---
Title: Azure Integration - Database for MySQL
Date: 2020-04-30T14:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/azure/database-for-mysql
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613592628895
CustomPath: integrations/azure/database-for-mysql
---

Mackerel supports obtaining and monitoring <a href="https://azure.microsoft.com/en-us/services/mysql/" target="_blank">Database for MySQL</a> metrics in Azure Integration. Billable targets are determined using the conversion 1 resource = 1 Micro Host.  (Currently the only supported deployment option is "Single server")

Please refer to the following page for Azure Integration configuration methods and a list of supported Azure services.<br>
<a href="https://mackerel.io/docs/entry/integrations/azure">Azure Integration</a>

## Obtaining metrics
The metrics obtainable with Azure Integration’s Database for MySQL support vary by price level.
The metrics for each price level are as follows. For `Metric` explanations, please refer to the <a href="https://docs.microsoft.com/en-us/azure/mysql/concepts-monitoring" target="_blank">Azure documentation</a>. 

### Basic 
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

### General Purpose
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

### Memory Optimized
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

## Notes
- Of the graphs / metrics that can be obtained with Azure integration listed above, Backup Storage used is normally obtained with a 15-minute interval granularity according to the specifications of the Azure Monitor API used by Azure integration. Also, updates can have a latency of up to 30 minutes.

- seconds_behind_master only counts up on replica servers.<br>
As a result, Replication lag in seconds is not graphed in the master server.


