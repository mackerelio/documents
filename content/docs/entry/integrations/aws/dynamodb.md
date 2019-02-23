---
Title: AWS Integration - DynamoDB
Date: 2018-09-21T17:22:28+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/dynamodb
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10257846132636302674
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/dynamodb/" target="_blank">Amazon DynamoDB</a> metrics in AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 Table = 1 Host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services.  <br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

## Obtaining metrics

The metrics obtainable with AWS Integration’s DynamoDB support are as follows. For `Metric` explanations, refer to the <a href="https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/metrics-dimensions.html" target="_blank">AWS help page</a>.

### Metrics per table

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:--|:--|:--|:--|:--|
|ReadCapacityUnits|ProvisionedReadCapacityUnits<br>ConsumedReadCapacityUnits|dynamodb.read_capacity_units.provisioned<br>dynamodb.read_capacity_units.consumed|float|Average<br>Sum|
|WriteCapacityUnits|ProvisionedWriteCapacityUnits<br>ConsumedWriteCapacityUnits|dynamodb.write_capacity_units.provisioned<br>dynamodb.write_capacity_units.consumed|float|Average<br>Sum|
|Requests|ConditionalCheckFailedRequests<br>SuccessfulRequestLatency<br>ThrottledRequests<br>UserErrors<br>SystemErrors|dynamodb.requests.conditional_check_failed_requests<br>dynamodb.requests.success_requests<br>dynamodb.requests.throttled_requests<br>dynamodb.requests.user_errors<br>dynamodb.requests.system_errors|integer|Sum<br>SampleCount<br>Sum<br>Sum<br>SampleCount|
|ThrottleEvents|ReadThrottleEvents<br>WriteThrottleEvents|dynamodb.throttle_events.read_throttle_events<br>dynamodb.throttle_events.write_throttle_events|integer|Sum<br>Sum|
|TimeToLiveDeletedItemCount|TimeToLiveDeletedItemCount|dynamodb.time_to_live_deleted_item_count.count|integer|Sum|
|SuccessfulRequestLatency|SuccessfulRequestLatency|dynamodb.successful_request_latency.#.minimum<br>dynamodb.successful_request_latency.#.average<br>dynamodb.successful_request_latency.#.maximum|float|Minimum<br>Average<br>Maximum|
|ReturnedItemCount|ReturnedItemCount|dynamodb.returned_item_count.#.minimum<br>dynamodb.returned_item_count.#.average<br>dynamodb.returned_item_count.#.maximum|float|Minimum<br>Average<br>Maximum|
|RequestCount|RequestCount|dynamodb.request_count.requests|integer|SampleCount|

- Among the metrics displayed in Requests,  metrics generated for each operation are aggregated and displayed.
- Similar to UpdateItem, the DynamoDB operation name is actually included in the # of "Mackerel metrics".
- SuccessfullRequestLatency provides the following two types of information.
    - The elapsed time of successful request（Minimum, Maximum, or Average）
    - The number of successful requests（SampleCount）

### Metrics per stream
In addition to the above, the following metrics can be collected when a DynamoDB Stream is enabled for the target table.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:--|:--|:--|:--|:--|
|Stream ReturnedBytes|ReturnedBytes|dynamodb.returned_bytes.GetRecords.minimum<br>dynamodb.returned_bytes.GetRecords.average<br>dynamodb.returned_bytes.GetRecords.maximum|bytes|Minimum<br>Average<br>Maximum|
|Stream ReturnedRecordsCount|ReturnedRecordsCount|dynamodb.returned_records_count.GetRecords.minimum<br>dynamodb.returned_records_count.GetRecords.average<br>dynamodb.returned_records_count.GetRecords.maximum|float|Minimum<br>Average<br>Maximum|
|Stream RequestCount|ReturnedRecordsCount|dynamodb.request_count_streams.GetRecords.requests|integer|SampleCount|
|Stream SuccessfulRequestLatency|SuccessfulRequestLatency|dynamodb.successful_request_latency_streams.GetRecords.minimum<br>dynamodb.successful_request_latency_streams.GetRecords.average<br>dynamodb.successful_request_latency_streams.GetRecords.maximum|float|Minimum<br>Average<br>Maximum|

<h2 id="notes">Precautions</h2>
Among the graphs/metrics obtainable with AWS Integration, metrics included in the following graphs are obtained in 5 minute intervals.

- ReadCapacityUnits
    - ProvisionedReadCapacityUnits
- WriteCapacityUnits
    - ProvisionedWriteCapacityUnits
