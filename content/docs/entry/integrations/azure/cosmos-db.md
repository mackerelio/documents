---
Title: Azure Integration - Cosmos DB
Date: 2026-07-07T16:12:22+09:00
URL: https://mackerel.io/docs/entry/integrations/azure/cosmos-db
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/14945776032050944714
CustomPath: integrations/azure/cosmos-db
---

Mackerel supports obtaining and monitoring <a href="https://azure.microsoft.com/en-us/products/cosmos-db" target="_blank">Azure Cosmos DB</a> metrics in Azure Integration. When integrating with Azure Integration, billable targets are determined using the conversion 1 database account = 1 Micro Host. In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for Azure Integration configuration methods and a list of supported Azure services.<br>
<a href="https://mackerel.io/docs/entry/integrations/azure">Azure Integration</a>

## Obtaining metrics
The metrics obtainable with Azure Integration's Cosmos DB support are as follows. For `Metric` explanations, refer to the <a href="https://learn.microsoft.com/en-us/azure/azure-monitor/reference/supported-metrics/microsoft-documentdb-databaseaccounts-metrics" target="_blank">Azure help page</a>.

The maximum number of metrics obtainable is `1 + 28 × (number of databases) + 6 × (number of database and collection name pairs) + (number of source and target region pairs for replication latency)`.

The metric name's `#` will contain the database name. For Document Count, Document Quota, Provisioned Throughput, Autoscaled RU, Autoscale Max Throughput, and Normalized RU Consumption, `*` will contain the collection name. For Total Requests, `*` will contain the status code. For P99 Replication Latency, `#` will contain the source region and `*` will contain the target region.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
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
