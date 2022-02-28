---
Title: AWS Integration - Kinesis Data Streams
Date: 2018-11-13T12:33:10+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/kinesis
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10257846132669015144
---

Mackerel supports obtaining and monitoring the metrics of <a href="https://aws.amazon.com/kinesis/data-streams/" target="_blank">Amazon Kinesis Data Streams</a> with AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 stream = 1 Micro Host.
In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services.<br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

## Obtaining metrics
The metrics obtainable with AWS Integration's support for Kinesis Data Streams are as follows. For `Metric` explanations, refer to the <a href="https://docs.aws.amazon.com/streams/latest/dev/monitoring-with-cloudwatch.html" target="_blank">AWS help page</a>.

The maximum number of metrics obtainable is 34.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:--|:--|:--|:--|:--|
|Bytes|GetRecords.Bytes<br>IncomingBytes<br>PutRecord.Bytes<br>PutRecords.Bytes|kinesis.bytes.get_records<br>kinesis.bytes.incoming<br>kinesis.bytes.put_record<br>kinesis.bytes.put_records|bytes|Sum|
|Records|GetRecords.Records<br>IncomingRecords<br>PutRecords.SuccessfulRecords<br>PutRecords.TotalRecords<br>PutRecords.FailedRecords<br>PutRecords.ThrottledRecords|kinesis.records.get_records<br>kinesis.records.incoming<br>kinesis.records.put_records_successful<br>kinesis.records.put_records_total<br>kinesis.records.put_records_failed<br>kinesis.records.put_records_throttled|integer|Sum|
|Delay|GetRecords.IteratorAgeMilliseconds|kinesis.delay.minimum<br>kinesis.delay.average<br>kinesis.delay.maximum|float|Minimum<br>Average<br>Maximum|
|Success Rate|GetRecords.Success<br>PutRecord.Success<br>PutRecords.Success|kinesis.success_rate.get_records<br>kinesis.success_rate.put_record<br>kinesis.success_rate.put_records|float|Average|
|Success Count|GetRecords.Success<br>PutRecord.Success<br>PutRecords.Success|kinesis.success_count.get_records<br>kinesis.success_count.put_record<br>kinesis.success_count.put_records|integer|Sum|
|Provisioned  Throughput Exceeded|ReadProvisionedThroughputExceeded<br>WriteProvisionedThroughputExceeded|kinesis.provisioned_throughput_exceeded.#.minimum<br>kinesis.provisioned_throughput_exceeded.#.average<br>kinesis.provisioned_throughput_exceeded.#.maximum|float|Minimum<br>Average<br>Maximum|
|Latency|GetRecords.Latency<br>PutRecord.Latency<br>PutRecords.Latency|kinesis.latency.#.minimum<br>kinesis.latency.#.average<br>kinesis.latency.#.maximum|float|Minimum<br>Average<br>Maximum|

- Enter either "Read" or "Write" for the # in [Provisioned Throughput Exceeded] under "Metric name in Mackerel". Likewise, either "GetRecords", "PutRecord", or "PutRecords" for the # in [Latency].

<h2 id="notes">Notes</h2>

Assigning roles by tags is not available with AWS Integration Kinesis Data Streams support.
