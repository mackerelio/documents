---
Title: Azure Integration - SQL Database
Date: 2017-06-26T11:50:36+09:00
URL: https://mackerel.io/docs/entry/integrations/azure/sql-database
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812274112027
---

Mackerel supports obtaining and monitoring <a href="https://azure.microsoft.com/en-us/services/sql-database/" target="_blank">SQL Database</a> metrics in Azure Integration. When integrating with Azure Integration, billable targets are determined using the conversion 1 Instance = 1 Micro Host.

Please refer to the following page for Azure Integration configuration methods and a list of supported Azure services. <br>
<a href="https://mackerel.io/docs/entry/integrations/azure">Azure Integration</a>

## Obtaining metrics
The metrics available with Azure Integration SQL Database support vary by pricing tier. The metrics that can be obtained at each tier are as follows. For `Metric` explanations, refer to the <a href="https://docs.microsoft.com/en-us/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases" target="_blank">Azure help page</a>.

## DTU based model

### Basic, Standard, Premium
The maximum number of metrics obtainable is 21.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|Data space allocated|allocated_data_storage|azure.sql_database.allocated_data_storage.allocated_data|bytes|Average|
|Connections|blocked_by_firewall<br>connection_failed<br>connection_successful|azure.sql_database.connection.blocked_by_firewall<br>azure.sql_database.connection.failed<br>azure.sql_database.connection.successful|integer|Total|
|CPU|cpu_percent|azure.sql_database.cpu.percent|percentage|Average|
|Deadlocks|deadlock|azure.sql_database.deadlock.count|integer|Total|
|DTU Consumption|dtu_consumption_percent|azure.sql_database.dtu_consumption.percent|percentage|Average|
|DTU|dtu_limit<br>dtu_used|azure.sql_database.dtu.limit<br>azure.sql_database.dtu.used|float|Average|
|Log Write|log_write_percent|azure.sql_database.log_write.percent|percentage|Average|
|Data Read|physical_data_read_percent|azure.sql_database.physical_data_read.percent|percentage|Average|
|Sessions|sessions_percent|azure.sql_database.sessions.percent|percentage|Average|
|SQL Server process core percent|sqlserver_process_core_percent|azure.sql_database.sqlserver_process_core_percent.percent|percentage|Maximum|
|SQL Server process memory percent|sqlserver_process_memory_percent|azure.sql_database.sqlserver_process_memory_percent.percent|percentage|Maximum|
|Data space used|storage|azure.sql_database.storage.used|bytes|Maximum|
|Data space used percent|storage_percent|azure.sql_database.storage_percent.percent|percentage|Maximum|
|Tempdb Data File Size|tempdb_data_size|azure.sql_database.tempdb_data_size.data_size|bytes|Maximum|
|Tempdb Log File Size|tempdb_log_size|azure.sql_database.tempdb_log_size.log_size|bytes|Maximum|
|Tempdb Percent Log Used|tempdb_log_used_percent|azure.sql_database.tempdb_log_used_percent.percent|percentage|Maximum|
|Workers|workers_percent|azure.sql_database.workers.percent|percentage|Average|
|In-Memory OLTP Storage|xtp_storage_percent|azure.sql_database.xtp_storage.percent|percentage|Average|

## vCore based model

### General Purpose
The maximum number of metrics obtainable is 20.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|Data space allocated|allocated_data_storage|azure.sql_database.allocated_data_storage.allocated_data|bytes|Average|
|Connections|blocked_by_firewall<br>connection_failed<br>connection_successful|azure.sql_database.connection.blocked_by_firewall<br>azure.sql_database.connection.failed<br>azure.sql_database.connection.successful|integer|Total|
|CPU limit/used|cpu_limit<br>cpu_used|azure.sql_database.cpu_limit_used.limit<br>azure.sql_database.cpu_limit_used.used|float|Average|
|CPU|cpu_percent|azure.sql_database.cpu.percent|percentage|Average|
|Deadlocks|deadlock|azure.sql_database.deadlock.count|integer|Total|
|Log Write|log_write_percent|azure.sql_database.log_write.percent|percentage|Average|
|Data Read|physical_data_read_percent|azure.sql_database.physical_data_read.percent|percentage|Average|
|Sessions|sessions_percent|azure.sql_database.sessions.percent|percentage|Average|
|SQL Server process core percent|sqlserver_process_core_percent|azure.sql_database.sqlserver_process_core_percent.percent|percentage|Maximum|
|SQL Server process memory percent|sqlserver_process_memory_percent|azure.sql_database.sqlserver_process_memory_percent.percent|percentage|Maximum|
|Data space used|storage|azure.sql_database.storage.used|bytes|Maximum|
|Data space used percent|storage_percent|azure.sql_database.storage_percent.percent|percentage|Maximum|
|Tempdb Data File Size|tempdb_data_size|azure.sql_database.tempdb_data_size.data_size|bytes|Maximum|
|Tempdb Log File Size|tempdb_log_size|azure.sql_database.tempdb_log_size.log_size|bytes|Maximum|
|Tempdb Percent Log Used|tempdb_log_used_percent|azure.sql_database.tempdb_log_used_percent.percent|percentage|Maximum|
|Workers|workers_percent|azure.sql_database.workers.percent|percentage|Average|
|In-Memory OLTP Storage|xtp_storage_percent|azure.sql_database.xtp_storage.percent|percentage|Average|

In addition to the metrics above, the following metrics are retrieved for serverless databases that are supported for General Purpose only.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|App CPU billed|app_cpu_billed|azure.sql_database.app_cpu_billed.billed|integer|Total|
|App CPU percentage|app_cpu_percent|azure.sql_database.app_cpu_percent.percent|percentage|Average|
|App memory used percentage|app_memory_percent|azure.sql_database.app_memory_percent.percent|percentage|Average|

### Business Critical
The maximum number of metrics obtainable is 20.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|Data space allocated|allocated_data_storage|azure.sql_database.allocated_data_storage.allocated_data|bytes|Average|
|Connections|blocked_by_firewall<br>connection_failed<br>connection_successful|azure.sql_database.connection.blocked_by_firewall<br>azure.sql_database.connection.failed<br>azure.sql_database.connection.successful|integer|Total|
|CPU limit/used|cpu_limit<br>cpu_used|azure.sql_database.cpu_limit_used.limit<br>azure.sql_database.cpu_limit_used.used|float|Average|
|CPU|cpu_percent|azure.sql_database.cpu.percent|percentage|Average|
|Deadlocks|deadlock|azure.sql_database.deadlock.count|integer|Total|
|Log Write|log_write_percent|azure.sql_database.log_write.percent|percentage|Average|
|Data Read|physical_data_read_percent|azure.sql_database.physical_data_read.percent|percentage|Average|
|Sessions|sessions_percent|azure.sql_database.sessions.percent|percentage|Average|
|SQL Server process core percent|sqlserver_process_core_percent|azure.sql_database.sqlserver_process_core_percent.percent|percentage|Maximum|
|SQL Server process memory percent|sqlserver_process_memory_percent|azure.sql_database.sqlserver_process_memory_percent.percent|percentage|Maximum|
|Data space used|storage|azure.sql_database.storage.used|bytes|Maximum|
|Data space used percent|storage_percent|azure.sql_database.storage_percent.percent|percentage|Maximum|
|Tempdb Data File Size|tempdb_data_size|azure.sql_database.tempdb_data_size.data_size|bytes|Maximum|
|Tempdb Log File Size|tempdb_log_size|azure.sql_database.tempdb_log_size.log_size|bytes|Maximum|
|Tempdb Percent Log Used|tempdb_log_used_percent|azure.sql_database.tempdb_log_used_percent.percent|percentage|Maximum|
|Workers|workers_percent|azure.sql_database.workers.percent|percentage|Average|
|In-Memory OLTP Storage|xtp_storage_percent|azure.sql_database.xtp_storage.percent|percentage|Average|

### Hyperscale
The maximum number of metrics obtainable is 18.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|Data space allocated|allocated_data_storage|azure.sql_database.allocated_data_storage.allocated_data|bytes|Average|
|Connections|blocked_by_firewall<br>connection_failed<br>connection_successful|azure.sql_database.connection.blocked_by_firewall<br>azure.sql_database.connection.failed<br>azure.sql_database.connection.successful|integer|Total|
|CPU limit/used|cpu_limit<br>cpu_used|azure.sql_database.cpu_limit_used.limit<br>azure.sql_database.cpu_limit_used.used|float|Average|
|CPU|cpu_percent|azure.sql_database.cpu.percent|percentage|Average|
|Deadlocks|deadlock|azure.sql_database.deadlock.count|integer|Total|
|Log Write|log_write_percent|azure.sql_database.log_write.percent|percentage|Average|
|Data Read|physical_data_read_percent|azure.sql_database.physical_data_read.percent|percentage|Average|
|Sessions|sessions_percent|azure.sql_database.sessions.percent|percentage|Average|
|SQL Server process core percent|sqlserver_process_core_percent|azure.sql_database.sqlserver_process_core_percent.percent|percentage|Maximum|
|SQL Server process memory percent|sqlserver_process_memory_percent|azure.sql_database.sqlserver_process_memory_percent.percent|percentage|Maximum|
|Tempdb Data File Size|tempdb_data_size|azure.sql_database.tempdb_data_size.data_size|bytes|Maximum|
|Tempdb Log File Size|tempdb_log_size|azure.sql_database.tempdb_log_size.log_size|bytes|Maximum|
|Tempdb Percent Log Used|tempdb_log_used_percent|azure.sql_database.tempdb_log_used_percent.percent|percentage|Maximum|
|Workers|workers_percent|azure.sql_database.workers.percent|percentage|Average|
|In-Memory OLTP Storage|xtp_storage_percent|azure.sql_database.xtp_storage.percent|percentage|Average|

## Notes
Databases within Azure SQL Database supported elastic pools will not obtain the following metrics.

- sqlserver_process_core_percent
- sqlserver_process_memory_percent
- tempdb_data_size
- tempdb_log_size
- tempdb_log_used_percent

These metrics are also unobtainable if using a DTU-based SQL Database with Gen 4 hardware and less than 200 DTU. <br>
This is due to Azure platform restrictions.

Use the following method to check your environment.

- DTU
  - DTU can be checked in the Azure portal's target resource overview.
- Hardware generation
  - The generation can be checked by running the following query in the target's SQL Database.


```
SELECT TOP (1) rg.slo_name,
  CASE 
    WHEN rg.slo_name LIKE '%SQLG4%' THEN 'Gen4'
    WHEN rg.slo_name LIKE '%SQLGZ%' THEN 'Gen4'
    WHEN rg.slo_name LIKE '%SQLG5%' THEN 'Gen5'
    WHEN rg.slo_name LIKE '%SQLG6%' THEN 'Gen5'
 END AS dtu_hardware_gen
FROM sys.dm_user_db_resource_governance AS rg
```

Furthermore, Gen 4 hardware is gradually being replaced with Gen 5 through Azure maintenance. <br>
SQL Databases that do not currently display the corresponding metrics may display them in the future.
