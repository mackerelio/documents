---
Title: Azure Integration - Blob Storage
Date: 2020-12-01T09:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/azure/blob-storage
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613686651436
CustomPath: integrations/azure/blob-storage
---

Mackerel supports obtaining and monitoring <a href="https://azure.microsoft.com/en-us/services/storage/blobs/" target="_blank">Blob Storage</a> metrics in Azure Integration. Billable targets are determined using the conversion 1 Blob Storage = 1 Micro Host.

Please refer to the following page for Azure Integration configuration methods and a list of supported Azure services.<br>
<a href="https://mackerel.io/docs/entry/integrations/azure">Azure Integration</a>

### Notes

[Data Lake Storage](https://azure.microsoft.com/en-us/products/storage/data-lake-storage/) is not supported when Hierarchical Namespace is enabled.

## Obtaining metrics
The metrics available with Azure Integration Application Gateway support vary by performance tier. The metrics that can be obtained at each performance tier are as follows. For `Metric` explanations, please refer to the <a href="https://docs.microsoft.com/en-us/azure/storage/blobs/monitor-blob-storage-reference" target="_blank">Azure documentation</a>.

### Standard
The maximum number of metrics obtainable can be calculated using the formula `3 × (number of operation types) + 2 × (number of authentication types) + 2 (if Secondary is enabled) + 1 × (Number of response types)`[^1].

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|Availability (ApiName)|Availability|azure.blob_storage.availability_apiname.#.percent|percentage|Average|
|Availability (Authentication)|Availability|azure.blob_storage.availability_authentication.#.percent|percentage|Average|
|Ingress/Egress|Ingress<br>Egress|azure.blob_storage.ingress_egress.#.ingress<br>azure.blob_storage.ingress_egress.#.egress|bytes|Total|
|Success E2E Latency|SuccessE2ELatency|azure.blob_storage.success_e2e_latency.#.milliseconds|float|Average|
|Success Server Latency|SuccessServerLatency|azure.blob_storage.success_server_latency.#.milliseconds|float|Average|
|Transactions (Authentication)|Transactions|azure.blob_storage.transactions_authentication.#.count|integer|Total|
|Transactions (ResponseType)|Transactions|azure.blob_storage.transactions_responsetype.#.count|integer|Total|

### Premium
The maximum number of metrics obtainable can be calculated using the formula `3 × (number of operation types) + 2 × (number of authentication types) + 1 × (number of Blob Storage access levels) + 2 (if Secondary is enabled) + 1 × (Number of response types)`[^1].

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|Availability (ApiName)|Availability|azure.blob_storage.availability_apiname.#.percent|percentage|Average|
|Availability (Authentication)|Availability|azure.blob_storage.availability_authentication.#.percent|percentage|Average|
|Blob Provisioned Size|BlobProvisionedSize|azure.blob_storage.blob_provisioned_size.#.bytes|bytes|Average|
|Ingress/Egress|Ingress<br>Egress|azure.blob_storage.ingress_egress.#.ingress<br>azure.blob_storage.ingress_egress.#.egress|bytes|Total|
|Success E2E Latency|SuccessE2ELatency|azure.blob_storage.success_e2e_latency.#.milliseconds|float|Average|
|Success Server Latency|SuccessServerLatency|azure.blob_storage.success_server_latency.#.milliseconds|float|Average|
|Transactions (Authentication)|Transactions|azure.blob_storage.transactions_authentication.#.count|integer|Total|
|Transactions (ResponseType)|Transactions|azure.blob_storage.transactions_responsetype.#.count|integer|Total|

Enter one of the following in place of the # in "Metric name in Mackerel".

  - ApiName<br>
  Indicates the API name defined in Blob Storage.
    - Corresponding graph
      - Availability (ApiName)
      - Success E2E Latency
      - Success Server Latency
  
  - Authentication<br>
  Indicates the type of authentication used in transactions with Blob Storage.
    - Corresponding graph
      - Availability (Authentication)
      - Transactions (Authentication)
  
  - BlobTier<br>
  Indicates the Blob Storage access level.
    - Corresponding graph
      - Blob Provisioned Size
  
  - GeoType<br>
  Indicates a transaction from either Primary or Secondary.
    - Corresponding graph
      - Ingress/Egress
  
  - ResponseType<br>
  Indicates the type of Blob Storage response for a transaction.
    - Corresponding graph
      - Transactions (ResponseType)

[^1]: The maximum number of metrics that can be obtained depends on your Blob Storage. See [here](https://docs.microsoft.com/en-us/azure/storage/blobs/monitor-blob-storage-reference#metrics-dimensions) for more details.

