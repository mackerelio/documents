---
Title: Azure Integration - Azure Files
Date: 2020-12-21T09:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/azure/azure-files
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613681092471
---

Mackerel supports obtaining and monitoring <a href="https://azure.microsoft.com/en-us/services/storage/files/" target="_blank">Azure Files</a> metrics in Azure Integration. Billable targets are determined using the conversion 1 File = 1 Micro Host. Depending on the number of metrics retrieved, additional charges may be incurred for exceeding the maximum number of metrics per micro host.

Please refer to the following page for Azure Integration configuration methods and a list of supported Azure services.<br>
<a href="https://mackerel.io/docs/entry/integrations/azure">Azure Integration</a>

## Obtaining metrics
The metrics available with Azure Integration's Azure Files support vary by peformance tier. The metrics that can be obtained at each tier are as follows. For `Metric` explanations, please refer to the <a href="https://docs.microsoft.com/en-us/azure/storage/files/storage-files-monitoring-reference" target="_blank">Azure documentation</a>.

### Standard
The maximum number of obtainable metrics can be calculated using the formula 1 + 3 * (number of operation types) + 2 * (number of authentication types) + 5 * (number of target Azure Files file shares) + 2 (if Secondary is enabled) + 1 * (number of response types)[^1].

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|Availability (ApiName)|Availability|azure.files.availability_apiname.#.percent|percentage|Average|
|Availability (Authentication)|Availability|azure.files.availability_authentication.#.percent|percentage|Average|
|File Capacity|FileCapacity|azure.files.file_capacity.#.bytes|bytes|Average|
|File Count|FileCount|azure.files.file_count.#.count|float|Average|
|File Share Capacity Quota|FileShareCapacityQuota|azure.files.file_share_capacity_quota.#.bytes|bytes|Average|
|File Share Count|FileShareCount|azure.files.file_share_count.count|float|Average|
|File Share Snapshot Count|FileShareSnapshotCount|azure.files.file_share_snapshot_count.#.count|float|Average|
|File Share Snapshot Size|FileShareSnapshotSize|azure.files.file_share_snapshot_size.#.bytes|bytes|Average|
|Ingress/Egress|Ingress<br>Egress|azure.files.ingress_egress.#.ingress<br>azure.files.ingress_egress.#.egress|bytes|Total|
|Success E2E Latency|SuccessE2ELatency|azure.files.success_e2e_latency.#.milliseconds|float|Average|
|Success Server Latency|SuccessServerLatency|azure.files.success_server_latency.#.milliseconds|float|Average|
|Transactions (Authentication)|Transactions|azure.files.transactions_authentication.#.count|integer|Total|
|Transactions (ResponseType)|Transactions|azure.files.transactions_responsetype.#.count|integer|Total|

### Premium
The maximum number of obtainable metrics can be calculated using the formula 1 + 3 * (number of operation types) + 2 * (number of authentication types) + 6 * (number of target Azure Files file shares) + 2 (if Secondary is enabled) + 1 * (number of response types)[^1].

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|Availability (ApiName)|Availability|azure.files.availability_apiname.#.percent|percentage|Average|
|Availability (Authentication)|Availability|azure.files.availability_authentication.#.percent|percentage|Average|
|File Capacity|FileCapacity|azure.files.file_capacity.#.bytes|bytes|Average|
|File Count|FileCount|azure.files.file_count.#.count|float|Average|
|File Share Capacity Quota|FileShareCapacityQuota|azure.files.file_share_capacity_quota.#.bytes|bytes|Average|
|File Share Count|FileShareCount|azure.files.file_share_count.count|float|Average|
|File Share Provisioned IOPS|FileShareProvisionedIOPS|azure.files.file_share_provisioned_iops.#.bytes|bytes|Average|
|File Share Snapshot Count|FileShareSnapshotCount|azure.files.file_share_snapshot_count.#.count|float|Average|
|File Share Snapshot Size|FileShareSnapshotSize|azure.files.file_share_snapshot_size.#.bytes|bytes|Average|
|Ingress/Egress|Ingress<br>Egress|azure.files.ingress_egress.#.ingress<br>azure.files.ingress_egress.#.egress|bytes|Total|
|Success E2E Latency|SuccessE2ELatency|azure.files.success_e2e_latency.#.milliseconds|float|Average|
|Success Server Latency|SuccessServerLatency|azure.files.success_server_latency.#.milliseconds|float|Average|
|Transactions (Authentication)|Transactions|azure.files.transactions_authentication.#.count|integer|Total|
|Transactions (ResponseType)|Transactions|azure.files.transactions_responsetype.#.count|integer|Total|

The "#" in 'Metric name in Mackerel' is a placeholder for one of the following.

  - ApiName<br>
  Indicates the API name defined in Azure Files.
    - Corresponding graphs
      - Availability (ApiName)
      - Success E2E Latency
      - Success Server Latency
  
  - Authentication<br>
  Indicates the type of authentication used for transactions with Azure Files.
    - Corresponding graphs
      - Availability (Authentication)
      - Transactions (Authentication)

  - FileShare<br>
  Indicates the file share name created with Azure Files.
    - Corresponding graphs
      - File Capacity
      - File Count
      - File Share Capacity Quota
      - File Share Provisioned IOPS
      - File Share Snapshot Count
      - File Share Snapshot Size

  - GeoType<br>
  Indicates a transaction from either Primary or Secondary.
    - Corresponding graphs
      - Ingress/Egress
  
  - ResponseType<br>
  Indicates the type of Azure Files response for transactions.
    - Corresponding graphs
      - Transactions (ResponseType)

## Metrics for which monitors do not work

Mackerel's monitor do not work if the timestamp of the metric being monitored is more than 20 minutes old. Therefore, even if you set a monitor for a metric with a sampling interval (Time Grains) of 20 minutes or more, it will not work. For information about the sampling interval for metrics, see the following Azure Files documentation.

[Azure Files monitoring data reference](https://learn.microsoft.com/en-us/azure/storage/files/storage-files-monitoring-reference)

[^1]: The maximum number of metrics can differ depending on your Azure Files. Refer [here](https://docs.microsoft.com/en-us/azure/storage/files/storage-files-monitoring-reference#metrics-dimensions) for more information.
