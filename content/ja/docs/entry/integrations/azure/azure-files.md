---
Title: Azureインテグレーション - Azure Files
Date: 2020-12-21T09:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/azure/azure-files
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613681091351
---

MackerelはAzureインテグレーションにて<a href="https://azure.microsoft.com/ja-jp/services/storage/files/" target="_blank">Azure Files</a>のメトリック取得や監視に対応しています。課金対象として 1 Files = 1マイクロホスト と換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

Azureインテグレーションの設定方法や対応Azureサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/azure">Azureインテグレーション</a>

## 取得メトリック
AzureインテグレーションのAzure Files対応で取得できるメトリックはパフォーマンスによって異なります。
各パフォーマンスで取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://docs.microsoft.com/ja-jp/azure/storage/files/storage-files-monitoring-reference" target="_blank">Azureのドキュメント</a>をご確認ください。

### Standard
最大で 1 + 3 * (行われるオペレーションの種類の数) + 2 * (行われる認証の種類の数) + 5 * (対象のAzure Filesのファイル共有の数) + 2 (Secondaryが有効な場合) + 1 * (行われる応答の種類の数) 個[^1]のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
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
最大で 1 + 3 * (行われるオペレーションの種類の数) + 2 * (行われる認証の種類の数) + 6 * (対象のAzure Filesのファイル共有の数) + 2 (Secondaryが有効な場合) + 1 * (行われる応答の種類の数) 個[^1]のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
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

Mackerel上のメトリック名の「#」には、以下のいずれかが格納されます。

  - ApiName<br>
  Azure Filesで定義されたAPI名を示します。
    - 該当グラフ
      - Availability (ApiName)
      - Success E2E Latency
      - Success Server Latency
  
  - Authentication<br>
  Azure Filesとのトランザクションで使用される認証の種類を示します。
    - 該当グラフ
      - Availability (Authentication)
      - Transactions (Authentication)

  - FileShare<br>
  Azure Filesで作成されたファイル共有名を示します。
    - 該当グラフ
      - File Capacity
      - File Count
      - File Share Capacity Quota
      - File Share Provisioned IOPS
      - File Share Snapshot Count
      - File Share Snapshot Size

  - GeoType<br>
  Primary、またはSecondaryからのトランザクションであることを示します。
    - 該当グラフ
      - Ingress/Egress
  
  - ResponseType<br>
  トランザクションに対するAzure Filesの応答の種類を示します。
    - 該当グラフ
      - Transactions (ResponseType)

## 監視ルールによって評価されないメトリック

Mackerel の監視ルールは、監視対象のメトリックのタイムスタンプが現在時刻よりも 20 分以上古い場合に動作しない仕様のため、取得するメトリックのサンプリング間隔（Time Grains）が 20 分以上の場合は、監視ルールを設定しても動作しません。該当するメトリックに関しては、下記の Azure Files のドキュメントを参照してください。

[Azure Files monitoring data reference](https://learn.microsoft.com/en-us/azure/storage/files/storage-files-monitoring-reference)

[^1]: 最大メトリック数はご利用のAzure Filesによって異なります。詳しくは[こちら](https://docs.microsoft.com/ja-jp/azure/storage/files/storage-files-monitoring-reference#metrics-dimensions)をご覧ください。
