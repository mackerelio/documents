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
AzureインテグレーションのSQL Database対応で取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://msdn.microsoft.com/library/en-us/Mt163593.aspx" target="_blank">Azureのドキュメント</a>をご確認ください。

最大で12個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
|:---|:---|:---|:---|:---|
|CPU|cpu_percent|azure.sql_database.cpu_percent|percentage|Average|
|Data Read|physical_data_read_percent|azure.sql_database.physical_data_read_percent|percentage|Average|
|Log Write|log_write_percent|azure.sql_database.log_write_percent|percentage|Average|
|DTU|dtu_used|azure.sql_database.dtu.used|float|Average|
|DTU Consumption|dtu_consumption_percent|azure.sql_database.dtu_consumption_percent|percentage|Average|
|In-Memory OLTP storage|xtp_storage_percent|azure.sql_database.xtp_storage_percent|percentage|Average|
|Connections|connection_successful<br>connection_failed<br>blocked_by_firewall|azure.sql_database.connection.successful<br>azure.sql_database.connection.failed<br>azure.sql_database.connection.blocked_by_firewall|integer|Total|
|Deadlocks|deadlock|azure.sql_database.deadlock_count|integer|Total|
|Workers|workers_percent|azure.sql_database.workers_percent|percentage|Average|
|Sessions|sessions_percent|azure.sql_database.sessions_percent|percentage|Average|
