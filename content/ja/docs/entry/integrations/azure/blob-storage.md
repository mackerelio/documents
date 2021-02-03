---
Title: Azureインテグレーション - Blob Storage
Date: 2020-12-01T09:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/azure/blob-storage
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613686649831
CustomPath: integrations/azure/blob-storage
---

MackerelはAzureインテグレーションにて<a href="https://azure.microsoft.com/ja-jp/services/storage/blobs/" target="_blank">Blob Storage</a>のメトリック取得や監視に対応しています。課金対象として 1Blob Storage = 1マイクロホスト と換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

Azureインテグレーションの設定方法や対応Azureサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/azure">Azureインテグレーション</a>

## 取得メトリック
AzureインテグレーションのBlob Storage対応で取得できるメトリックはパフォーマンスによって異なります。
各パフォーマンスで取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://docs.microsoft.com/ja-jp/azure/storage/blobs/monitor-blob-storage-reference" target="_blank">Azureのドキュメント</a>をご確認ください。

### Standard
最大で `3 × (行われるオペレーションの種類の数) + 2 × (行われる認証の種類の数) + 2 (Secondaryが有効な場合) + 1 × (行われる応答の種類の数)` 個[^1]のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
|:---|:---|:---|:---|:---|
|Availability (ApiName)|Availability|azure.blob_storage.availability_apiname.#.percent|percentage|Average|
|Availability (Authentication)|Availability|azure.blob_storage.availability_authentication.#.percent|percentage|Average|
|Ingress/Egress|Ingress<br>Egress|azure.blob_storage.ingress_egress.#.ingress<br>azure.blob_storage.ingress_egress.#.egress|bytes|Total|
|Success E2E Latency|SuccessE2ELatency|azure.blob_storage.success_e2e_latency.#.milliseconds|float|Average|
|Success Server Latency|SuccessServerLatency|azure.blob_storage.success_server_latency.#.milliseconds|float|Average|
|Transactions (Authentication)|Transactions|azure.blob_storage.transactions_authentication.#.count|integer|Total|
|Transactions (ResponseType)|Transactions|azure.blob_storage.transactions_responsetype.#.count|integer|Total|

### Premium
最大で `3 × (行われるオペレーションの種類の数) + 2 × (行われる認証の種類の数) + 1 × (対象のBlob Storageのアクセス層の数) + 2 (Secondaryが有効な場合) + 1 × (行われる応答の種類の数)` 個[^1]のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
|:---|:---|:---|:---|:---|
|Availability (ApiName)|Availability|azure.blob_storage.availability_apiname.#.percent|percentage|Average|
|Availability (Authentication)|Availability|azure.blob_storage.availability_authentication.#.percent|percentage|Average|
|Blob Provisioned Size|BlobProvisionedSize|azure.blob_storage.blob_provisioned_size.#.bytes|bytes|Average|
|Ingress/Egress|Ingress<br>Egress|azure.blob_storage.ingress_egress.#.ingress<br>azure.blob_storage.ingress_egress.#.egress|bytes|Total|
|Success E2E Latency|SuccessE2ELatency|azure.blob_storage.success_e2e_latency.#.milliseconds|float|Average|
|Success Server Latency|SuccessServerLatency|azure.blob_storage.success_server_latency.#.milliseconds|float|Average|
|Transactions (Authentication)|Transactions|azure.blob_storage.transactions_authentication.#.count|integer|Total|
|Transactions (ResponseType)|Transactions|azure.blob_storage.transactions_responsetype.#.count|integer|Total|

Mackerel上のメトリック名の「#」には、以下のいずれかが格納されます。

  - ApiName<br>
  Blob Storageで定義されたAPI名を示します。
    - 該当グラフ
      - Availability (ApiName)
      - Success E2E Latency
      - Success Server Latency
  
  - Authentication<br>
  Blob Storageとのトランザクションで使用される認証の種類を示します。
    - 該当グラフ
      - Availability (Authentication)
      - Transactions (Authentication)
  
  - BlobTier<br>
  Blob Storageのアクセス層を示します。
    - 該当グラフ
      - Blob Provisioned Size
  
  - GeoType<br>
  Primary、またはSecondaryからのトランザクションであることを示します。
    - 該当グラフ
      - Ingress/Egress
  
  - ResponseType<br>
  トランザクションに対するBlob Storageの応答の種類を示します。
    - 該当グラフ
      - Transactions (ResponseType)

[^1]: 最大メトリック数はご利用のBlob Storageによって異なります。詳しくは[こちら](https://docs.microsoft.com/ja-jp/azure/storage/blobs/monitor-blob-storage-reference#metrics-dimensions)をご覧ください。
