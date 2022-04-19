---
Title: AWSインテグレーション - DocumentDB
Date: 2022-03-31T10:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/docdb
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/13574176438084381528
CustomPath: integrations/aws/docdb
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/documentdb/" target="_blank">Amazon DocumentDB</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1インスタンス = 1マイクロホストと換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのDocumentDB対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/documentdb/latest/developerguide/cloud_watch.html" target="_blank">AWSのヘルプ</a>をご確認ください。

最大で53個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|CPU|CPUUtilization|docdb.cpu.used|percentage|Average|
|Database Connections|DatabaseConnections<br>DatabaseConnectionsMax|docdb.database_connections.used<br>docdb.database_connections.max|integer|Sum|
|Database Cursor|DatabaseCursors<br>DatabaseCursorsMax<br>DatabaseCursorsTimedOut|docdb.database_cursor.used<br>docdb.database_cursor.max<br>docdb.database_cursor.timeout|integer|Sum|
|Memory|FreeableMemory<br>SwapUsage|docdb.memory.free<br>docdb.memory.swap|bytes|Average|
|Storage|FreeLocalStorage|docdb.storage.free|bytes|Average|
|Transaction|TransactionsOpen<br>TransactionsOpenMax<br>TransactionsStarted<br>TransactionsCommitted<br>TransactionsAborted|docdb.transaction.open<br>docdb.transaction.open_max<br>docdb.transaction.started<br>docdb.transaction.committed<br>docdb.transaction.aborted|integer|Sum|
|Replica Lag|DBClusterReplicaLagMaximum [*1](#docdb-primary)<br>DBClusterReplicaLagMinimum [*1](#docdb-primary)<br>DBInstanceReplicaLag [*2](#docdb-replica)|docdb.replica_lag.max<br>docdb.replica_lag.min<br>docdb.replica_lag.lag|float|Average|
|Operation|OpcountersCommand<br>OpcountersDelete<br>OpcountersGetmore<br>OpcountersInsert<br>OpcountersQuery<br>OpcountersUpdate|docdb.operation.command<br>docdb.operation.delete<br>docdb.operation.getmore<br>docdb.operation.insert<br>docdb.operation.query<br>docdb.operation.update|integer|Sum|
|Disk Latency|ReadLatency<br>WriteLatency|docdb.disk_latency.read<br>docdb.disk_latency.write|float|Average|
|Disk IOPS|ReadIOPS<br>WriteIOPS|docdb.disk_iops.read<br>docdb.disk_iops.write|iops|Average|
|Documents|DocumentsDeleted<br>DocumentsInserted<br>DocumentsReturned<br>DocumentsUpdated<br>TTLDeletedDocuments|docdb.documents.deleted<br>docdb.documents.inserted<br>docdb.documents.returned<br>docdb.documents.updated<br>docdb.documents.ttl_deleted|integer|Sum|
|Network Throughput|NetworkReceiveThroughput<br>NetworkTransmitThroughput<br>NetworkThroughput|docdb.network_throughput.receive<br>docdb.network_throughput.transmit<br>docdb.network_throughput.all|integer|Average|
|Disk Throughput|ReadThroughput<br>WriteThroughput|docdb.disk_throughput.read<br>docdb.disk_throughput.write|bytes|Average|
|CPU Credit|CPUCreditUsage [*3](#docdb-t3)<br>CPUCreditBalance [*3](#docdb-t3)<br>CPUSurplusCreditBalance [*3](#docdb-t3)<br>CPUSurplusCreditsCharged [*3](#docdb-t3)|docdb.cpu_credit.usage<br>docdb.cpu_credit.balance<br>docdb.cpu_credit.surplus<br>docdb.cpu_credit.charged|float|Average|
|Cache Hit Ratio|BufferCacheHitRatio<br>IndexBufferCacheHitRatio|docdb.cache_hit_ratio.buffer<br>docdb.cache_hit_ratio.index|percentage|Average|
|Throttle Queue Depth|LowMemThrottleQueueDepth<br>LowMemThrottleMaxQueueDepth|docdb.throttle_queue_depth.low_mem<br>docdb.throttle_queue_depth.low_mem_max|integer|Sum|
|Disk Queue Depth|DiskQueueDepth|docdb.disk_queue_depth.disk|integer|Sum|
|Uptime|EngineUptime|docdb.uptime.engine|integer|Sum|
|Throttled|LowMemNumOperationsThrottled|docdb.throttled.low_mem|integer|Sum|
|Change Stream Log|ChangeStreamLogSize|docdb.change_stream_log.size|integer|Sum|
|Volume Used|VolumeBytesUsed [*4](#docdb-cluster)|docdb.volume_used.bytes|bytes|Sum|
|Backup Storage Used|SnapshotStorageUsed [*4](#docdb-cluster)<br>TotalBackupStorageBilled [*4](#docdb-cluster)<br>BackupRetentionPeriodStorageUsed [*4](#docdb-cluster)|docdb.backup_storage_used.snapshot<br>docdb.backup_storage_used.total_billed<br>docdb.backup_storage_used.retention_period|bytes|Sum|

<div id="docdb-primary">*1 PRIMARY側のみ発生します</div>
<div id="docdb-replica">*2 REPLICA側のみ発生します</div>
<div id="docdb-t3">*3 T3インスタンスのみ発生します</div>
<div id="docdb-cluster">*4 クラスター毎に発生するメトリックであり、同じクラスターのインスタンスは同じメトリックが表示されます</div>

<h2 id="notes">注意事項</h2>

AWSインテグレーションにより取得可能な上記のグラフ・メトリックのうち、下記のグラフに含まれるメトリックについては、通常、5分間隔粒度でのメトリックが取得されます。

* CPU Credit
* Volume Used

下記のグラフに含まれるメトリックについては、1日間隔のメトリックが取得されます。

* Backup Storage Used

これは、AWS CloudWatch APIの仕様です。
