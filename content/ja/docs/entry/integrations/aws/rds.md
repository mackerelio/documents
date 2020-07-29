---
Title: AWSインテグレーション - RDS
Date: 2016-12-28T12:44:42+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/rds
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10328749687201712775
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/rds/" target="_blank">Amazon Relational Database Service (RDS)</a>のメトリック取得や監視に対応しています。
AWSインテグレーションで連携をおこなった場合、以下の対応で換算されます。

- Aurora Serverlessの場合: Aurora Serverless 1クラスター = 1マイクロホスト
- それ以外の場合: RDS 1インスタンス = 1マイクロホスト

またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのRDS対応で取得できるメトリックは以下の通りです。 `メトリック` の説明に関してはAWSのヘルプ(<a href="https://docs.aws.amazon.com/ja_jp/AmazonRDS/latest/AuroraUserGuide/Aurora.Monitoring.html" target="_blank">Aurora</a>、<a href="https://docs.aws.amazon.com/ja_jp/AmazonRDS/latest/UserGuide/MonitoringOverview.html" target="_blank">Aurora以外</a>)をご確認ください。

Auroraでは最大で52個、Aurora Serverlessでは最大で51個、それ以外では最大で24個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:---|:---|:---|:---|:---|
|CPU|CPUUtilization|rds.cpu.used|percentage|Average|
|CPU Credit|CPUCreditUsage<br>CPUCreditBalance|rds.cpu_credit.used<br>rds.cpu_credit.balance|float|Average|
|Database Connections|DatabaseConnections|rds.database_connections.used|float|Average|
|Disk Queue|DiskQueueDepth|rds.disk_queue.depth|float|Average|
|BinLog Disk Usage|BinLogDiskUsage|rds.disk_usage.bin_log|bytes|Average|
|Memory|FreeableMemory<br>SwapUsage|rds.memory.free<br>rds.memory.swap|bytes|Average|
|Free Storage Space|FreeStorageSpace|rds.disk.free|bytes|Average|
|Replica Lag|ReplicaLag|rds.replica_lag.lag|float|Average|
|Disk IOPS|ReadIOPS<br>WriteIOPS|rds.diskiops.read<br>rds.diskiops.write|iops|Average|
|Disk Latency|ReadLatency<br>WriteLatency|rds.latency.read<br>rds.latency.write|float|Average|
|Disk Throughput|ReadThroughput<br>WriteThroughput|rds.throughput.read<br>rds.throughput.write|bytes/sec|Average|
|Network Throughput|NetworkReceiveThroughput<br>NetworkTransmitThroughput|rds.network_throughput.read<br>rds.network_throughput.transmit|bytes/sec|Average|
|gp2 Storage Burst Balance|BurstBalance|rds.burst_balance.balance|percentage|Average|
|Maximum Used Transaction IDs|MaximumUsedTransactionIDs [*1](#rds-postgres)|rds.maximum_used_transaction_ids.count|integer|Average|
|Disk Usage|ReplicationSlotDiskUsage [*1](#rds-postgres)<br>TransactionLogsDiskUsage [*1](#rds-postgres)|rds.postgres_disk_usage.replication_slot<br>rds.postgres_disk_usage.transaction_logs|bytes|Average|
|Oldest Replication Slot Lag|OldestReplicationSlotLag [*1](#rds-postgres)|rds.oldest_replication_slot_lag.slot_lag|bytes|Average|
|Transaction Logs Generation|TransactionLogsGeneration [*1](#rds-postgres)|rds.transaction_logs_generation.transaction_log|bytes/sec|Average|
|Failed SQL Server Agent Jobs|FailedSQLServerAgentJobsCount [*2](#rds-sqlserver)|rds.failed_sql_server_agent_jobs.failed|integer|Average|

<div id="rds-postgres">*1 PostgreSQLに適用されます</div>
<div id="rds-sqlserver">*2 Microsoft SQL Serverに適用されます</div>
<br>

Auroraで取得できるメトリックは、上記に加えて以下のとおりです。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:---|:---|:---|:---|:---|
|Storage|FreeLocalStorage|rds.aurora.storage.free|bytes|Average|
|BinLog Replica Lag|AuroraBinLogReplicaLag|rds.aurora.binlog_replica_lag.lag|float|Average|
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
|Backtrack Window Difference|BacktrackWindowActual [*3](#rds-aurora-mysql)|rds.aurora.backtrack_window_difference.minutes|integer|Average|
|Backtrack Window Alert|BacktrackWindowAlert [*3](#rds-aurora-mysql)|rds.aurora.backtrack_window_alert.alert|integer|Sum|
|Aurora Volume Bytes Left Total|AuroraVolumeBytesLeftTotal [*3](#rds-aurora-mysql)|rds.aurora.aurora_volume_bytes_left_total.total|bytes|Average|

<div id="rds-aurora-mysql">*3 Aurora MySQLに適用されます</div>
<br>

また、 Aurora Serverlessクラスターの場合は、Auroraで取得できるメトリックに加えて以下のメトリックが取得できます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:---|:---|:---|:---|:---|
|Serverless Database Capacity|ServerlessDatabaseCapacity|rds.aurora.serverless_database_capacity.capacity|float|Average|

<h2 id="notes">注意事項</h2>

AWSインテグレーションにより取得可能な上記のグラフ・メトリックのうち、下記のグラフに含まれるメトリックについては、通常、5分間隔粒度でのメトリックが取得されます。

* CPU Credit
* gp2 Storage Burst Balance

これは、AWS CloudWatch API の仕様です。
