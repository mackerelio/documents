---
Title: ホストのカスタムメトリックを投稿する
Date: 2014-04-30T19:37:29+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/custom-metrics
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815722995191
---

ホストごとのメトリックには「loadavg5」や「cpu.user」などエージェントが自動で投稿するメトリックに加えて、任意の値（カスタムメトリック）を定期的にエージェントから投稿できます。

後述の[sensu][]プラグイン互換のフォーマットで計測値を出力するコマンドを登録すると、その出力がデフォルトのメトリックと共にMackerelに送信され、別個のグラフに可視化されるようになります。

公式のプラグイン集も提供しています。公式プラグインの使い方などについては[公式プラグイン集を使う](https://mackerel.io/ja/docs/entry/howto/mackerel-agent-plugins)を参照してください。

また、公式プラグインで利用している、ヘルパーライブラリである、[github.com/mackerelio/go-mackerel-plugin](https://github.com/mackerelio/go-mackerel-plugin) を利用したプラグインの開発方法については、[go-mackerel-pluginを利用してカスタムメトリックプラグインを作成する](https://mackerel.io/ja/docs/entry/advanced/go-mackerel-plugin) をご覧ください。

<h2 id="setting">設定</h2>

[エージェントの設定ファイル](https://mackerel.io/ja/docs/entry/spec/agent#config-file)に、以下のような項目を追加します（例）:

```config
[plugin.metrics.vmstat]
command = [ "ruby", "/path/to/vmstat-metrics.rb" ]
user = "SOME_USER_NAME" # optional
custom_identifier = "SOME_IDENTIFIER" # optional
include_pattern = 'cpu' # optional
exclude_pattern = 'waiting$' # optional
timeout_seconds = 45 # optional
env = { SAMPLE_KEY = "VALUE" } # optional
```


1行目の `[]` で囲まれた設定項目名は "plugin.metrics." で始まっている必要があり、含まれるドットの数はちょうど2である必要があります。プラグインを複数指定する場合は、プラグインごとに別の名前を指定して下さい。

2行目以降の各設定項目の説明は以下の通りです。 `command` のみが必須の項目で、他の項目は全て optional です。

- command: エージェントが定期的に実行し、その標準出力を計測結果として使用するコマンドです。コマンドは<a href="#post-metric">後述する仕様</a>に沿って動作する必要があります。
    - `command` には文字列を渡すことも可能です。上記の例の場合、`command = "ruby /path/to/vmstat-metrics.rb"` のように指定できます。
        - 文字列を渡した場合、シェル経由での実行になり、エスケープが必要になります。
        - 配列を渡した場合、シェル経由での実行にならず、エスケープも不要になります。
- user: `command` で指定したコマンドの実行ユーザーを指定できます。指定しない場合は mackerel-agent の実行ユーザーがコマンドの実行ユーザーとなります。Windows環境には未対応のため、利用できません。
- custom_identifier: 実行結果のメトリックを、 agent が動作しているホストではなく指定した識別子のホストのメトリックとして送信します。
    - AWS / Azure / Google Cloud インテグレーションの連携ホストにメトリックを追加する場合などに有用です。詳細は[AWSインテグレーションのドキュメント](https://mackerel.io/ja/docs/entry/integrations/aws#plugin-custom-identifier)をご覧下さい。
- include_pattern / exclude_pattern: 実行結果のメトリックのうち特定のものだけを Mackerel に投稿するために正規表現を記述できます。
    - include_pattern が指定されていた場合、メトリック名が指定された正規表現にマッチするメトリックのみを投稿します。
    - exclude_pattern が指定されていた場合、メトリック名が指定された正規表現にマッチしないメトリックのみを投稿します。
    - include_pattern と exclude_pattern の両方にマッチするメトリック名の場合、そのメトリックは投稿されません。
- timeout_seconds: プラグインの処理におけるタイムアウト時間を秒で指定します。デフォルト値は30秒です。各プラグイン実行の多重起動の制御はされませんので、プラグインの実行間隔を超えないような設定をすることを推奨します。
- env: commandに渡す環境変数を指定できます。TOMLの[Table][]もしくは[Inline Table][]で指定します。

**注意: PowerShell スクリプトを利用する場合**

Windows PowerShell スクリプトの実行ポリシーによっては PowerShell スクリプトで書かれたプラグインが実行できないことがあります。
そのような場合、下記のような バッチファイルを用意してプラグインのコマンドに指定すると実行することが出来ます。

```bat
@echo off
cd %~dp0

powershell Set-ExecutionPolicy RemoteSigned
powershell .\sample-plugin.ps1
```

<h2 id="post-metric">メトリックの投稿</h2>

設定ファイルで指定するコマンドは、標準出力の各行に以下のフォーマットの出力をすることが期待されます（`\t` はタブ文字です）:

```
{metric name}\t{metric value}\t{epoch seconds}
```

- 名前の最後のドットまでが共通するメトリックがひとつのグラフにまとめられ、ホスト詳細で閲覧できます。
- メトリック名の先頭には自動的に"custom."という文字列が付与されます。
- メトリック名に使える文字は英数字もしくはハイフン（`-`）、アンダースコア（`_`）、ドット（`.`）のいずれか（`/[-a-zA-Z0-9_.]/`）です。

**例: `example.foo` と `example.bar` という名前のメトリックを投稿した場合**

`custom.example.*` と名付けられたグラフがホスト詳細に現れます。このグラフには `example.foo` と `example.bar` の系列データが描画されます。

[sensu]: http://sensuapp.org/

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
| `graphs.{graph}` | ユーザー定義メトリック {graph}.* に対応するグラフ名。{graph} にはドット（`.`）を含むことができます。また、ワイルドカード`#`, `*` を使用することもできます。詳しくは <a href="https://mackerel.io/ja/api-docs/entry/host-metrics#post-graphdef">API仕様（v0）/ グラフ定義の投稿</a> を参照してください。 |
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
