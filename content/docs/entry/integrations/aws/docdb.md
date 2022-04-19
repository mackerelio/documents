---
Title: AWS Integration - DocumentDB
Date: 2022-03-31T10:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/docdb
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/13574176438084381444
CustomPath: integrations/aws/docdb
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/documentdb/" target="_blank">Amazon DocumentDB</a> metrics in AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 Instance = 1 Micro Host. In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services.<br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

## Obtaining metrics
The metrics obtainable with AWS Integration's DocumentDB support are as follows. For `Metric` explanations, refer to the <a href="https://docs.aws.amazon.com/connect/latest/adminguide/monitoring-cloudwatch.html" target="_blank">AWS help page</a>.

The maximum number of metrics obtainable is 53.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
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

<div id="docdb-primary">*1 Only generated on the PRIMARY instance</div>
<div id="docdb-replica">*2 Only generated on the REPLICA instance</div>
<div id="docdb-t3">*3 Only generated on T3 instances</div>
<div id="docdb-cluster">*4 Metric is generated per cluster, and instances of the same cluster show the same metrics.</div>

<h2 id="notes">Notes</h2>

Among the graphs/metrics obtainable with AWS Integration, metrics included in the following graphs are usually obtained in 5 minute intervals.

* CPU Credit
* Volume Used

Metrics included in the following graphs are usually obtained in 1 day intervals.

* Backup Storage Used

This is specified by the AWS CloudWatch API.
