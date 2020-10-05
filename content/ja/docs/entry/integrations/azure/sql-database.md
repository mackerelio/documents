---
Title: Azureインテグレーション - SQL Database
Date: 2017-05-18T16:26:16+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/azure/sql-database
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10328749687248185094
---

MackerelはAzureインテグレーションにて<a href="https://azure.microsoft.com/ja-jp/services/sql-database/" target="_blank">SQL Database</a>のメトリック取得や監視に対応しています。課金対象として 1インスタンス = 1マイクロホスト と換算します。

Azureインテグレーションの設定方法や対応Azureサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/azure">Azureインテグレーション</a>

## 取得メトリック
AzureインテグレーションのSQL Database対応で取得できるメトリックは価格レベルによって異なります。
各価格レベルで取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://docs.microsoft.com/ja-jp/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases" target="_blank">Azureのドキュメント</a>をご確認ください。

## DTUベース

### Basic, Standard, Premium
最大で21個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
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

## 仮想コアベース

### General Purpose
最大で20個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
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

General Purposeでのみサポートされているサーバーレスデータベースの場合は、上記のメトリックに加えて以下のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
|:---|:---|:---|:---|:---|
|App CPU billed|app_cpu_billed|azure.sql_database.app_cpu_billed.billed|integer|Total|
|App CPU percentage|app_cpu_percent|azure.sql_database.app_cpu_percent.percent|percentage|Average|
|App memory used percentage|app_memory_percent|azure.sql_database.app_memory_percent.percent|percentage|Average|

### Business Critical
最大で20個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
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
最大で18個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
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

## 注意事項
Azure SQL Databaseでサポートされているエラスティックプール内のデータベースでは、以下のメトリックは取得されません。

- sqlserver_process_core_percent
- sqlserver_process_memory_percent
- tempdb_data_size
- tempdb_log_size
- tempdb_log_used_percent

また、DTUベースのSQL Databaseをご利用で、ハードウェアの世代がGen 4かつ200 DTU未満の場合、上記のメトリックは取得できません。<br>
これは、Azureのプラットフォーム上の制約によるものです。<br>

ご利用の環境については、以下の方法でご確認ください。<br>

- DTU
  - Azure Portalの対象リソースの概要から確認することができます。
- ハードウェアの世代
  - 対象のSQL Databaseにて下記のクエリを実行することで、そのハードウェア世代を確認することができます。

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

なお、ハードウェアの世代がGen 4のハードウェアについては、Azureの計画メンテンナンスにおいて順次Gen 5に置き換えられています。<br>
現在当該メトリックが表示されないSQL Databaseについても、将来表示されるようになる可能性があります。
