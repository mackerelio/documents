---
Title: AWSインテグレーション - Connect
Date: 2021-03-09T10:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/connect
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613703992932
CustomPath: integrations/aws/connect
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/connect/" target="_blank">Amazon Connect</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1インスタンス = 1マイクロホストと換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのConnect対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/connect/latest/adminguide/monitoring-cloudwatch.html" target="_blank">AWSのヘルプ</a>をご確認ください。

最大で `11 + 3 × (問い合わせフロー数) + 5 × (キュー数) + 1 × (署名キー)` のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|Concurrent Calls Service Quota|ConcurrentCallsPercentage|connect.concurrent_calls_service_quota.quota|percentage|Maximum|
|To Instance Packet Loss Rate|ToInstancePacketLossRate|connect.to_instance_packet_loss_rate.minimum<br>connect.to_instance_packet_loss_rate.average<br>connect.to_instance_packet_loss_rate.maximum|float|Minimum<br>Average<br>Maximum|
|Voice Calls|CallsBreachingConcurrencyQuota<br>CallsPerInterval<br>ConcurrentCalls<br>ThrottledCalls<br>MissedCalls<br>MisconfiguredPhoneNumbers|connect.voice_calls.breaching_concurrency_quota<br>connect.voice_calls.calls_per_interval<br>connect.voice_calls.concurrent_calls<br>connect.voice_calls.throttled<br>connect.voice_calls.missed<br>connect.voice_calls.misconfigured_phone_numbers|integer|Sum<br>Sum<br>Maximum<br>Sum<br>Sum<br>Sum|
|Call Recording Upload|CallRecordingUploadError|connect.call_recording_upload.error|integer|Sum|
|Contact Flows|ContactFlowErrors<br>ContactFlowFatalErrors<br>CallBackNotDialableNumber|connect.contact_flows.CONTACT_FLOW_NAME.errors<br>connect.contact_flows.CONTACT_FLOW_NAME.fatal_errors<br>connect.contact_flows.CONTACT_FLOW_NAME.call_back_not_dialable|integer|Sum|
|Queues|QueueSize<br>QueueCapacityExceededError|connect.queue.QUEUE_NAME.size<br>connect.queue.QUEUE_NAME.capacity_exceeded_error|integer|Maximum<br>Sum|
|Queue Wait Time|LongestQueueWaitTime|connect.queue_wait_time.QUEUE_NAME.minimum<br>connect.queue_wait_time.QUEUE_NAME.average<br>connect.queue_wait_time.QUEUE_NAME.maximum|float|Minimum<br>Average<br>Maximum|
|Public Signing Key|PublicSigningKeyUsage|connect.public_signing_key.SIGNING_KEY_ID|integer|Sum|

- "Mackerel上のメトリック名"の以下の項目は次の通り設定されます。
    - `CONTACT_FLOW_NAME`：問い合わせフローの名前
    - `QUEUE_NAME`：キューの名前
    - `SIGNING_KEY_ID`：署名キーID

<h2 id="notes">注意事項</h2>

- AWSインテグレーションのConnect対応ではタグによるホストの絞り込みに対応していません。
- ASCII以外の文字が問い合わせフローの名前、またはキューの名前に含まれる場合、CloudWatchにメトリックが発生しません。これは、AWSの仕様です。
