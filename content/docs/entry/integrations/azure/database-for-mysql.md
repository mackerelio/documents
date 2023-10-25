---
Title: Azure Integration - Database for MySQL
Date: 2020-04-30T14:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/azure/database-for-mysql
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613592628895
CustomPath: integrations/azure/database-for-mysql
---

Mackerel supports obtaining and monitoring <a href="https://azure.microsoft.com/en-us/services/mysql/" target="_blank" rel="noreferrer">Database for MySQL</a> metrics in Azure Integration. Billable targets are determined using the conversion 1 resource = 1 Micro Host.  (Currently "Single Server" and "Flexible Server" deployment options are supported.)

Please refer to the following page for Azure Integration configuration methods and a list of supported Azure services.<br>
<a href="https://mackerel.io/docs/entry/integrations/azure">Azure Integration</a>

## Flexible Server
Integration for Flexible Server can be done by selecting "Database for MySQL Flexible Server" in "Metric collecting service" section of the Azure Integration Settings screen.

### Obtaining metrics
The following metrics can be retrieved for Database for MySQL flexible servers. A maximum of 36 metrics will be retrieved.
The actual metrics obtained may be reduced depending on the server SKU and role. For `Metric` explanations, please refer to the <a href="https://docs.microsoft.com/en-us/azure/mysql/flexible-server/concepts-monitoring" target="_blank" rel="noreferrer">Azure documentation</a>.

|Graph names|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|Backup Storage used|backup_storage_used|azure.db_for_mysql.backup_storage_used.bytes|bytes|Average|
|Connections|active_connections<br>aborted_connections<br>total_connections|azure.db_for_mysql.connections.active<br>azure.db_for_mysql.connections.aborted<br>azure.db_for_mysql.connections.total|float|Average<br>Total<br>Total|
|CPU credits Remaining/Consumed|cpu_credits_remaining<br>cpu_credits_consumed|azure.db_for_mysql.cpu_credits.remaining<br>azure.db_for_mysql.cpu_credits.consumed|float|Average|
|CPU percent|cpu_percent|azure.db_for_mysql.cpu.percent|percentage|Average|
|HA IO/SQL Status|HA_IO_status<br>HA_SQL_status|azure.db_for_mysql.ha_status.io<br>azure.db_for_mysql.ha_status.sql|float|Average|
|InnoDB Buffer Pool Reads/Requests Count|Innodb_buffer_pool_reads<br>Innodb_buffer_pool_read_requests|azure.db_for_mysql.innodb_buffer_pool_read.reads<br>azure.db_for_mysql.innodb_buffer_pool_read.requests|float|Total|
|IO percent|io_consumption_percent|azure.db_for_mysql.io.percent|percentage|Average|
|Memory percent|memory_percent|azure.db_for_mysql.memory.percent|percentage|Average|
|MySQL DDL Command|Com_create_db<br>Com_drop_db<br>Com_create_table<br>Com_drop_table<br>Com_alter_table|azure.db_for_mysql.cmd_ddl.Com_create_db<br>azure.db_for_mysql.cmd_ddl.Com_drop_db<br>azure.db_for_mysql.cmd_ddl.Com_create_table<br>azure.db_for_mysql.cmd_ddl.Com_drop_table<br>azure.db_for_mysql.cmd_ddl.Com_alter_table|float|Total|
|MySQL DML Command|Com_select<br>Com_update<br>Com_insert<br>Com_delete|azure.db_for_mysql.cmd_dml.Com_select<br>azure.db_for_mysql.cmd_dml.Com_update<br>azure.db_for_mysql.cmd_dml.Com_insert<br>azure.db_for_mysql.cmd_dml.Com_delete|float|Total|
|Network In/Out|network_bytes_egress<br>network_bytes_ingress|azure.db_for_mysql.network.out<br>azure.db_for_mysql.network.in|bytes|Total|
|Total/Slow Queries|Queries<br>Slow_queries|azure.db_for_mysql.queries.total<br>azure.db_for_mysql.queries.slow|float|Total|
|Replica IO/SQL Running|Replica_IO_Running<br>Replica_SQL_Running|db_for_mysql.replica_running.io<br>db_for_mysql.replica_running.sql|float|Average|
|Replication lag in seconds|replication_lag|db_for_mysql.replication_lag|seconds|Maximum|
|Server Log storage limit/used|serverlog_storage_limit<br>serverlog_storage_usage|azure.db_for_mysql.server_log_storage_limit_used.limit<br>azure.db_for_mysql.server_log_storage_limit_used.used|bytes|Maximum<br>Average|
|Server Log storage percent|serverlog_storage_percent|azure.db_for_mysql.server_log_storage.percent|percentage|Average|
|Storage IO count|storage_io_count|azure.db_for_mysql.storage_io_count|float|Total|
|Storage limit/used|storage_limit<br>storage_used|azure.db_for_mysql.storage_limit_used.limit<br>azure.db_for_mysql.storage_limit_used.used|bytes|Maximum<br>Average|
|Storage percent|storage_percent|azure.db_for_mysql.storage.percent|percentage|Average|

### Notes
- Of the graphs / metrics that can be obtained with Azure integration listed above, Backup Storage used is normally obtained with a 15-minute interval granularity according to the specifications of the Azure Monitor API used by Azure integration. Also, updates can have a latency of up to 30 minutes.

## Single Server
Integration for Single Server can be done by selecting "Database for MySQL Single Server" in "Metric collecting service" section of the Azure Integration Settings screen.
The old Database for MySQL integration settings have been transferred to Database for MySQL Single Server.

### Obtaining metrics
The metrics obtainable with Azure Integration’s Database for MySQL support vary by price level.
The metrics for each price level are as follows. For `Metric` explanations, please refer to the <a href="https://docs.microsoft.com/en-us/azure/mysql/single-server/concepts-monitoring" target="_blank" rel="noreferrer">Azure documentation</a>. 

#### Basic 
The maximum number of metrics obtainable is 13.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|Active/Failed Connections|active_connections<br>connections_failed|azure.db_for_mysql.connections.active<br>azure.db_for_mysql.connections.failed|float|Average<br>Total|
|Backup Storage used|backup_storage_used|azure.db_for_mysql.backup_storage_used.bytes|bytes|Average|
|CPU percent|cpu_percent|azure.db_for_mysql.cpu.percent|percentage|Average|
|Memory percent|memory_percent|azure.db_for_mysql.memory.percent|percentage|Average|
|Network In/Out|network_bytes_egress<br>network_bytes_ingress|azure.db_for_mysql.network.out<br>azure.db_for_mysql.network.in|bytes|Total|
|Server Log storage limit/used|serverlog_storage_limit<br>serverlog_storage_usage|azure.db_for_mysql.server_log_storage_limit_used.limit<br>azure.db_for_mysql.server_log_storage_limit_used.used|bytes|Maximum<br>Average|
|Server Log storage percent|serverlog_storage_percent|azure.db_for_mysql.server_log_storage.percent|percentage|Average|
|Storage limit/used|storage_limit<br>storage_used|azure.db_for_mysql.storage_limit_used.limit<br>azure.db_for_mysql.storage_limit_used.used|bytes|Maximum<br>Average|
|Storage percent|storage_percent|azure.db_for_mysql.storage.percent|percentage|Average|

#### General Purpose
The maximum number of metrics obtainable is 15.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|Active/Failed Connections|active_connections<br>connections_failed|azure.db_for_mysql.connections.active<br>azure.db_for_mysql.connections.failed|float|Average<br>Total|
|Backup Storage used|backup_storage_used|azure.db_for_mysql.backup_storage_used.bytes|bytes|Average|
|CPU percent|cpu_percent|azure.db_for_mysql.cpu.percent|percentage|Average|
|IO percent|io_consumption_percent|azure.db_for_mysql.io.percent|percentage|Average|
|Memory percent|memory_percent|azure.db_for_mysql.memory.percent|percentage|Average|
|Network In/Out|network_bytes_egress<br>network_bytes_ingress|azure.db_for_mysql.network.out<br>azure.db_for_mysql.network.in|bytes|Total|
|Replication lag in seconds|seconds_behind_master|azure.db_for_mysql.replication_lag.seconds|float|Maximum|
|Server Log storage limit/used|serverlog_storage_limit<br>serverlog_storage_usage|azure.db_for_mysql.server_log_storage_limit_used.limit<br>azure.db_for_mysql.server_log_storage_limit_used.used|bytes|Maximum<br>Average|
|Server Log storage percent|serverlog_storage_percent|azure.db_for_mysql.server_log_storage.percent|percentage|Average|
|Storage limit/used|storage_limit<br>storage_used|azure.db_for_mysql.storage_limit_used.limit<br>azure.db_for_mysql.storage_limit_used.used|bytes|Maximum<br>Average|
|Storage percent|storage_percent|azure.db_for_mysql.storage.percent|percentage|Average|

#### Memory Optimized
The maximum number of metrics obtainable is 15.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|Active/Failed Connections|active_connections<br>connections_failed|azure.db_for_mysql.connections.active<br>azure.db_for_mysql.connections.failed|float|Average<br>Total|
|Backup Storage used|backup_storage_used|azure.db_for_mysql.backup_storage_used.bytes|bytes|Average|
|CPU percent|cpu_percent|azure.db_for_mysql.cpu.percent|percentage|Average|
|IO percent|io_consumption_percent|azure.db_for_mysql.io.percent|percentage|Average|
|Memory percent|memory_percent|azure.db_for_mysql.memory.percent|percentage|Average|
|Network In/Out|network_bytes_egress<br>network_bytes_ingress|azure.db_for_mysql.network.out<br>azure.db_for_mysql.network.in|bytes|Total|
|Replication lag in seconds|seconds_behind_master|azure.db_for_mysql.replication_lag.seconds|float|Maximum|
|Server Log storage limit/used|serverlog_storage_limit<br>serverlog_storage_usage|azure.db_for_mysql.server_log_storage_limit_used.limit<br>azure.db_for_mysql.server_log_storage_limit_used.used|bytes|Maximum<br>Average|
|Server Log storage percent|serverlog_storage_percent|azure.db_for_mysql.server_log_storage.percent|percentage|Average|
|Storage limit/used|storage_limit<br>storage_used|azure.db_for_mysql.storage_limit_used.limit<br>azure.db_for_mysql.storage_limit_used.used|bytes|Maximum<br>Average|
|Storage percent|storage_percent|azure.db_for_mysql.storage.percent|percentage|Average|

### Notes
- Of the graphs / metrics that can be obtained with Azure integration listed above, Backup Storage used is normally obtained with a 15-minute interval granularity according to the specifications of the Azure Monitor API used by Azure integration. Also, updates can have a latency of up to 30 minutes.

- seconds_behind_master only counts up on replica servers.<br>
As a result, Replication lag in seconds is not graphed in the master server.


