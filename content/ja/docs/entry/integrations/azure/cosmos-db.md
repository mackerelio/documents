---
Title: Azureインテグレーション - Cosmos DB
Date: 2026-07-07T16:13:13+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/azure/cosmos-db
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/14945776032050944965
CustomPath: integrations/azure/cosmos-db
---

MackerelはAzureインテグレーションにて<a href="https://azure.microsoft.com/ja-jp/products/cosmos-db" target="_blank">Azure Cosmos DB</a>のメトリック取得や監視に対応しています。Azureインテグレーションで連携をおこなった場合、課金対象として 1データベースアカウント = 1マイクロホスト と換算します。また、取得されるメトリック数に応じて、1マイクロホストあたりのメトリック数上限を超過した分が請求対象になることがあります。

Azureインテグレーションの設定方法や対応Azureサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/azure">Azureインテグレーション</a>

## 取得メトリック
AzureインテグレーションのCosmos DB対応で取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://learn.microsoft.com/ja-jp/azure/azure-monitor/reference/supported-metrics/microsoft-documentdb-databaseaccounts-metrics" target="_blank">Azureのドキュメント</a>をご確認ください。

最大で以下の数のメトリックが取得されます。

`1 + 28 × (データベース数) + 6 × (データベース名とコレクション名の組み合わせ数) + (レプリケーション遅延の取得対象となるソースリージョンとターゲットリージョンの組み合わせ数)`

メトリック名の `#` にはデータベース名が入ります。以下のメトリックでは `*` にコレクション名が入ります。

- Document Count
- Document Quota
- Provisioned Throughput
- Autoscaled RU
- Autoscale Max Throughput
- Normalized RU Consumption

Total Requestsでは `*` にステータスコードが入ります。P99 Replication Latencyでは `#` にソースリージョン、`*` にターゲットリージョンが入ります。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
|:---|:---|:---|:---|:---|
|Total Requests|TotalRequests|azure.cosmos_db.total_requests.#.*|integer|Count|
|Total Request Units|TotalRequestUnits|azure.cosmos_db.total_request_units.#.total|integer|Total|
|Provisioned Throughput|ProvisionedThroughput|azure.cosmos_db.provisioned_throughput.#.*|integer|Maximum|
|Autoscaled RU|AutoscaledRU|azure.cosmos_db.autoscaled_ru.#.*|integer|Maximum|
|Autoscale Max Throughput|AutoscaleMaxThroughput|azure.cosmos_db.autoscale_max_throughput.#.*|integer|Maximum|
|Normalized RU Consumption|NormalizedRUConsumption|azure.cosmos_db.normalized_ru_consumption.#.*|percentage|Maximum|
|Server Side Latency Direct|ServerSideLatencyDirect|azure.cosmos_db.server_side_latency_direct.#.average<br>azure.cosmos_db.server_side_latency_direct.#.max|milliseconds|Average<br>Maximum|
|Server Side Latency Gateway|ServerSideLatencyGateway|azure.cosmos_db.server_side_latency_gateway.#.average<br>azure.cosmos_db.server_side_latency_gateway.#.max|milliseconds|Average<br>Maximum|
|Data Usage|DataUsage|azure.cosmos_db.data_usage.#.total|bytes|Total|
|Index Usage|IndexUsage|azure.cosmos_db.index_usage.#.total|bytes|Total|
|Document Count|DocumentCount|azure.cosmos_db.document_count.#.*|integer|Total|
|Document Quota|DocumentQuota|azure.cosmos_db.document_quota.#.*|bytes|Total|
|Service Availability|ServiceAvailability|azure.cosmos_db.service_availability.average|percentage|Average|
|Metadata Requests|MetadataRequests|azure.cosmos_db.metadata_requests.#.count|integer|Count|
|P99 Replication Latency|ReplicationLatency|azure.cosmos_db.replication_latency.#.*|milliseconds|Maximum|
|Physical Partition Count|PhysicalPartitionCount|azure.cosmos_db.physical_partition_count.#.max|integer|Maximum|
|Physical Partition Size|PhysicalPartitionSizeInfo|azure.cosmos_db.physical_partition_size.#.max|bytes|Maximum|
|Physical Partition Throughput|PhysicalPartitionThroughputInfo|azure.cosmos_db.physical_partition_throughput.#.max|integer|Maximum|
