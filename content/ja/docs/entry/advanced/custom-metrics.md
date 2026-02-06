---
Title: ホストのカスタムメトリックを投稿する
Date: 2014-04-30T19:37:29+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/custom-metrics
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815722995191
---

ホストごとのメトリックには「loadavg5」や「cpu.user」などエージェントが自動で投稿するメトリックに加えて、任意の値（カスタムメトリック）を定期的にエージェントから投稿できます。

[メトリックの投稿](#post-metric)に記載のフォーマットで標準出力するコマンドを登録すると、その出力がデフォルトのメトリックと共にMackerelに送信され、別個のグラフに可視化されるようになります。

公式のプラグイン集も提供しています。公式プラグインの使い方などについては[公式プラグイン集を使う](https://mackerel.io/ja/docs/entry/howto/mackerel-agent-plugins)を参照してください。

また、公式プラグインで利用している、ヘルパーライブラリである、[github.com/mackerelio/go-mackerel-plugin](https://github.com/mackerelio/go-mackerel-plugin) を利用したプラグインの開発方法については、[go-mackerel-pluginを利用してカスタムメトリックプラグインを作成する](https://mackerel.io/ja/docs/entry/advanced/go-mackerel-plugin) をご覧ください。

<h2 id="post-metric">メトリックの投稿</h2>

設定ファイルで指定するコマンドは、標準出力の各行に以下のフォーマットの出力をすることが期待されます（`\t` はタブ文字です）:

```
{metric name}\t{metric value}\t{epoch seconds}
```

- `{metric name}`：メトリック名
  - メトリック名に使える文字は英数字もしくはハイフン（`-`）、アンダースコア（`_`）、ドット（`.`）のいずれかです。
    - 正規表現：`/^[-a-zA-Z0-9_.]+$/`
  - メトリック名の先頭には自動的に `custom.` という文字列が付与されます。
  - メトリック名の最後のドットまでが共通する場合、ひとつのグラフにまとめられます。
    - 例：`example.foo` と `example.bar` という名前のメトリックを投稿した場合、`custom.example.*` という名前のグラフに描画されます。
- `{metric value}`：メトリックの値
  - 投稿する値を出力します。
- `{epoch seconds}`：メトリックのタイムスタンプ
  - エポック秒で指定します。
  - 現在より24時間過去、もしくは未来の日時を指定した場合は投稿されません。

**出力例**：`example.foo` という名前のメトリックを投稿する場合

```
example.foo\t100\t1769544774
```

<h2 id="setting">エージェントへの設定例</h2>

[エージェントの設定ファイル](https://mackerel.io/ja/docs/entry/spec/agent#config-file)に、以下のような項目を追加します。以下は`vmstat-metrics.rb`というプログラムを使用した監視の設定例です。各項目の意味は[設定項目](#items)を参照してください。必須項目以外は必要に応じて設定してください。

```toml
[plugin.metrics.vmstat]
command = [ "ruby", "/path/to/vmstat-metrics.rb" ]
timeout_seconds = 45
custom_identifier = "SOME_IDENTIFIER"
include_pattern = 'cpu'
exclude_pattern = 'waiting$'
user = "SOME_USER_NAME"
env = { SAMPLE_KEY = "VALUE" }
```

<h3 id="items">設定項目</h3>

| 設定項目             | 必須 | 説明                                                                                                                                                                                                                                                                 | デフォルト値 |
|----------------------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------|
| [plugin.metrics.XXX] | ◯    | 1行目の [] で囲まれた設定項目名は "plugin.metrics." で始まっている必要があり、含まれるドットの数はちょうど2である必要があります。プラグインを複数指定する場合は、プラグインごとに別の名前で設定してください。                                                                                                                        |         |
| command              | ◯    | エージェントが定期的に実行し、その標準出力を計測結果として使用するコマンドです。コマンドは[メトリックの投稿](#post-metric)に記載の仕様に沿って動作する必要があります。*1                                                                                                                                              |         |
| timeout_seconds      |      | プラグインの処理におけるタイムアウト時間を秒で指定します。                                                                                                                                                                                                                                 | 30 秒   |
| custom_identifier    |      | 実行結果のメトリックを、mackerel-agent が動作しているホストではなく、指定した識別子のホストに投稿します。<br>AWS / Azure / Google Cloud インテグレーションの連携ホストにメトリックを追加する場合などに有用です。詳細は[AWSインテグレーションのドキュメント](https://mackerel.io/ja/docs/entry/integrations/aws#plugin-custom-identifier)をご覧ください。 |         |
| include_pattern      |      | メトリック名が指定された正規表現にマッチするメトリックのみを投稿します。*2                                                                                                                                                                                                                      |         |
| exclude_pattern      |      | メトリック名が指定された正規表現にマッチしないメトリックのみを投稿します。*2                                                                                                                                                                                                                     |         |
| user                 |      | `command` で指定したコマンドの実行ユーザーを指定できます。指定しない場合は mackerel-agent の実行ユーザーがコマンドの実行ユーザーとなります。Windows環境には未対応のため、利用できません。                                                                                                                                   |         |
| env                  |      | commandに渡す環境変数を指定できます。                                                                                                        |         |

- *1 `command` には文字列を渡すことも可能です。上記の例の場合、`command = "ruby /path/to/vmstat-metrics.rb"` のように指定できます。文字列を渡した場合、シェル経由での実行になります。
- *2 `include_pattern` と `exclude_pattern` の両方にマッチするメトリック名の場合、そのメトリックは投稿されません。

**注意: PowerShell スクリプトを利用する場合**

Windows PowerShell スクリプトの実行ポリシーによっては PowerShell スクリプトで書かれたプラグインが実行できないことがあります。
そのような場合、下記のような バッチファイルを用意してプラグインのコマンドに指定すると実行することが出来ます。

```bat
@echo off
cd %~dp0

powershell Set-ExecutionPolicy RemoteSigned
powershell .\sample-plugin.ps1
```

<h2 id="graph-schema">グラフ定義を指定する</h2>

ユーザー定義メトリックを投稿することで作られるグラフの表示設定を、実行されるコマンドから JSON で指定できます。

mackerel-agent は起動時に、設定ファイルで指定されたコマンドを `MACKEREL_AGENT_PLUGIN_META` 環境変数を "1" に設定した状態で起動します。この時、当該コマンドの出力が以下のフォーマットに従っていると、mackerel-agent はプラグインのメタ情報として解釈し、グラフ定義を Mackerel に送信します。これによって、ユーザー定義メトリックの表示設定をウェブ上で行わずにあらかじめ指定できます。

出力は、必ず以下の行から始めてください。最初の行がこの内容でなかった場合、mackerel-agent はこのプラグインがメタ情報を出力しないものとして、グラフ定義の設定を行いません。

```
# mackerel-agent-plugin
```

続けて、以下のような設定を JSON フォーマットで出力します。

```
{
  "graphs": {
    {graph}: {
      "label": GRAPH_LABEL,
      "unit": UNIT_TYPE
      "metrics": [
        {
          "name": METRIC_NAME,
          "label": METRIC_LABEL
        },
        ...
      ]
    },
    GRAPH_NAME: ...
  }
}
…
```

それぞれの項目は、以下のような意味を持ちます。

| 項目 | 説明 |
| ---- | ---- |
| `graphs.{graph}` | ユーザー定義メトリック {graph}.* に対応するグラフ名。{graph} にはドット（`.`）を含むことができます。また、ワイルドカード`#`, `*` を使用することもできます。詳しくは <a href="https://mackerel.io/ja/api-docs/entry/host-metrics#post-graphdef">API仕様（v0）/ グラフ定義の投稿</a> を参照してください。<br>**注意**：ワイルドカードを含むカスタムメトリックのグラフは、一定時間（およそ6〜8時間以上）送信がない場合に、自動的に削除されます。 |
| `graphs.{graph}.label` | ユーザー定義メトリック {graph}.* に対応するグラフの表示名。 |
| `graphs.{graph}.unit`  | ユーザー定義メトリック {graph}.* に対応するグラフの値の種類。可能な値は "float", "integer", "percentage", "seconds", "milliseconds", "bytes", "bytes/sec", "bits/sec", "iops" のいずれか。 |
| `graphs.{graph}.metrics` | ユーザー定義メトリック {graph}.* に対応するメトリック定義の配列。 |

メトリック定義は以下のようなキーを持ちます。

| キー | 説明 |
| ---- | ---- |
| `name`  | このメトリックがユーザー定義メトリック {graph}.{name} に対応することを表す。この値にドット（`.`）を含むことはできません。使用できる文字は英数字もしくはハイフン（`-`）、アンダースコア（`_`）のいずれか（`/[-a-zA-Z0-9_]/`）です。 |
| `label`  | ユーザー定義メトリック {graph}.{name} に対応する時系列の表示名。 |
| `stacked`  | ユーザー定義メトリック {graph}.{name} に対応する時系列を積み上げ表示するかどうか。false なら線分で表示する。 |

<h3 id="graph-schema-example-ruby">Ruby によるサンプル</h3>

6面ダイスおよび20面ダイスの値を Mackerel に投稿するプラグインです。

```ruby
if ENV['MACKEREL_AGENT_PLUGIN_META'] == '1'
  require 'json'

  meta = {
    :graphs => {
      'super.dice' => {
        :label   => 'My Dice',
        :unit    => 'integer',
        :metrics => [
          {
            :name  => 'd6',
            :label => 'Die (d6)'
          }, {
            :name  => 'd20',
            :label => 'Die (d20)'
          }
        ]
      }
    }
  }

  puts '# mackerel-agent-plugin'
  puts meta.to_json
  exit 0
end

puts [ 'super.dice.d6',  rand( 6)+1, Time.now.to_i ].join("\t")
puts [ 'super.dice.d20', rand(20)+1, Time.now.to_i ].join("\t")
```

このプラグインで、以下のようなグラフを生成できます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20171017/20171017113656.png" alt="f:id:mackerelio:20171017113656p:image" title="f:id:mackerelio:20171017113656p:image" class="hatena-fotolife" itemprop="image"></span></p>

[Table]: https://github.com/toml-lang/toml#table
[Inline Table]: https://github.com/toml-lang/toml#inline-table
