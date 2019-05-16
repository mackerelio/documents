---
Title: AWSインテグレーション - Kinesis Data Streams
Date: 2018-11-09T18:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/kinesis
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10257846132669009781
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/kinesis/data-streams/" target="_blank">Amazon Kinesis Data Streams</a>のメトリック取得や監視に対応しています。
AWSインテグレーションで連携を行なった場合、課金対象として1ストリーム = 1マイクロホストと換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのKinesis Data Streams対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/streams/latest/dev/monitoring-with-cloudwatch.html" target="_blank">AWSのヘルプ</a>をご確認ください。

最大で31個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|Bytes|GetRecords.Bytes<br>IncomingBytes<br>PutRecord.Bytes<br>PutRecords.Bytes|kinesis.bytes.get_records<br>kinesis.bytes.incoming<br>kinesis.bytes.put_record<br>kinesis.bytes.put_records|bytes|Sum|
|Records|GetRecords.Records<br>IncomingRecords<br>PutRecords.Records|kinesis.records.get_records<br>kinesis.records.incoming<br>kinesis.records.put_records|integer|Sum|
|Delay|GetRecords.IteratorAgeMilliseconds|kinesis.delay.minimum<br>kinesis.delay.average<br>kinesis.delay.maximum|float|Minimum<br>Average<br>Maximum|
|Success Rate|GetRecords.Success<br>PutRecord.Success<br>PutRecords.Success|kinesis.success_rate.get_records<br>kinesis.success_rate.put_record<br>kinesis.success_rate.put_records|float|Average|
|Success Count|GetRecords.Success<br>PutRecord.Success<br>PutRecords.Success|kinesis.success_count.get_records<br>kinesis.success_count.put_record<br>kinesis.success_count.put_records|integer|Sum|
|Provisioned  Throughput Exceeded|ReadProvisionedThroughputExceeded<br>WriteProvisionedThroughputExceeded|kinesis.provisioned_throughput_exceeded.#.minimum<br>kinesis.provisioned_throughput_exceeded.#.average<br>kinesis.provisioned_throughput_exceeded.#.maximum|float|Minimum<br>Average<br>Maximum|
|Latency|GetRecords.Latency<br>PutRecord.Latency<br>PutRecords.Latency|kinesis.latency.#.minimum<br>kinesis.latency.#.average<br>kinesis.latency.#.maximum|float|Minimum<br>Average<br>Maximum|

- "Mackerel上のメトリック名"の#には、[Provisioned Throughput Exceeded]では"Read"、"Write"のいずれかが、[Latency]では"GetRecords"、"PutRecord"、"PutRecords"のいずれかが入ります。
