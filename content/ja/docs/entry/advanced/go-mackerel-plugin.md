---
Title: go-mackerel-pluginを利用してカスタムメトリックプラグインを作成する
Date: 2017-10-16T17:38:52+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/go-mackerel-plugin
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8599973812308530764
---

公式プラグインでも利用しているGo言語でのカスタムメトリックプラグイン作成用のライブラリである、[github.com/mackerelio/go-mackerel-plugin](https://github.com/mackerelio/go-mackerel-plugin) を利用したプラグインの開発方法を説明します。

このライブラリの作法に従って開発することで、グラフ定義の出力や、前回取得した値との差分値計算などを簡単に行えます。また、このライブラリを使えば、自ずと公式プラグインと同じ作法でプラグインを書くことになるので、公式プラグインへのPull Requestを検討している場合などは、とくにこのライブラリを使うことを推奨します。

## go-mackerel-plugin を利用時の構成

go-mackerel-pluginを利用した場合、プラグインのソースコードは以下の5つの部分で構成されます。

1. package宣言とimport文
2. プラグイン用 struct の定義
3. グラフ定義出力メソッド `GraphDefinition` をstructに定義
4. メトリック取得用メソッド `FetchMetrics` をstruct定義
5. メトリックキーのプレフィックス取得用メソッド `MetricKeyPrefix` をstruct定義
6. `main()` 関数 の定義

ここでは、 [mackerel-agent-uptime](https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-uptime) を例にとってそれぞれを見ていくことにしましょう。

### 1. package宣言とimport文

```go
package main

import (
    "flag"
    "fmt"
    "strings"

    mp "github.com/mackerelio/go-mackerel-plugin"
    "github.com/mackerelio/go-osstat/uptime"
)
```

`package` は `main` で宣言します。また、go-mackerel-plugin は `mp` というエイリアスでインポートすることが慣例となっています。

### 2. プラグイン用 struct の定義

```go
type UptimePlugin struct {
    Prefix string
}
```

プラグイン用の struct を定義しています。このstructには `Prefix` というフィールドが定義されています。これは、グラフ定義出力時に、そのメトリックの名前空間の先頭を決めるためのものです。uptimeプラグインの標準では `Prefix` は `uptime` であり、 `uptime.seconds` というキーでメトリックを出力しますが、この中の `uptime` を例えば `uptime2` に変更したい場合に利用されるフィールドです。

uptimeプラグインでは `Prefix` フィールドは特に有益ではありません。ただ、例えばミドルウェア用のプラグインの場合、1台のホストの中で、同じミドルウェアを複数起動して、それぞれのメトリックを取得したい場合に、メトリックの名前空間を分ける必要が出てくるので、このフィールドを定義しておくことが推奨されています。

uptimeプラグインでは、この `Prefix` フィールドのみが定義されていますが、一般的なミドルウェアのプラグインであれば、`Port` や `Host` といったフィールドも必要になるでしょう。

また、このプラグイン用 struct は `mp.PluginWithPrefix` の `interface` を満たす必要があります。 `interface` の定義は以下のようになっています。

```go
type PluginWithPrefix interface {
    FetchMetrics() (map[string]float64, error)
    GraphDefinition() map[string]mp.Graphs
    MetricKeyPrefix() string
}
```

これらが、グラフ定義出力メソッド、メトリック取得用メソッド、メトリックのプレフィックス取得用メソッドです。

### 3. グラフ定義出力メソッド `GraphDefinition` をstructに定義
```go
func (u UptimePlugin) GraphDefinition() map[string]mp.Graphs {
    labelPrefix := strings.Title(u.MetricKeyPrefix())
    return map[string]mp.Graphs{
        "": {
            Label: labelPrefix,
            Unit:  mp.UnitFloat,
            Metrics: []mp.Metrics{
                {Name: "seconds", Label: "Seconds"},
            },
        },
    }
}
```

この `GraphDefinition` を定義することで、グラフ定義のJSONや、メトリックが正しく出力されるようになります。uptimeプラグインでは、 空文字をキーとした一つのグラフ定義しか返していませんが、多くのプラグインは、 `"runtime"`、`"memory"` と言ったようなキーで複数のグラフ定義を返します。

実際のグラフ定義出力時には、この定義のキーの前に `MetricKeyPrefix()` が返す値が付けられます。例えば `MetricKeyPrefix()` がuptimeを返す場合、`""` -> `"uptime"`, `"runtime"` -> `"uptime.runtime"`のように出力されます。

`Label` はMackerel上で表示されるグラフ名、`Unit` はグラフの単位で、グラフ定義API同様に"float", "integer", "percentage", "seconds", "milliseconds", "bytes", "bytes/sec", "bits/sec", "iops"が指定可能となっています。

`Metrics` にはそのグラフ内に描画する複数の `Metrics` 定義を指定します。 `mp.Metrics` に指定できるフィールドは以下のとおりです。

| フィールド | 型      | 説明  |
| ---------- | ------  | ----- |
| Name       | string  | (必須)メトリックの名前。 `FetchMetrics()` で取得する map のキー名と対応する |
| Label      | string  | Mackerel上での表示名                                                      |
| Diff       | bool    | plugin上で差分値計算をするかどうか (Default: false)                              |
| Stacked    | bool    | Mackerel上で積み上げ表示されるかどうか (Default: false)                          |
| Scale      | float64 | 指定された場合、取得した値にこのScaleの値を乗じてから出力をおこなう。例えば、KBで取得した値をByteに補正したい場合に、1024を指定する |

### 4. メトリック取得用メソッド `FetchMetrics` をstruct定義

```go
func (u UptimePlugin) FetchMetrics() (map[string]float64, error) {
    ut, err := uptime.Get()
    if err != nil {
        return nil, fmt.Errorf("Failed to fetch uptime metrics: %s", err)
    }
    return map[string]float64{"seconds": ut.Seconds()}, nil
}
```

`Fetchmetrics()` は `map[string]float64` の形式で値を返します。ここでは、 `seconds` をキーに uptimeの値が格納されたmapを返しています。 `seconds` は前述の `GraphDefinition` の `Name` に指定した名前です。

### 5. メトリックのプレフィックス取得用メソッド `MetricKeyPrefix` をstruct定義

```go
func (u UptimePlugin) MetricKeyPrefix() string {
    if u.Prefix == "" {
        u.Prefix = "uptime"
    }
    return u.Prefix
}
```

メトリックのプレフィックス取得用メソッドを定義します。プラグイン利用者がプレフィックスを指定していたらそれを、指定していなければデフォルトで `"uptime"` を返すようにしています。

### 6. `main()` 関数 の定義

```go
func main() {
    optPrefix := flag.String("metric-key-prefix", "uptime", "Metric key prefix")
    optTempfile := flag.String("tempfile", "", "Temp file name")
    flag.Parse()

    u := UptimePlugin{
        Prefix: *optPrefix,
    }
    plugin := mp.NewMackerelPlugin(u)
    plugin.Tempfile = *optTempfile
    plugin.Run()
}
```

プラグインのメインの処理です。コマンドラインオプションのパース、プラグインオブジェクトの作成、そしてプラグインの実行をおこなっています。

`plugin` に指定されている `Tempfile` は、差分値計算用に前回取得した値を保持しておくためのファイルです。他のプラグインや、同じプラグインであっても引数が異なる複数の設定がある場合などに重複しないように指定する必要があるので、気をつけてください。

---

go-mackerel-plugin を利用した、カスタムメトリックプラグインの作成方法を説明しました。ここの説明はあくまで基本的なものなので、好きなパッケージを組み込んだりして、是非独自のプラグインを作成してみてください。

有益なプラグインで他のユーザーにも役立ちそうなものができたら、さらに[mkr plugin installに対応したプラグインを作成する](https://mackerel.io/ja/docs/entry/advanced/make-plugin-corresponding-to-installer)のドキュメントに従って、`mkr plugin install`対応した上で、[プラグインレジストリ](https://github.com/mackerelio/plugin-registry)へ登録してみてください。

## 関連ドキュメント
- [mkr plugin installでプラグインをインストールする](https://mackerel.io/ja/docs/entry/advanced/install-plugin-by-mkr)
- [mkr plugin installに対応したプラグインを作成する](https://mackerel.io/ja/docs/entry/advanced/make-plugin-corresponding-to-installer)
