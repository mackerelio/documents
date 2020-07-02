---
Title: Azure Integration - Database for PostgreSQL
Date: 2020-05-25T14:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/azure/database-for-postgresql
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613592629022
CustomPath: integrations/azure/database-for-postgresql
---

Mackerel supports obtaining and monitoring <a href="https://azure.microsoft.com/en-us/services/postgresql/" target="_blank">Database for PostgreSQL</a> metrics in Azure Integration. Billable targets are determined using the conversion 1 resource = 1 Micro Host.

Please refer to the following page for Azure Integration configuration methods and a list of supported Azure services.<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/azure">Azure Integration</a>

## Obtaining metrics
The metrics obtainable with Azure Integration’s Database for PostgreSQL support vary by price level.
The metrics for each price level are as follows. For `Metric` explanations, please refer to the <a href="https://docs.microsoft.com/en-us/azure/postgresql/concepts-monitoring" target="_blank">Azure documentation</a>. 

### Basic 
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

### General Purpose
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

### Memory Optimized
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

## Notes
- Of the graphs / metrics that can be obtained with Azure integration listed above, when obtaining Backup Storage used at the Basic price level, it is done so according to the specifications of the Azure Monitor API used by Azure integration, normally with a 15-minute interval granularity and updates having a latency of up to 30 minutes.<br>
Additionally, it is not graphed in replica servers.

- pg_replica_log_delay_in_bytes only counts up on the master server.<br>
As a result, Max Lag Across Replicas is not graphed in replica servers.

- pg_replica_log_delay_in_seconds only counts up on replica servers.<br>
As a result, Replica Lag is not graphed in the master server.
