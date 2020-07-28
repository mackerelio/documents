---
Title: AWSインテグレーション - SQS
Date: 2017-01-13T14:43:24+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/sqs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10328749687206320840
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/sqs/" target="_blank">Amazon Simple Queue Service (SQS)</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1キュー = 1マイクロホストと換算します。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのSQS対応で取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-available-cloudwatch-metrics.html" target="_blank">AWSのヘルプ</a>をご確認ください。

最大で11個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:---|:---|:---|:---|:---|
|Oldest Age|ApproximateAgeOfOldestMessage|sqs.oldest_message.age|integer|Maximum|
|Message State Count|ApproximateNumberOfMessagesDelayed<br>ApproximateNumberOfMessagesNotVisible<br>ApproximateNumberOfMessagesVisible|sqs.message_state.delayed<br>sqs.message_state.not_visible<br>sqs.message_state.visible|float|Average|
|Receive Count|NumberOfEmptyReceives<br>NumberOfMessagesReceived|sqs.receive_count.empty<br>sqs.receive_count.received|integer|Sum|
|Message Count|NumberOfMessagesDeleted<br>NumberOfMessagesSent|sqs.message_count.deleted<br>sqs.message_count.sent|integer|Sum|
|Sent Message Size|SentMessageSize<br>SentMessageSize<br>SentMessageSize|sqs.message_size.average<br>sqs.message_size.max<br>sqs.message_size.min|bytes|Average<br>Maximum<br>Minimum|

## 注意事項

AWSのAPI上の制限により、同じリージョン内に1000件以上のキューが存在する場合は全てのキューと連携することができません。
