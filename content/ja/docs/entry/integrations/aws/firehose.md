---
Title: AWSインテグレーション - Kinesis Data Firehose
Date: 2019-07-16T10:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/firehose
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613376610645
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/kinesis/data-firehose/" target="_blank">Amazon Kinesis Data Firehose</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1デリバリーストリーム = 1マイクロホストと換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのKinesis Data Firehose対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/firehose/latest/dev/monitoring-with-cloudwatch-metrics.html" target="_blank">AWSのヘルプ</a>をご確認ください。

送信先に指定するサービス毎に、最大で以下のメトリックが取得されます。

- S3の場合：33個
- Elasticsearch Serviceの場合：29個
- Redshiftの場合：33個
- Splunkの場合：30個

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|Service Bytes|BackupToS3.Bytes<br>DeliveryToElasticsearch.Bytes<br>DeliveryToRedshift.Bytes<br>DeliveryToS3.Bytes<br>DeliveryToSplunk.Bytes<br>IncomingBytes<br>DataReadFromKinesisStream.Bytes|firehose.service_bytes.backup_to_s3<br>firehose.service_bytes.delivery_to_elasticsearch<br>firehose.service_bytes.delivery_to_redshift<br>firehose.service_bytes.delivery_to_s3<br>firehose.service_bytes.delivery_to_splunk<br>firehose.service_bytes.incoming<br>firehose.service_bytes.read_from_kinesis_streams|bytes|Sum|
|Service Records|BackupToS3.Records<br>DeliveryToElasticsearch.Records<br>DeliveryToRedshift.Records<br>DeliveryToS3.Records<br>DeliveryToSplunk.Records<br>IncomingRecords<br>DataReadFromKinesisStream.Records|firehose.service_records.backup_to_s3<br>firehose.service_records.delivery_to_elasticsearch<br>firehose.service_records.delivery_to_redshift<br>firehose.service_records.delivery_to_s3<br>firehose.service_records.delivery_to_splunk<br>firehose.service_records.incoming<br>firehose.service_records.read_from_kinesis_streams|integer|Sum|
|Service Success|BackupToS3.Success<br>DeliveryToElasticsearch.Success<br>DeliveryToRedshift.Success<br>DeliveryToS3.Success<br>DeliveryToSplunk.Success|firehose.service_success.backup_to_s3<br>firehose.service_success.delivery_to_elasticsearch<br>firehose.service_success.delivery_to_redshift<br>firehose.service_success.delivery_to_s3<br>firehose.service_success.delivery_to_splunk|float|Average|
|Service Data Freshness|BackupToS3.DataFreshness<br>DeliveryToElasticsearch.DataFreshness<br>DeliveryToRedshift.DataFreshness<br>DeliveryToS3.DataFreshness<br>DeliveryToSplunk.DataFreshness|firehose.service_data_freshness.backup_to_s3<br>firehose.service_data_freshness.delivery_to_elasticsearch<br>firehose.service_data_freshness.delivery_to_redshift<br>firehose.service_data_freshness.delivery_to_s3<br>firehose.service_data_freshness.delivery_to_splunk|float|Maximum|
|Service Data Ack Latency|DeliveryToSplunk.DataAckLatency|firehose.service_data_ack_latency.delivery_to_splunk|float|Maximum|
|Millis Behind Latest|KinesisMillisBehindLatest|firehose.millis_behind_latest.kinesis|integer|Sum|
|API Latency|DescribeDeliveryStream.Latency<br>PutRecord.Latency<br>PutRecordBatch.Latency<br>UpdateDeliveryStream.Latency|firehose.api_latency.describe_delivery_stream_average<br>firehose.api_latency.describe_delivery_stream_maximum<br>firehose.api_latency.put_record_average<br>firehose.api_latency.put_record_maximum<br>firehose.api_latency.put_record_batch_average<br>firehose.api_latency.put_record_batch_maximum<br>firehose.api_latency.update_delivery_stream_average<br>firehose.api_latency.update_delivery_stream_maximum|float|Average<br>Maximum|
|API Bytes|PutRecord.Bytes<br>PutRecordBatch.Bytes|firehose.api_bytes.put_record<br>firehose.api_bytes.put_record_batch|bytes|Sum|
|API Requests|DescribeDeliveryStream.Requests<br>PutRecord.Requests<br>PutRecordBatch.Requests<br>UpdateDeliveryStream.Requests|firehose.api_requests.describe_delivery_stream<br>firehose.api_requests.put_record<br>firehose.api_requests.put_record_batch<br>firehose.api_requests.update_delivery_stream|integer|Sum|
|API Records|PutRecordBatch.Records|firehose.api_records.put_record_batch|integer|Sum|
|API Throttled|ThrottledDescribeStream<br>ThrottledGetRecords<br>ThrottledGetShardIterator|firehose.api_throttled.describe_stream<br>firehose.api_throttled.get_records<br>firehose.api_throttled.get_shard_iterator|integer|Sum|
|Data Transformation Duration|ExecuteProcessing.Duration|firehose.data_transformation_duration|float|Average|
|Data Transformation Success|ExecuteProcessing.Success|firehose.data_transformation_success.execute|float|Average|
|Data Transformation Records|SucceedProcessing.Records|firehose.data_transformation_records.succeed|integer|Sum|
|Data Transformation Bytes|SucceedProcessing.Bytes|firehose.data_transformation_bytes.succeed|bytes|Sum|
|Format Conversion Records|SucceedConversion.Records<br>FailedConversion.Records|firehose.format_conversion_records.succeed<br>firehose.format_conversion_recordsfailed|integer|Sum|
|Format Conversion Bytes|SucceedConversion.Bytes<br>FailedConversion.Bytes|firehose.format_conversion_bytes.succeed<br>firehose.format_conversion_bytes.failed|bytes|Sum|
