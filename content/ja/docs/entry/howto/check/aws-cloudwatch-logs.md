---
Title: Amazon CloudWatch Logsの監視をおこなう
Date: 2018-09-25T18:36:01+09:00
URL: https://mackerel.io/ja/docs/entry/howto/check/aws-cloudwatch-logs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10257846132639276802
---

公式チェックプラグイン集の `check-aws-cloudwatch-logs` を使うと、[Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html)のログデータの監視をおこなえます。公式チェックプラグイン集のインストールは、[チェック監視に公式チェックプラグイン集を使う](https://mackerel.io/ja/docs/entry/howto/mackerel-check-plugins) をごらんください。

## `check-aws-cloudwatch-logs` の使い方

`check-aws-cloudwatch-logs`を使ってCloudWatch Logsの監視をおこなうためには、例えば以下のように mackerel-agent.conf に記述し、mackerel-agentを再起動します。

```config
[plugin.checks.aws-cloudwatch-logs-sample]
command = ["check-aws-cloudwatch-logs", "--log-group-name", "/aws/lambda/sample_log_group", "--pattern", "Error", "--critical-over", "5", "--warning-over", "3"]
env = { AWS_REGION = "ap-northeast-1" }
```

以下のオプションを指定できます。

- `--log-group-name` (必須): ロググループ名を指定します。
- `--log-stream-name-prefix`: ストリーム名をプレフィックスで絞り込みます。
- `--pattern` (必須): 監視を行うための検索文字列。[フィルタとパターンの構文](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/logs/FilterAndPatternSyntax.html)を参照してください。
- `--warning-over`: 設定値より多くのメッセージが検出された場合にwarningにする。
- `--critical-over`: 設定値より多くのメッセージが検出された場合にcriticalにする。
- `--return`: 検出したメッセージをMackerelに送信します。秘匿情報が含まれないよう注意してください。メッセージの量が大きい場合は表示が切り詰められることがあります。

その他のオプションに関しては `check-aws-cloudwatch-logs --help` をご確認ください。

## 認証と必要なポリシーについて
EC2のインスタンスから監視する場合、インスタンスプロファイルが認証に使われます。プラグインの実行のためには `logs:FilterLogEvents` アクションが必要なので、ポリシーをアタッチしてください。
インスタンスプロファイルを用いる以外の方法としては、`AWS_PROFILE`を設定する方法と、`AWS_ACCESS_KEY_ID`・`AWS_SECRET_ACCESS_KEY`を設定する方法があります。プラグインの設定の`env`にて指定してください。

## `--pattern`の設定値について
ログメッセージを絞り込むための`--pattern`オプションには、Amazon CloudWatch Logsの[フィルタとパターンの構文](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/logs/FilterAndPatternSyntax.html)に記述されている構文を指定できます。正規表現ではないこと、複数指定することはできないことに注意してください。

## Unknown: command timed out について
流量の多いログストリームや、ストリーム数の多いロググループでは、プラグインの実行がタイムアウトすることがあります。

プラグインの設定で `timeout_seconds` を指定することで、プラグインの実行のタイムアウトを伸ばせます。ただし、実行間隔 (デフォルトでは60秒) を超えないように指定する必要があります。詳しくは[チェック監視項目を追加する](https://mackerel.io/ja/docs/entry/custom-checks)を参照してください。

ログの量が多い場合、定期実行しているプラグインがログを収集しきれないのは構造上避けられない状況です。ログの量を減らしたり、サンプリングするなどの対策が考えられます。

## ソースコードについて

`check-aws-cloudwatch-logs`のソースコードは以下に公開されています。

<https://github.com/mackerelio/go-check-plugins/tree/master/check-aws-cloudwatch-logs>
