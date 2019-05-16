---
Title: AWSインテグレーション - DynamoDB
Date: 2018-09-14T18:44:35+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/dynamodb
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10257846132630327670
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/dynamodb/" target="_blank">Amazon DynamoDB</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1テーブル = 1マイクロホストと換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。

[AWSインテグレーション](https://mackerel.io/ja/docs/entry/integrations/aws)

## 取得メトリック

AWSインテグレーションのDynamoDB対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/metrics-dimensions.html" target="_blank">AWSのヘルプ</a>をご確認ください。

最大で `13 + 6 × (行われるオペレーションの種類の数)` 個、ストリームが有効になっている場合はそれに加えて最大で10個のメトリックが取得されます。

### テーブルごとのメトリック

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|ReadCapacityUnits|ProvisionedReadCapacityUnits<br>ConsumedReadCapacityUnits|dynamodb.read_capacity_units.provisioned<br>dynamodb.read_capacity_units.consumed|float|Average<br>Sum|
|WriteCapacityUnits|ProvisionedWriteCapacityUnits<br>ConsumedWriteCapacityUnits|dynamodb.write_capacity_units.provisioned<br>dynamodb.write_capacity_units.consumed|float|Average<br>Sum|
|Requests|ConditionalCheckFailedRequests<br>SuccessfulRequestLatency<br>ThrottledRequests<br>UserErrors<br>SystemErrors|dynamodb.requests.conditional_check_failed_requests<br>dynamodb.requests.success_requests<br>dynamodb.requests.throttled_requests<br>dynamodb.requests.user_errors<br>dynamodb.requests.system_errors|integer|Sum<br>SampleCount<br>Sum<br>Sum<br>SampleCount|
|ThrottleEvents|ReadThrottleEvents<br>WriteThrottleEvents|dynamodb.throttle_events.read_throttle_events<br>dynamodb.throttle_events.write_throttle_events|integer|Sum<br>Sum|
|TimeToLiveDeletedItemCount|TimeToLiveDeletedItemCount|dynamodb.time_to_live_deleted_item_count.count|integer|Sum|
|SuccessfulRequestLatency|SuccessfulRequestLatency|dynamodb.successful_request_latency.#.minimum<br>dynamodb.successful_request_latency.#.average<br>dynamodb.successful_request_latency.#.maximum|float|Minimum<br>Average<br>Maximum|
|ReturnedItemCount|ReturnedItemCount|dynamodb.returned_item_count.#.minimum<br>dynamodb.returned_item_count.#.average<br>dynamodb.returned_item_count.#.maximum|float|Minimum<br>Average<br>Maximum|
|RequestCount|RequestCount|dynamodb.request_count.requests|integer|SampleCount|

- Requestsに表示されるメトリックの内、オペレーションごとに発生するメトリックは集約して表示します。
- "Mackerel上のメトリック" の # には、実際には UpdateItem のような DynamoDB のオペレーション名が入ります。
- SuccessfullRequestLatencyは以下のような2種類の情報が提供されます。
    - 成功したリクエストの経過時間（Minimum、Maximum、またはAverage）
    - 成功したリクエストの数（SampleCount）

### ストリームごとのメトリック
テーブルでストリームの設定を有効にしている場合、以下のメトリックを追加で収集します。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|Stream ReturnedBytes|ReturnedBytes|dynamodb.returned_bytes.GetRecords.minimum<br>dynamodb.returned_bytes.GetRecords.average<br>dynamodb.returned_bytes.GetRecords.maximum|bytes|Minimum<br>Average<br>Maximum|
|Stream ReturnedRecordsCount|ReturnedRecordsCount|dynamodb.returned_records_count.GetRecords.minimum<br>dynamodb.returned_records_count.GetRecords.average<br>dynamodb.returned_records_count.GetRecords.maximum|float|Minimum<br>Average<br>Maximum|
|Stream RequestCount|ReturnedRecordsCount|dynamodb.request_count_streams.GetRecords.requests|integer|SampleCount|
|Stream SuccessfulRequestLatency|SuccessfulRequestLatency|dynamodb.successful_request_latency_streams.GetRecords.minimum<br>dynamodb.successful_request_latency_streams.GetRecords.average<br>dynamodb.successful_request_latency_streams.GetRecords.maximum|float|Minimum<br>Average<br>Maximum|

<h2 id="notes">注意事項</h2>
AWSインテグレーションにより取得可能な上記のグラフ・メトリックのうち、下記のグラフに含まれるメトリックについては、5分間隔粒度でのメトリックが取得されます。

- ReadCapacityUnits
    - ProvisionedReadCapacityUnits
- WriteCapacityUnits
    - ProvisionedWriteCapacityUnits
