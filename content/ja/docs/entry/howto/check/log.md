---
Title: ログ監視をおこなう
Date: 2015-11-05T12:11:14+09:00
URL: https://mackerel.io/ja/docs/entry/howto/check/log
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653458415126986600
---

Mackerelでは公式チェックプラグインを利用することで、以下のようなログを監視できます。

- ログファイル
- Windowsのイベントログ
- Amazon CloudWatch Logsのログ

このページでは、チェックプラグインの導入からログの種類に応じたシンプルな監視設定までをご案内します。詳細な設定については、別途利用するプラグインのヘルプをご確認ください。  

[:contents]

## ログ監視の始め方

### 1. 公式チェックプラグイン集をインストールする

[チェック監視に公式チェックプラグイン集を使う](https://mackerel.io/ja/docs/entry/howto/mackerel-check-plugins) を参照し、公式チェックプラグイン集を導入します。

### 2. チェック監視の設定を追加する

ログ監視の対象となるサーバの mackerel-agent.conf の末尾に、監視したいログに合わせたチェック監視の設定をおこないます。
追加する設定は、以下のようなフォーマットになっています。

```toml
[plugin.checks.<監視ルール名>]
command = ["実行したいコマンド", "引数", "引数"...]
```

追記が完了したら、mackerel-agent を再起動（Linux の場合は reload でも可）することで、監視が開始されます。  
設定が正常に反映された場合、設定で指定した監視ルール名が対象ホストの Monitors に表示されます。

<figure class="figure-image figure-image-fotolife" title="ホスト詳細画面の Monitors に log-sample や aws-cloudwatch-logs-sample という名前のログ監視設定が表示されている様子">[f:id:mackerelio:20251024231326p:plain]<figcaption>ホスト詳細画面の Monitors に log-sample や aws-cloudwatch-logs-sample という名前のログ監視設定が表示されている様子</figcaption></figure>

設定後は、監視対象のログにエラー文字列を追記してアラートが発報するかといった動作確認をおすすめいたします。

以下に監視したいログ別の設定例を示します。

#### ログファイルの監視（check-log）

以下は、`log-sample` という監視ルール名で、`/var/log/messages` に `ERROR` が出力されたらCRITICALアラートを発報する例です。

```toml
[plugin.checks.log-sample]
command = ["check-log", "--file", "/var/log/messages", "--pattern", "ERROR"]
```

<figure class="figure-image figure-image-fotolife" title="上記設定にてアラートが発生した例">[f:id:mackerelio:20251024230354p:plain]<figcaption>上記設定にてアラートが発生した例</figcaption></figure>

そのほか監視目的に合わせて、パターンにマッチしたログの件数をアラートの条件にしたり、複数のファイルを対象に監視したりといった設定も可能です。
詳しくは [チェックプラグイン - check-log - Mackerel ヘルプ](https://mackerel.io/ja/docs/entry/plugins/check-log) をご確認ください。

#### Windowsのイベントログの監視（check-windows-eventlog）

Windowsのイベントログはcheck-windows-eventlogプラグインを利用することで監視できます。  

以下は、`windows-eventlog-sample` という監視ルール名で、`Application` ログに、イベントレベルが `Error` でイベントのメッセージに `ERROR` という文字列が含まれていたらアラートを発報する例です。

```toml
[plugin.checks.windows-eventlog-sample]
command = ["check-windows-eventlog", "--log", "Application", "--type", "Error", "--message-pattern", "ERROR"]
```

上記設定でアラートが発生した際は、`Event Log CRITICAL: 0 warnings, 1 criticals.` のようなログがMackerelのアラート詳細画面に表示されます。


そのほか、Systemログを監視対象にしたり、特定のイベントIDやイベントソースを対象にしたりもできます。詳しくは [check-windows-eventlogのヘルプ](https://mackerel.io/ja/docs/entry/plugins/check-windows-eventlog) をご確認ください。

#### Amazon CloudWatch Logsの監視（check-aws-cloudwatch-log）

Amazon CloudWatch Logsのログは、check-aws-cloudwatch-logsプラグインを利用して監視できます。検出したい文字列の条件にはAmazon CloudWatch Logsのフィルターパターンを利用します。

以下は、`aws-cloudwatch-logs-sample` という監視ルール名で、ロググループ `/aws/lambda/sample_log_group` に `Error` を含む文字列が出力されたらCRITICALアラートを発報する例です。

```toml
[plugin.checks.aws-cloudwatch-logs-sample]
command = ["check-aws-cloudwatch-logs", "--log-group-name", "/aws/lambda/sample_log_group", "--pattern", "Error"]
```

上記設定でアラートが発生した際は、`CloudWatch Logs CRITICAL: 1 > 0 messages for pattern /Error/ ` のようなログがアラート詳細画面に表示されます。

そのほか、対象のログストリームをプレフィックスで絞り込んだり、パターンにマッチしたログの件数をアラートの条件にしたりもできます。
詳しくは [チェックプラグイン - check-aws-cloudwatch-logs - Mackerel ヘルプ](https://mackerel.io/ja/docs/entry/plugins/check-aws-cloudwatch-logs) をご確認ください。

また、別途 check-aws-cloudwatch-logs-insightsプラグインを導入することで、CloudWatch Logs Insights の構文を利用した監視が可能です。  
check-aws-cloudwatch-logsプラグインとの違いや、導入方法については、[チェックプラグイン - check-aws-cloudwatch-logs-insights - Mackerel ヘルプ](https://mackerel.io/ja/docs/entry/plugins/check-aws-cloudwatch-logs-insights)をご確認ください。

### 3. チェック監視の動作を変更する

必要に応じて、チェック監視の実行間隔や、何回連続で異常を検知したらアラート発報するかなどの設定が可能です。
以下はデフォルトでは実行間隔が1分のチェック監視を10分間隔に変更し、さらに3回実行した結果がすべて異常だった場合にアラートを発報する例です。

```toml
[plugin.checks.messages_error]
command = ["check-log", "--file", "/var/log/messages", "--pattern", "ERROR"]
check_interval = 10m
max_check_attempts = 3
```

そのほか、チェック監視に設定可能な項目については、[チェック監視項目を追加する - Mackerel ヘルプ](https://mackerel.io/ja/docs/entry/custom-checks#items) をご参照ください。

## 実験的提供プラグインを利用した監視

公式チェックプラグイン集には同梱されていませんが、Mackerelではそのほかのログ監視の用途に向けたプラグインを[Mackerel Labs](https://github.com/mackerelio-labs)にて実験提供しています。

cloudwatch-logs-aggregatorでは、Amazon CloudWatch Logsのログを集計し、メトリックとして投稿できます。

- [Amazon CloudWatch Logs のログを集計して Mackerel にメトリックを投稿する - Mackerel ヘルプ](https://mackerel.io/ja/docs/entry/advanced/cloudwatch-logs-aggregator)
- [cloudwatch-logs-aggregator で Amazon CloudWatch Logs のログのメトリック化を実践する](https://mackerel.io/ja/blog/entry/cloudwatch-logs-aggregator)

mackerel-sql-metric-collectorでは、MySQL や Athena などさまざまなデータベースへのクエリ結果をメトリックとして投稿できます。

- [mackerel-sql-metric-collector - README](https://github.com/mackerelio-labs/mackerel-sql-metric-collector/blob/main/README_ja.md)
- [各種のデータベースから自由にクエリした結果をMackerelに投稿できるツール「mackerel-sql-metric-collector」を紹介します](https://mackerel.io/ja/blog/entry/meetup14-7)

check-systemd-journalでは、journaldに投稿されたログを対象に監視できます。

- [mackerelio-labs/check-systemd-journal](https://github.com/mackerelio-labs/check-systemd-journal?tab=readme-ov-file#%E8%AA%AC%E6%98%8E)

Mackerel Labsで公開されているソフトウェアは実験的なものであるため、サポートの対象外となります。お問い合わせをいただいても基本的には対応いたしかねますのであらかじめご理解のほどお願いいたします。  
