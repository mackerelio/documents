---
Title: メトリックプラグイン - mackerel-plugin-snmp
Date: 2022-11-21T18:10:43+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/mackerel-plugin-snmp
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889938595532
---

mackerel-plugin-snmp はSNMPによりネットワーク機器から任意の数値のMIB情報を取得し、メトリックとして監視できます。

本プラグインはSNMP v2cに対応しており、SNMP Trapには対応していません。

[:contents]

<h2 id="metrics">監視できるメトリック</h2>

- SNMP v2cで取得可能な任意の数値のMIB情報をメトリックとして投稿します。

<h2 id="options">指定可能なオプション</h2>

プラグインに指定可能なオプションは以下の通りです。

| オプション | 説明                                                                                                                                       | 初期値    |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| -name      | グラフ名                                                                                                                                   | snmp      |
| -unit      | グラフに属するメトリックの単位（指定可能な単位は[こちら](https://mackerel.io/ja/api-docs/entry/host-metrics#post-graphdef)の`unit`を参照） | float     |
| -host      | SNMPリクエストを送るホスト                                                                                                                 | localhost |
| -community | コミュニティ名                                                                                                                             | public    |
| -tempfile  | 一時ファイルの保存先                                                                                                                       |           |

取得するメトリックの定義は `OID:NAME[:DIFF?][:STACK?][:COUNTER?]` のように半角コロン(`:`)区切りで定義します。各項目はそれぞれ以下の通りです。

| 項目  | 説明                                                        | 必須 | 初期値 |
| ----- | ----------------------------------------------------------- | ---- | ------ |
| OID   | MIB値を取得するOID（※MIB名での指定はできません）            | ◯    |        |
| NAME  | メトリック名                                                | ◯    |        |
| DIFF  | カウンター値として差分表示するか（`0:しない/1:する`で指定） |      | 0      |
| STACK | 積み上げ表示とするか（`0:しない/1:する`で指定）             |      | 0      |
| COUNTER | カウンターのビット数を uint32 もしくは uint64 で指定            |      |       |

COUNTER の指定について

- 監視対象のカウンターがオーバーフローした場合、前回との差分が正しく計算できず、グラフ描画の連続性が損なわれてしまうため、特に 32 ビットのカウンターを監視対象にする場合は指定することを推奨します。

<h2 id="config">エージェントへの設定例</h2>

プラグインはオプション、メトリック定義の順で指定する必要があります。

コミュニティが`public`で、OIDが`.1.2.3.4.5.6.7`のMIBを、グラフ名`foo`に`bar`というメトリック名で、差分/積み上げ表示をともに`しない`投稿する設定は以下のようになります。

```toml
[plugin.metrics.snmp]
command = ["mackerel-plugin-snmp", "-name", "foo", "-community", "public", ".1.2.3.4.5.6.7:bar:0:0"]
```

同じグラフに複数のメトリックを投稿する場合、メトリックの定義を複数並べることで対応できます。

```toml
[plugin.metrics.snmp]
command = ["mackerel-plugin-snmp", "-name", "foo", "-community", "public", ".1.2.3.4.5.6.7:bar:0:0", ".1.2.3.4.5.6.8:fuga:0:0"]
```

上記設定をターミナルなどから直接実行して確認するには、以下のように実行してください。

```
mackerel-plugin-snmp -name foo -community public ".1.2.3.4.5.6.7:bar:0:0" ".1.2.3.4.5.6.8:fuga:0:0"
```

<h2 id="troubleshoot">トラブルシューティング</h2>

### メトリックが取得できない

メトリックが取得できない場合、OIDの指定が誤っている可能性があるため、snmpgetコマンドでOIDが参照可能かご確認ください。

また、OID配下にツリーが含まれるものは監視できないため、snmpwalkコマンドではなく必ずsnmpgetコマンドによる事前確認をお試しください。

### command timed out が発生する

SNMPによる疎通不可もしくはレスポンスに時間がかかっている可能性があります。
snmpgetコマンドでOIDが正常に参照可能かご確認ください。

また、プラグインのオプション指定に誤りがある場合に正しく通信が行えず、タイムアウトとなる場合があります。
[エージェントへの設定例](#エージェントへの設定例)を参考に、正しくオプションが指定されているかご確認ください。

<h2 id="repository">リポジトリ</h2>

https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-snmp
