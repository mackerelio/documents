---
Title: Azureインテグレーション - Azure NetApp Files
Date: 2026-02-09T16:22:32+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/azure/netapp-files
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17179246901352790648
---

MackerelはAzureインテグレーションにて<a href="https://azure.microsoft.com/ja-jp/services/netapp/" target="_blank">Azure NetApp Files</a>のメトリック取得や監視に対応しています。課金対象として 1容量プール = 1マイクロホスト と換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

Azureインテグレーションの設定方法や対応Azureサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/azure">Azureインテグレーション</a>

## 取得メトリック
AzureインテグレーションのAzure NetApp Files対応で取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては次のAzureのドキュメントをご確認ください。
- [サポートされているメトリック - Microsoft.NetApp/netAppAccounts/capacityPools - Azure Monitor | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/azure-monitor/reference/supported-metrics/microsoft-netapp-netappaccounts-capacitypools-metrics)
- [サポートされているメトリック - Microsoft.NetApp/netAppAccounts/capacityPools/volumes - Azure Monitor | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/azure-monitor/reference/supported-metrics/microsoft-netapp-netappaccounts-capacitypools-volumes-metrics)

最大で`6 + 36 × (ボリューム数)`個のメトリックが取得されます。

### 容量プールごとのグラフ

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
|:---|:---|:---|:---|:---|
|Pool Size|VolumePoolAllocatedSize<br>VolumePoolAllocatedUsed<br>VolumePoolTotalLogicalSize|azure.netapp_files.pool_size.allocated<br>azure.netapp_files.pool_size.allocated_used<br>azure.netapp_files.pool_size.consumed|bytes|Average
|Pool Total Snapshot Size|VolumePoolTotalSnapshotSize|azure.netapp_files.pool_total_snapshot_size.bytes|bytes|Average
|Pool Throughput|VolumePoolProvisionedThroughput<br>VolumePoolAllocatedToVolumeThroughput|azure.netapp_files.pool_throughput.provisioned<br>azure.netapp_files.pool_throughput.allocated_to_volume|bytes/sec|Average

### ボリュームごとのグラフ

メトリック名の`#`にボリュームの名前が入ります。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
|:---|:---|:---|:---|:---|
|Volume Average R/W Latency|AverageReadLatency<br>AverageWriteLatency|azure.netapp_files.average_rw_latency.#.read<br>azure.netapp_files.average_rw_latency.#.write|milliseconds|Average
|Volume Backup Is Suspended|CbsVolumeBackupActive|azure.netapp_files.cbs_volume_backup_active.#.active|integer|Average
|Volume Backup Bytes|CbsVolumeLogicalBackupBytes|azure.netapp_files.cbs_volume_logical_backup_bytes.#.bytes|bytes|Average
|Volume Backup Operation Last Transferred Bytes|CbsVolumeOperationBackupTransferredBytes|azure.netapp_files.cbs_volume_operation_backup_transferred_bytes.#.bytes|bytes|Average
|Volume Backup Operation Is Completed|CbsVolumeOperationComplete|azure.netapp_files.cbs_volume_operation_complete.#.complete|integer|Average
|Volume Backup Restore Operation Last Transferred Bytes|CbsVolumeOperationRestoreTransferredBytes|azure.netapp_files.cbs_volume_operation_restore_transferred_bytes.#.bytes|bytes|Average
|Volume Backup Is Enabled|CbsVolumeProtected|azure.netapp_files.cbs_volume_protected.#.protected|integer|Average
|Volume IOPS|ReadIops<br>WriteIops<br>OtherIops<br>TotalIops|azure.netapp_files.iops.#.read<br>azure.netapp_files.iops.#.write<br>azure.netapp_files.iops.#.other<br>azure.netapp_files.iops.#.total|iops|Average
|Volume Throughput|ReadThroughput<br>WriteThroughput<br>OtherThroughput<br>TotalThroughput|azure.netapp_files.throughput.#.read<br>azure.netapp_files.throughput.#.write<br>azure.netapp_files.throughput.#.other<br>azure.netapp_files.throughput.#.total|bytes/sec|Average
|Volume QoS Latency Delta|QosLatencyDelta|azure.netapp_files.qos_latency_delta.#.milliseconds|milliseconds|Average
|Volume Throughput Limit Reached|ThroughputLimitReached|azure.netapp_files.throughput_limit_reached.#.reached|integer|Average
|Volume Allocated Size|VolumeAllocatedSize|azure.netapp_files.volume_allocated_size.#.bytes|bytes|Average
|Volume Consumed Size Percentage|VolumeConsumedSizePercentage|azure.netapp_files.volume_consumed_size_percentage.#.percent|percentage|Average
|Volume Cool Tier Data R/W Size|VolumeCoolTierDataReadSize<br>VolumeCoolTierDataWriteSize |azure.netapp_files.volume_cool_tier_data_rw_size.#.read<br>azure.netapp_files.volume_cool_tier_data_rw_size.#.write|bytes|Average
|Volume Cool Tier Data Size|VolumeCoolTierSize|azure.netapp_files.volume_cool_tier_data_size.#.bytes|bytes|Average
|Volume Inodes Percentage|VolumeInodesPercentage|azure.netapp_files.volume_inodes_percentage.#.percent|percentage|Average
|Volume Inodes|VolumeInodesUsed<br>VolumeInodesTotal<br>VolumeInodesQuota|azure.netapp_files.volume_inodes.#.used<br>azure.netapp_files.volume_inodes.#.total<br>azure.netapp_files.volume_inodes.#.quota|integer|Average
|Volume Consumed Size|VolumeLogicalSize|azure.netapp_files.volume_consumed_size.#.bytes|bytes|Average
|Volume Snapshot Size|VolumeSnapshotSize|azure.netapp_files.volume_snapshot_size.#.bytes|bytes|Average
|Volume Replication Status Is Healthy|XregionReplicationHealthy|azure.netapp_files.xregion_replication_healthy.#.healthy|integer|Average
|Volume Replication Lag Time|XregionReplicationLagTime|azure.netapp_files.xregion_replication_lag_time.#.seconds|seconds|Average
|Volume Replication Last Transfer Duration|XregionReplicationLastTransferDuration|azure.netapp_files.xregion_replication_last_transfer_duration.#.seconds|seconds|Average
|Volume Replication Last Transfer Size|XregionReplicationLastTransferSize|azure.netapp_files.xregion_replication_last_transfer_size.#.bytes|bytes|Average
|Volume Replication Progress|XregionReplicationRelationshipProgress |azure.netapp_files.xregion_replication_relationship_progress.#.bytes|bytes|Average
|Volume Replication Is Transferring|XregionReplicationRelationshipTransferring|azure.netapp_files.xregion_replication_relationship_transferring.#.transferring|integer|Average
|Volume Replication Total Transfer|XregionReplicationTotalTransferBytes|azure.netapp_files.xregion_replication_total_transfer_bytes.#.bytes|bytes|Average
