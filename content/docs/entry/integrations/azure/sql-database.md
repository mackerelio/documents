---
Title: Azure Integration - SQL Database
Date: 2017-06-26T11:50:36+09:00
URL: https://mackerel.io/docs/entry/integrations/azure/sql-database
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812274112027
---

Mackerel supports obtaining and monitoring <a href="https://azure.microsoft.com/en-us/services/sql-database/" target="_blank">SQL Database</a> metrics in Azure Integration.

Please refer to the following page for Azure Integration configuration methods and a list of supported Azure services. <br>
<a href="https://mackerel.io/docs/entry/integrations/azure">Azure Integration</a>

## Obtaining metrics
The metrics obtainable with Azure Integration’s SQL Database support are as follows. For `Metric` explanations, refer to the <a href="https://msdn.microsoft.com/library/en-us/Mt163593.aspx" target="_blank">Azure help page</a>.


|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|CPU|cpu_percent|azure.sql_database.cpu_percent|Percent|Average|
|Data Read|physical_data_read_percent|azure.sql_database.physical_data_read_percent|Percent|Average|
|Log Write|log_write_percent|azure.sql_database.log_write_percent|Percent|Average|
|DTU|dtu_used|azure.sql_database.dtu.used|Count|Average|
|DTU Consumption|dtu_consumption_percent|azure.sql_database.dtu_consumption_percent|Percent|Average|
|In-Memory OLTP storage|xtp_storage_percent|azure.sql_database.xtp_storage_percent|Percent|Average|
|Connections|connection_successful<br>connection_failed<br>blocked_by_firewall|azure.sql_database.connection.successful<br>azure.sql_database.connection.failed<br>azure.sql_database.connection.blocked_by_firewall|Count|Total|
|Deadlocks|deadlock|azure.sql_database.deadlock_count|Count|Total|
|Workers|workers_percent|azure.sql_database.workers_percent|Percent|Average|
|Sessions|sessions_percent|azure.sql_database.sessions_percent|Percent|Average|
