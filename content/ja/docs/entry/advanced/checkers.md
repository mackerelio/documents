---
Title: checkersを利用してチェックプラグインを作成する
Date: 2016-04-18T16:11:06+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/checkers
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10328537792371624855
---

公式のチェックプラグインは、[github.com/mackerelio/checkers](https://github.com/mackerelio/checkers) というユーティリティライブラリを利用して作成されています。また、コマンドライン引数のパースに [github.com/jessevdk/go-flags](https://github.com/jessevdk/go-flags) を統一的に利用しています。

ここでは、 checkers を用いたチェックプラグインの作成方法を説明します。

## チェックプラグインのソースコード構成

checkersを利用した場合、プラグインのソースコードは以下の4つの部分で構成されます。

1. package宣言とimport文
2. コマンドライン引数用の `struct` の定義
3. `main()` 関数 の定義
4. `run()` 関数の実装

ここでは、 [check-uptime](https://github.com/mackerelio/go-check-plugins/tree/master/check-uptime) を例にとってそれぞれを見ていくことにしましょう。

## 1. package宣言とimport文

```go
package main

import (
    "fmt"
    "os"
    "time"

    "github.com/jessevdk/go-flags"
    "github.com/mackerelio/checkers"
    "github.com/mackerelio/golib/uptime"
)
```

そのまま実行コマンドとなるので`package` は `main` で宣言します。その他必要なライブラリのimportをおこないます。

## 2. コマンドライン引数用の `struct` の定義

```go
var opts struct {
    WarnUnder *float64 `short:"w" long:"warning-under" value-name:"N" description:"Trigger a warning if under the seconds"`
    CritUnder *float64 `short:"c" long:"critical-under" value-name:"N" description:"Trigger a critial if under the seconds"`
    WarnOver  *float64 `short:"W" long:"warning-over" value-name:"N" description:"Trigger a warning if over the seconds"`
    CritOver  *float64 `short:"C" long:"critical-over" value-name:"N" description:"Trigger a critical if over the seconds"`
}
```

プラグインが受け取るコマンドライン引数の設定をおこないます。この設定方法は、 github.com/jessevdk/go-flags の仕様に準じますので、詳しくは、[go-flagsのドキュメント](https://godoc.org/github.com/jessevdk/go-flags)をごらんください。

コマンドライン引数では、閾値の設定やミドルウェアのポートなどを指定します。`check-time` の場合、 uptimeが一定の時間を下回っていた場合、上回っていた場合それぞれのために、 `warning-under`, `critical-under`, `warning-over`, `critical-over` という4つのオプションの設定を可能にしています。

## 3. `main()` 関数の定義

```go
func main() {
    ckr := run(os.Args[1:])
    ckr.Name = "Uptime"
    ckr.Exit()
}
```

`main()` 関数は、コマンドのエントリーポイントですが、3行だけと非常にシンプルです。`run()` 関数により、 `*checkers.Checker` オブジェクトを受け取り、それに `Name` の設定を行い、終了しています。`Name` は、プラグイン実行時に先頭に表示される単語を統一的に指定するためのものです。例えば、check-uptime を実行すると以下の様な出力が表示されますが、この出力の先頭の "Uptime" がそれです。

```
% check-uptime
Uptime OK: 0 day(s) 3 hour(s) 17 minute(s) (11877 second(s))
```

## 4. `run()` 関数の実装

```go
func run(args []string) *checkers.Checker {
    _, err := flags.ParseArgs(&opts, args) // <- (1) コマンドライン引数の処理
    if err != nil {
        os.Exit(1)
    }
    ut, err := uptime.Get() // <- (2) uptimeの取得
    if err != nil {
        return checkers.Unknown(fmt.Sprintf("Failed to fetch uptime metrics: %s", err))
    }

    // (3) 閾値を比較と終了ステータスの決定
    checkSt := checkers.OK
    if opts.WarnUnder != nil && *opts.WarnUnder > ut {
        checkSt = checkers.WARNING
    }
    if opts.WarnOver != nil && *opts.WarnOver < ut {
        checkSt = checkers.WARNING
    }
    if opts.CritUnder != nil && *opts.CritUnder > ut {
        checkSt = checkers.CRITICAL
    }
    if opts.CritOver != nil && *opts.CritOver < ut {
        checkSt = checkers.CRITICAL
    }

    // (4) 出力メッセージの組み立て
    dur := time.Duration(ut * float64(time.Second))
    hours := int64(dur.Hours())
    days := hours / 24
    hours = hours % 24
    mins := int64(dur.Minutes()) % 60
    msg := fmt.Sprintf("%d day(s) %d hour(s) %d minute(s) (%d second(s))\n", days, hours, mins, int64(dur.Seconds()))
    return checkers.NewChecker(checkSt, msg)
}
```

`check-uptime` の処理はほとんどこの `run()` 関数の中で実装されています。 `run()` 関数のシグネチャは `func run(args []string) *checkers.Checker` となっています。これは、コマンドライン引数を受け取り、監視結果オブジェクトである `*checkers.Checker` を返すということです。

コメントで補足していますが、この `run()` 関数もおおまかに以下の4つの部分に分けられます。

1. コマンドライン引数の処理
2. uptimeの取得
3. 閾値比較と終了ステータスの決定
4. 出力メッセージの組み立て

### コマンドライン引数の処理

コマンドライン引数の処理では、`flags.ParseArgs()` によりコマンドライン引数の内容が `opt` にマッピングされます。

### uptimeの取得

github.com/mackerelio/golib/uptime パッケージの `uptime.Get()` を利用してuptimeを取得しています。

### 閾値比較と終了ステータスの決定

`opt` 内の閾値と比較しながら、終了ステータスの決定をおこないます。終了ステータスには、 `checkers.OK`/`WARNING`/`CRITICAL`/`UNKNOWN` 定数を用います。

### 出力メッセージの組み立て

出力メッセージを組み立て、最後に `*checkers.Checker` オブジェクトを返しています。ここでは `return checkers.NewChecker(checkSt, msg)` のようにして、`return` していますが、終了ステータスが決まっている場合、`return checkers.Critical("message")` と簡潔に書く機能も checkers には備わっているので、状況に応じて使い分けてください。

これで一通りチェックプラグインの作成方法を説明しました。皆さんも是非チャレンジしてみてください。
