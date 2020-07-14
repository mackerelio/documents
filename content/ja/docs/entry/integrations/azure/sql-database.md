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
各価格レベルで取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://msdn.microsoft.com/library/en-us/Mt163593.aspx" target="_blank">Azureのドキュメント</a>をご確認ください。

## DTUベース

### Basic, Standard, Premium
最大で16個のメトリックが取得されます。

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
|Data space used|storage|azure.sql_database.storage.used|bytes|Maximum|
|Data space used percent|storage_percent|azure.sql_database.storage_percent.percent|percentage|Maximum|
|Workers|workers_percent|azure.sql_database.workers.percent|percentage|Average|
|In-Memory OLTP Storage|xtp_storage_percent|azure.sql_database.xtp_storage.percent|percentage|Average|

## 仮想コアベース

### General Purpose
最大で15個のメトリックが取得されます。

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
|Data space used|storage|azure.sql_database.storage.used|bytes|Maximum|
|Data space used percent|storage_percent|azure.sql_database.storage_percent.percent|percentage|Maximum|
|Workers|workers_percent|azure.sql_database.workers.percent|percentage|Average|
|In-Memory OLTP Storage|xtp_storage_percent|azure.sql_database.xtp_storage.percent|percentage|Average|

General Purposeでのみサポートされているサーバーレスデータベースの場合は、上記のメトリックに加えて以下のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
|:---|:---|:---|:---|:---|
|App CPU billed|app_cpu_billed|azure.sql_database.app_cpu_billed.billed|integer|Total|
|App CPU percentage|app_cpu_percent|azure.sql_database.app_cpu_percent.percent|percentage|Average|
|App memory used percentage|app_memory_percent|azure.sql_database.app_memory_percent.percent|percentage|Average|

### Business Critical
最大で15個のメトリックが取得されます。

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
|Data space used|storage|azure.sql_database.storage.used|bytes|Maximum|
|Data space used percent|storage_percent|azure.sql_database.storage_percent.percent|percentage|Maximum|
|Workers|workers_percent|azure.sql_database.workers.percent|percentage|Average|
|In-Memory OLTP Storage|xtp_storage_percent|azure.sql_database.xtp_storage.percent|percentage|Average|

### Hyperscale
最大で13個のメトリックが取得されます。

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
|Workers|workers_percent|azure.sql_database.workers.percent|percentage|Average|
|In-Memory OLTP Storage|xtp_storage_percent|azure.sql_database.xtp_storage.percent|percentage|Average|