---
Title: AWS Integration - Kinesis Data Firehose
Date: 2019-07-16T10:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/firehose
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613376612825
---

Mackerel supports obtaining and monitoring the metric of <a href="https://aws.amazon.com/en/kinesis/data-firehose/" target="_blank">Amazon Kinesis Data Firehose</a> in AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 delivery stream = 1 Micro Host. In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services.<br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

## Obtaining metrics
The metrics obtainable with AWS Integration's support for Kinesis Data Firehose are as follows. For `Metric` explanations, refer to <a href="https://docs.aws.amazon.com/firehose/latest/dev/monitoring-with-cloudwatch-metrics.html" target="_blank">the AWS help page</a>.

The maximum number of metrics obtainable is detemined by destination services:

- In S3 cases：33
- In Elasticsearch Service cases：29
- In Redshift cases：33
- In Splunk cases：30

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
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
