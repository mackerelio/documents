---
Title: Azure Integration - Database for PostgreSQL
Date: 2020-05-25T14:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/azure/database-for-postgresql
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613592629022
CustomPath: integrations/azure/database-for-postgresql
---

Mackerel supports obtaining and monitoring <a href="https://azure.microsoft.com/en-us/services/postgresql/" target="_blank" rel="noreferrer">Database for PostgreSQL</a> metrics in Azure Integration. Billable targets are determined using the conversion 1 resource = 1 Micro Host. (Currently "Single Server" and "Flexible Server" deployment options are supported.)

Please refer to the following page for Azure Integration configuration methods and a list of supported Azure services.<br>
<a href="https://mackerel.io/docs/entry/integrations/azure">Azure Integration</a>

## Flexible Server
Integration for Flexible Server can be done by selecting "Database for PostgreSQL Flexible Server" in "Metric collecting service" section of the Azure Integration Settings screen.

### Obtaining metrics
The following metrics can be retrieved for Database for PostgreSQL flexible servers.

A maximum of 34 metrics (including 16 enhanced metrics) will be retrieved.
Enhanced metrics can be retrieved by turning on the server parameter metrics.collector_database_activity.
The actual metrics obtained may be reduced depending on the server SKU and role. 

For `Metric` explanations and how to enable enhanced metrics, please refer to the <a href="https://docs.microsoft.com/en-us/azure/postgresql/flexible-server/concepts-monitoring" target="_blank" rel="noreferrer">Azure documentation</a>.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|Enhanced Metric|
|:---|:---|:---|:---|:---|:---|
|Availability|is_db_alive|azure.db_for_postgresql.availability.db|float|Average|x|
|Backends|numbackends|azure.db_for_postgresql.backends.count|float|Maximum|x|
|Backup Storage used|backup_storage_used|azure.db_for_postgresql.backup_storage_used.bytes|bytes|Average||
|Connections|active_connections<br>connections_succeeded<br>connections_failed|azure.db_for_postgresql.connections.active<br>azure.db_for_postgresql.connections.succeeded<br>azure.db_for_postgresql.connections.failed|float|Average<br>Total<br>Total||
|CPU credits Remaining/Consumed|cpu_credits_remaining<br>cpu_credits_consumed|azure.db_for_postgresql.cpu_credits.remaining<br>azure.db_for_postgresql.cpu_credits.consumed|float|Average||
|CPU percent|cpu_percent|azure.db_for_postgresql.cpu.percent|percentage|Average||
|Deadlocks|deadlocks|azure.db_for_postgresql.deadlocks.count|float|Total|x|
|Disk Bandwidth Consumed|disk_bandwidth_consumed_percentage|azure.db_for_postgresql.disk_bandwidth_consumed.percent|percentage|Average|x|
|Disk IOPS|read_iops<br>write_iops|azure.db_for_postgresql.disk_iops.read<br>azure.db_for_postgresql.disk_iops.write|iops|Average||
|Disk IOPS Consumed|disk_iops_consumed_percentage|azure.db_for_postgresql.disk_ios_consumed.percent|percentage|Average|x|
|Disk Throughput|read_throughput<br>write_throughput|azure.db_for_postgresql.disk_throughput.read<br>azure.db_for_postgresql.disk_throughput.write|bytes/sec|Average||
|Logical Replication Lag|logical_replication_delay_in_bytes|azure.db_for_postgresql.logical_replication_lag_bytes.max|bytes|Maximum|x|
|Memory percent|memory_percent|azure.db_for_postgresql.memory.percent|percentage|Average||
|Network In/Out|network_bytes_egress<br>network_bytes_ingress|azure.db_for_postgresql.network.out<br>azure.db_for_postgresql.network.in|bytes|Total||
|Operated Tuples by Queries|tup_deleted<br>tup_fetched<br>tup_inserted<br>tup_returned<br>tup_updated|azure.db_for_postgresql.tuples.deleted<br>azure.db_for_postgresql.tuples.fetched<br>azure.db_for_postgresql.tuples.inserted<br>azure.db_for_postgresql.tuples.returned<br>azure.db_for_postgresql.tuples.updated|float|Total|x|
|Physical Replication Lag|physical_replication_delay_in_bytes|azure.db_for_postgresql.replication_lag_bytes.max|bytes|Maximum|x|
|Read Replica Lag|physical_replication_delay_in_seconds|azure.db_for_postgresql.replication_lag.seconds|seconds|Maximum|x|
|Storage percent|storage_percent|azure.db_for_postgresql.storage.percent|percentage|Average||
|Storage limit/used|storage_limit<br>storage_used|azure.db_for_postgresql.storage_limit_used.limit<br>azure.db_for_postgresql.storage_limit_used.used|bytes|Maximum<br>Average||
|Transactions|xact_total<br>xact_commit<br>xact_rollback|azure.db_for_postgresql.transactions.total<br>azure.db_for_postgresql.transactions.commit<br>azure.db_for_postgresql.transactions.rollback|float|Total|x|
|Used Transaction IDs|maximum_used_transactionIDs|azure.db_for_postgresql.used_transaction_ids.max|float|Max||

### Notes
- Of the graphs / metrics that can be obtained with Azure integration listed above, when obtaining Backup Storage used at the Basic price level, it is done so according to the specifications of the Azure Monitor API used by Azure integration, normally with a 15-minute interval granularity and updates having a latency of up to 30 minutes.

## Single Server
Integration for Single Server can be done by selecting "Database for PostgreSQL Single Server" in "Metric collecting service" section of the Azure Integration Settings screen.
The old Database for PostgreSQL integration settings have been transferred to Database for PostgreSQL Single Server.

### Obtaining metrics
The metrics obtainable with Azure Integration’s Database for PostgreSQL support vary by price level.
The metrics for each price level are as follows. For `Metric` explanations, please refer to the <a href="https://docs.microsoft.com/en-us/azure/postgresql/single-server/concepts-monitoring" target="_blank" rel="noreferrer">Azure documentation</a>.

#### Basic
The maximum number of metrics obtainable is 15.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|Active/Failed Connections|active_connections<br>connections_failed|azure.db_for_postgresql.connections.active<br>azure.db_for_postgresql.connections.failed|float|Average<br>Total|
|Backup Storage used|backup_storage_used|azure.db_for_postgresql.backup_storage_used.bytes|bytes|Average|
|CPU percent|cpu_percent|azure.db_for_postgresql.cpu.percent|percentage|Average|
|Max Lag Across Replicas|pg_replica_log_delay_in_bytes|azure.db_for_postgresql.replica_log_delay_in_bytes.bytes|bytes|Maximum|
|Memory percent|memory_percent|azure.db_for_postgresql.memory.percent|percentage|Average|
|Network In/Out|network_bytes_egress<br>network_bytes_ingress|azure.db_for_postgresql.network.out<br>azure.db_for_postgresql.network.in|bytes|Total|
|Replica Lag|pg_replica_log_delay_in_seconds|azure.db_for_postgresql.replica_log_delay_in_seconds.seconds|float|Maximum|
|Server Log storage limit/used|serverlog_storage_limit<br>serverlog_storage_usage|azure.db_for_postgresql.server_log_storage_limit_used.limit<br>azure.db_for_postgresql.server_log_storage_limit_used.used|bytes|Maximum<br>Average|
|Server Log storage percent|serverlog_storage_percent|azure.db_for_postgresql.server_log_storage.percent|percentage|Average|
|Storage limit/used|storage_limit<br>storage_used|azure.db_for_postgresql.storage_limit_used.limit<br>azure.db_for_postgresql.storage_limit_used.used|bytes|Maximum<br>Average|
|Storage percent|storage_percent|azure.db_for_postgresql.storage.percent|percentage|Average|

#### General Purpose
The maximum number of metrics obtainable is 15.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|Active/Failed Connections|active_connections<br>connections_failed|azure.db_for_postgresql.connections.active<br>azure.db_for_postgresql.connections.failed|float|Average<br>Total|
|CPU percent|cpu_percent|azure.db_for_postgresql.cpu.percent|percentage|Average|
|IO percent|io_consumption_percent|azure.db_for_postgresql.io.percent|percentage|Average|
|Max Lag Across Replicas|pg_replica_log_delay_in_bytes|azure.db_for_postgresql.replica_log_delay_in_bytes.bytes|bytes|Maximum|
|Memory percent|memory_percent|azure.db_for_postgresql.memory.percent|percentage|Average|
|Network In/Out|network_bytes_egress<br>network_bytes_ingress|azure.db_for_postgresql.network.out<br>azure.db_for_postgresql.network.in|bytes|Total|
|Replica Lag|pg_replica_log_delay_in_seconds|azure.db_for_postgresql.replica_log_delay_in_seconds.seconds|float|Maximum|
|Server Log storage limit/used|serverlog_storage_limit<br>serverlog_storage_usage|azure.db_for_postgresql.server_log_storage_limit_used.limit<br>azure.db_for_postgresql.server_log_storage_limit_used.used|bytes|Maximum<br>Average|
|Server Log storage percent|serverlog_storage_percent|azure.db_for_postgresql.server_log_storage.percent|percentage|Average|
|Storage limit/used|storage_limit<br>storage_used|azure.db_for_postgresql.storage_limit_used.limit<br>azure.db_for_postgresql.storage_limit_used.used|bytes|Maximum<br>Average|
|Storage percent|storage_percent|azure.db_for_postgresql.storage.percent|percentage|Average|

#### Memory Optimized
The maximum number of metrics obtainable is 15.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|Active/Failed Connections|active_connections<br>connections_failed|azure.db_for_postgresql.connections.active<br>azure.db_for_postgresql.connections.failed|float|Average<br>Total|
|CPU percent|cpu_percent|azure.db_for_postgresql.cpu.percent|percentage|Average|
|IO percent|io_consumption_percent|azure.db_for_postgresql.io.percent|percentage|Average|
|Max Lag Across Replicas|pg_replica_log_delay_in_bytes|azure.db_for_postgresql.replica_log_delay_in_bytes.bytes|bytes|Maximum|
|Memory percent|memory_percent|azure.db_for_postgresql.memory.percent|percentage|Average|
|Network In/Out|network_bytes_egress<br>network_bytes_ingress|azure.db_for_postgresql.network.out<br>azure.db_for_postgresql.network.in|bytes|Total|
|Replica Lag|pg_replica_log_delay_in_seconds|azure.db_for_postgresql.replica_log_delay_in_seconds.seconds|float|Maximum|
|Server Log storage limit/used|serverlog_storage_limit<br>serverlog_storage_usage|azure.db_for_postgresql.server_log_storage_limit_used.limit<br>azure.db_for_postgresql.server_log_storage_limit_used.used|bytes|Maximum<br>Average|
|Server Log storage percent|serverlog_storage_percent|azure.db_for_postgresql.server_log_storage.percent|percentage|Average|
|Storage limit/used|storage_limit<br>storage_used|azure.db_for_postgresql.storage_limit_used.limit<br>azure.db_for_postgresql.storage_limit_used.used|bytes|Maximum<br>Average|
|Storage percent|storage_percent|azure.db_for_postgresql.storage.percent|percentage|Average|

### Notes
- Of the graphs / metrics that can be obtained with Azure integration listed above, when obtaining Backup Storage used at the Basic price level, it is done so according to the specifications of the Azure Monitor API used by Azure integration, normally with a 15-minute interval granularity and updates having a latency of up to 30 minutes.<br>
Additionally, it is not graphed in replica servers.

- pg_replica_log_delay_in_bytes only counts up on the master server.<br>
As a result, Max Lag Across Replicas is not graphed in replica servers.

- pg_replica_log_delay_in_seconds only counts up on replica servers.<br>
As a result, Replica Lag is not graphed in the master server.
