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
The maximum number of metrics obtainable is 16.

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
|Data space used|storage|azure.sql_database.storage.used|bytes|Maximum|
|Data space used percent|storage_percent|azure.sql_database.storage_percent.percent|percentage|Maximum|
|Workers|workers_percent|azure.sql_database.workers.percent|percentage|Average|
|In-Memory OLTP Storage|xtp_storage_percent|azure.sql_database.xtp_storage.percent|percentage|Average|

## vCore based model

### General Purpose
The maximum number of metrics obtainable is 15.

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
|Data space used|storage|azure.sql_database.storage.used|bytes|Maximum|
|Data space used percent|storage_percent|azure.sql_database.storage_percent.percent|percentage|Maximum|
|Workers|workers_percent|azure.sql_database.workers.percent|percentage|Average|
|In-Memory OLTP Storage|xtp_storage_percent|azure.sql_database.xtp_storage.percent|percentage|Average|

In addition to the metrics above, the following metrics are retrieved for serverless databases that are supported for General Purpose only.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|App CPU billed|app_cpu_billed|azure.sql_database.app_cpu_billed.billed|integer|Total|
|App CPU percentage|app_cpu_percent|azure.sql_database.app_cpu_percent.percent|percentage|Average|
|App memory used percentage|app_memory_percent|azure.sql_database.app_memory_percent.percent|percentage|Average|

### Business Critical
The maximum number of metrics obtainable is 15.

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
|Data space used|storage|azure.sql_database.storage.used|bytes|Maximum|
|Data space used percent|storage_percent|azure.sql_database.storage_percent.percent|percentage|Maximum|
|Workers|workers_percent|azure.sql_database.workers.percent|percentage|Average|
|In-Memory OLTP Storage|xtp_storage_percent|azure.sql_database.xtp_storage.percent|percentage|Average|

### Hyperscale
The maximum number of metrics obtainable is 13.

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
|Workers|workers_percent|azure.sql_database.workers.percent|percentage|Average|
|In-Memory OLTP Storage|xtp_storage_percent|azure.sql_database.xtp_storage.percent|percentage|Average|
