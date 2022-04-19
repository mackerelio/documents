---
Title: AWS Integration - RDS
Date: 2017-01-11T18:33:21+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/rds
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10328749687205762200
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/rds/" target="_blank">Amazon Relational Database Service (RDS)</a> metrics in AWS Integration. When integrating with AWS Integration, billable targets are determined using the following conversions.

- For Aurora Serverless: 1 Cluster = 1 Micro Host
- For others: 1 Instance = 1 Micro Host

In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services.  <br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

## Obtaining metrics

The metrics obtainable with AWS Integration’s RDS support are as follows. For `Metric` explanations, refer to the AWS help pages (<a href="https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Monitoring.html" target="_blank">Aurora</a> and <a href="https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MonitoringOverview.html" target="_blank">otherwise</a>).

For each DB engine, the maximum number of metrics shown in the table below is obtained.

|DB engine|  |The maximum number of metrics obtainable|
|:---|:---|---:|
|RDS|PostgreSQL|24|
|  |SQL Server|20|
|  |Other than those above|19|
|Aurora|MySQL|43|
|  |PostgreSQL|39|
|Aurora Serverless|MySQL|41|
|  |PostgreSQL|40|

### Common metrics
The following metrics can be obtained in common for RDS, Aurora, and Aurora Serverless.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|CPU|CPUUtilization|rds.cpu.used|percentage|Average|
|CPU Credit|CPUCreditUsage<br>CPUCreditBalance|rds.cpu_credit.used<br>rds.cpu_credit.balance|float|Average|
|Database Connections|DatabaseConnections|rds.database_connections.used|float|Average|
|BinLog Disk Usage|BinLogDiskUsage|rds.disk_usage.bin_log|bytes|Average|
|Memory|FreeableMemory<br>SwapUsage|rds.memory.free<br>rds.memory.swap|bytes|Average|
|Network Throughput|NetworkReceiveThroughput<br>NetworkTransmitThroughput|rds.network_throughput.read<br>rds.network_throughput.transmit|bytes/sec|Average|
|gp2 Storage Burst Balance|BurstBalance|rds.burst_balance.balance|percentage|Average|
|Maximum Used Transaction IDs|MaximumUsedTransactionIDs [*1](#rds-postgres)|rds.maximum_used_transaction_ids.count|integer|Average|


<div id="common-postgres">*1 Applies to PostgreSQL</div>
<br>

### RDS metrics
In the case of RDS, the following metrics can be obtained in addition to the above common metrics.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|Disk Queue|DiskQueueDepth|rds.disk_queue.depth|float|Average|
|Free Storage Space|FreeStorageSpace|rds.disk.free|bytes|Average|
|Replica Lag|ReplicaLag|rds.replica_lag.lag|float|Average|
|Disk IOPS|ReadIOPS<br>WriteIOPS|rds.diskiops.read<br>rds.diskiops.write|iops|Average|
|Disk Latency|ReadLatency<br>WriteLatency|rds.latency.read<br>rds.latency.write|float|Average|
|Disk Throughput|ReadThroughput<br>WriteThroughput|rds.throughput.read<br>rds.throughput.write|bytes/sec|Average|
|Disk Usage|ReplicationSlotDiskUsage [*2](#rds-postgres)<br>TransactionLogsDiskUsage [*2](#rds-postgres)|rds.postgres_disk_usage.replication_slot<br>rds.postgres_disk_usage.transaction_logs|bytes|Average|
|Oldest Replication Slot Lag|OldestReplicationSlotLag [*2](#rds-postgres)|rds.oldest_replication_slot_lag.slot_lag|bytes|Average|
|Transaction Logs Generation|TransactionLogsGeneration [*2](#rds-postgres)|rds.transaction_logs_generation.transaction_log|bytes/sec|Average|
|Failed SQL Server Agent Jobs|FailedSQLServerAgentJobsCount [*3](#rds-sqlserver)|rds.failed_sql_server_agent_jobs.failed|integer|Average|

<div id="rds-postgres">*2 Applies to PostgreSQL</div>
<div id="rds-sqlserver">*3 Applies to Microsoft SQL Server</div>
<br>
### Aurora metrics
In the case of Aurora, the following metrics can be obtained in addition to the above common metrics.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|Storage|FreeLocalStorage|rds.aurora.storage.free|bytes|Average|
|BinLog Replica Lag|AuroraBinlogReplicaLag|rds.aurora.binlog_replica_lag.lag|float|Average|
|Replica Lag|AuroraReplicaLag<br>AuroraReplicaLagMaximum<br>AuroraReplicaLagMinimum|rds.aurora.replica_lag.lag<br>rds.aurora.replica_lag.max_lag<br>rds.aurora.replica_lag.min_lag|float|Average|
|Engine Uptime|EngineUptime|rds.aurora.engine_uptime.uptime|float|Average|
|Transactions|ActiveTransactions<br>BlockedTransactions|rds.aurora.transactions.active<br>rds.aurora.transactions.blocked|float|Average|
|Cache Hit Ratio|BufferCacheHitRatio<br>ResultSetCacheHitRatio|rds.aurora.hit_ratio.buffer<br>rds.aurora.hit_ratio.result_set|percentage|Average|
|DML Latency|InsertLatency<br>UpdateLatency<br>DeleteLatency<br>DMLLatency|rds.aurora.dml_latency.insert<br>rds.aurora.dml_latency.update<br>rds.aurora.dml_latency.delete<br>rds.aurora.dml_latency.dml|float|Average|
|DML throughput|InsertThroughput<br>UpdateThroughput<br>DeleteThroughput<br>DMLThroughput|rds.aurora.dml_throughput.insert<br>rds.aurora.dml_throughput.update<br>rds.aurora.dml_throughput.delete<br>rds.aurora.dml_throughput.dml|float|Average|
|Commit Latency|CommitLatency|rds.aurora.commit_latency.commit|float|Average|
|Commit Throughput|CommitThroughput|rds.aurora.commit_throughput.commit|float|Average|
|Select Latency|SelectLatency|rds.aurora.select_latency.select|float|Average|
|Select Throughput|SelectThroughput|rds.aurora.select_throughput.select|float|Average|
|DDL Latency|DDLLatency|rds.aurora.ddl_latency.ddl|float|Average|
|DDL Throughput|DDLThroughput|rds.aurora.ddl_throughput.ddl|float|Average|
|Queries|Queries|rds.aurora.queries.queries|float|Average|
|Login Failures|LoginFailures|rds.aurora.login_failures.failures|float|Average|
|Deadlocks|Deadlocks|rds.aurora.deadlocks.deadlocks|float|Average|
|Backtrack Window Difference|BacktrackWindowActual [*4](#rds-aurora-mysql)|rds.aurora.backtrack_window_difference.minutes|integer|Average|
|Backtrack Window Alert|BacktrackWindowAlert [*4](#rds-aurora-mysql)|rds.aurora.backtrack_window_alert.alert|integer|Sum|
|Aurora Volume Bytes Left Total|AuroraVolumeBytesLeftTotal [*4](#rds-aurora-mysql)|rds.aurora.aurora_volume_bytes_left_total.total|bytes|Average|
|Aborted Clients|AbortedClients [*4](#rds-aurora-mysql)|rds.aurora.aborted_clients.aborted|integer|Sum|
|Row Lock Time|RowLockTime [*4](#rds-aurora-mysql)|rds.aurora.row_lock_time.row_lock|float|Average|
|Volume Used|VolumeBytesUsed [*5](#rds-aurora-cluster)|rds.aurora.volume_used.bytes|bytes|Average|

<div id="rds-aurora-mysql">*4 Applies to Aurora MySQL</div>
<div id="rds-aurora-cluster">*5 Generated per cluster. Instances of the same cluster will display the same metric.</div>
<br>

### Aurora Serverless metrics
In the case of Aurora Serverless, the following metrics can be obtained in addition to the above common metrics.


|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|Serverless Database Capacity|ServerlessDatabaseCapacity|rds.aurora.serverless_database_capacity.capacity|float|Average|

<h2 id="notes">Precautions</h2>

Among the graphs/metrics obtainable with AWS Integration, metrics included in the following graphs are usually obtained in 5 minute intervals.

* CPU Credit
* gp2 Storage Burst Balance
* Volume Used

This is specified by the AWS CloudWatch API.
