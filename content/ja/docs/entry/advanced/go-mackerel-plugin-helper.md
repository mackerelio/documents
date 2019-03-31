---
Title: go-mackerel-plugin-helperを利用してカスタムメトリックプラグインを作成する
Date: 2016-03-21T23:29:11+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/go-mackerel-plugin-helper
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10328537792368016809
---

**現在、プラグイン作成には、ここで紹介している[go-mackerel-plugin-helper](https://github.com/mackerelio/go-mackerel-plugin-helper)よりも、[go-mackerel-plugin](https://github.com/mackerelio/go-mackerel-plugin)を利用することを推奨しています。go-mackerel-pluginを利用したプラグイン作成方法の詳細は https://mackerel.io/ja/docs/entry/advanced/go-mackerel-plugin をご覧ください。**

公式プラグインでも利用しているGo言語でのカスタムメトリックプラグイン作成用のヘルパーライブラリである、[github.com/mackerelio/go-mackerel-plugin-helper](https://github.com/mackerelio/go-mackerel-plugin-helper) を利用したプラグインの開発方法を説明します。

このヘルパーの作法に従って開発することで、グラフ定義の出力や、前回取得した値との差分値計算などを簡単に行えます。また、このヘルパーを使えば、自ずと公式プラグインと同じ作法でプラグインを書くことになるので、公式プラグインへのp-rを検討している場合などは、とくにこのヘルパーを使うことを推奨します。

## go-mackerel-plugin-helper を利用時の構成

go-mackerel-plugin-helperを利用した場合、プラグインのソースコードは以下の5つの部分で構成されます。

1. package宣言とimport文
2. プラグイン用 struct の定義
3. グラフ定義出力メソッド `GraphDefinition` をstructに定義
4. メトリック取得用メソッド `FetchMetrics` をstruct定義
5. `main()` 関数 の定義

ここでは、 [mackerel-agent-uptime](https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-uptime) を例にとってそれぞれを見ていくことにしましょう。

### 1. package宣言とimport文

```go
package main

import (
    "flag"
    "fmt"
    "strings"

    mp "github.com/mackerelio/go-mackerel-plugin-helper"
    "github.com/mackerelio/golib/uptime"
)
```

`package` は `main` で宣言します。また、go-mackerel-plugin-helper は `mp` というエイリアスでインポートすることが慣例となっています。

### 2. プラグイン用 struct の定義

```go
type UptimePlugin struct {
    Prefix string
}
```

プラグイン用の struct を定義しています。このstructには `Prefix` というフィールドが定義されています。これは、グラフ定義出力時に、そのメトリックの名前空間の先頭を決めるためのものです。uptimeプラグインの標準では `Prefix` は `uptime` であり、 `uptime.seconds` というキーでメトリックを出力しますが、この中の `uptime` を例えば `uptime2` に変更したい場合に利用されるフィールドです。

uptimeプラグインでは `Prefix` フィールドは特に有益ではありません。ただ、例えばミドルウェア用のプラグインの場合、1台のホストの中で、同じミドルウェアを複数起動して、それぞれのメトリックを取得したい場合に、メトリックの名前空間を分ける必要が出てくるので、このフィールドを定義しておくことが推奨されています。

uptimeプラグインでは、この `Prefix` フィールドのみが定義されていますが、一般的なミドルウェアのプラグインであれば、`Port` や `Host` といったフィールドも必要になるでしょう。

また、このプラグイン用 struct は `mp.Plugin` の `interface` を満たす必要があります。 `interface` の定義は以下のようになっています。

```go
type Plugin interface {
    GraphDefinition() map[string]Graphs
    FetchMetrics() (map[string]interface{}, error)
}
```

これらが、グラフ定義出力メソッドと、メトリック取得用メソッドです。

### 3. グラフ定義出力メソッド `GraphDefinition` をstructに定義

```go
func (u UptimePlugin) GraphDefinition() map[string](mp.Graphs) {
    labelPrefix := strings.Title(u.Prefix)
    return map[string](mp.Graphs){
        u.Prefix: mp.Graphs{
            Label: labelPrefix,
            Unit:  "float",
            Metrics: [](mp.Metrics){
                mp.Metrics{Name: "seconds", Label: "Seconds"},
            },
        },
    }
}
```

この、 `GraphDefinition` を定義することで、グラフ定義のJSONや、メトリックが正しく出力されるようになります。uptimeプラグインでは、 `u.Prefix` をキーとした一つのグラフ定義しか返していませんが、多くのプラグインは、 `u.Prefix + "runtime"`、`u.Prefix + "memory"` と言ったようなキーで複数のグラフ定義を返します。

`Label` はMackerel上で表示されるグラフ名、`Unit` はグラフの単位で、グラフ定義API同様に"float", "integer", "percentage", "bytes", "bytes/sec", "iops"が指定可能となっています。

`Metrics` にはそのグラフ内に描画する複数の `Metrics` 定義を指定します。 `mp.Metrics` に指定できるフィールドは以下のとおりです。

| フィールド | 型      | 説明  |
| ---------- | ------  | ----- |
| Name       | string  | (必須)メトリックの名前。 `FetchMetrics()` で取得する map のキー名と対応する |
| Label      | string  | Mackerel上での表示名                                                      |
| Diff       | bool    | plugin上で差分値計算をするかどうか (Default: false)                              |
| Type       | strint  | "float64", "uint32" もしくは "uint64"。主に整数値のカウンターでDiff計算が必要な場合に上限値の決定のために用いられる (Default: float64) |
| Stacked    | bool    | Mackerel上で積み上げ表示されるかどうか (Default: false)                          |
| Scale      | float64 | 指定された場合、取得した値にこのScaleの値を乗じてから出力をおこなう。例えば、KBで取得した値をByteに補正したい場合に、1024を指定する |

### 4. メトリック取得用メソッド `FetchMetrics` をstruct定義

```go
func (u UptimePlugin) FetchMetrics() (map[string]interface{}, error) {
    ut, err := uptime.Get()
    if err != nil {
        return nil, fmt.Errorf("Failed to fetch uptime metrics: %s", err)
    }
    return map[string]interface{}{"seconds": ut}, nil
}
```

`Fetchmetrics()` は `map[string]interface{}` の形式で値を返します。 `interface{}` になってますが、実際は何らかの数値です。`uint64`と`float64`を統一的に扱うためにこのようになっています。

ここでは、 `seconds` をキーに uptimeの値が格納されたmapを返しています。

### 5. `main()` 関数 の定義

```go
func main() {
    optPrefix := flag.String("metric-key-prefix", "uptime", "Metric key prefix")
    optTempfile := flag.String("tempfile", "", "Temp file name")
    flag.Parse()
    
    u := UptimePlugin{
        Prefix: *optPrefix,
    }
    helper := mp.NewMackerelPlugin(u)
    helper.Tempfile = *optTempfile
    if helper.Tempfile == "" {
        helper.Tempfile = fmt.Sprintf("/tmp/mackerel-plugin-%s", *optPrefix)
    }
    helper.Run()
}
```

プラグインのメインの処理です。コマンドラインオプションのパース、プラグインヘルパーオブジェクトの作成、そしてプラグインの実行をおこなっています。

`helper` に指定されている `Tempfile` は、差分値計算用に前回取得した値を保持しておくためのファイルです。他のプラグインや、同じプラグインであっても引数が異なる複数の設定がある場合などに重複しないように指定する必要があるので、気をつけてください。

---

go-mackerel-plugin-helper を利用した、カスタムメトリックプラグインの作成方法を説明しました。ここの説明はあくまで基本的なものなので、好きなパッケージを組み込んだりして、是非独自のプラグインを作成してみてください。有益なプラグインで他のユーザーにも役立ちそうなものができたら、是非 [公式リポジトリ](https://github.com/mackerelio/mackerel-agent-plugins) にp-rを送ってみてください。
