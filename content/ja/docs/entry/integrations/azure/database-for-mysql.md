---
Title: Azureインテグレーション - Database for MySQL
Date: 2020-04-30T14:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/azure/database-for-mysql
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613591880945
CustomPath: integrations/azure/database-for-mysql
---

MackerelはAzureインテグレーションにて<a href="https://azure.microsoft.com/ja-jp/services/mysql/" target="_blank">Database for MySQL</a>のメトリック取得や監視に対応しています。課金対象として 1リソース = 1マイクロホスト と換算します。

Azureインテグレーションの設定方法や対応Azureサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/azure">Azureインテグレーション</a>

## 取得メトリック
AzureインテグレーションのDatabase for MySQL対応で取得できるメトリックは価格レベルによって異なります。
各価格レベルで取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://docs.microsoft.com/ja-jp/azure/mysql/concepts-monitoring" target="_blank">Azureのドキュメント</a>をご確認ください。

### Basic 
最大で13個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
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
最大で15個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
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
最大で15個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
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

## 注意事項
- Azureインテグレーションにより取得可能な上記のグラフ・メトリックのうち、Backup Storage usedについては、Azureインテグレーションが利用しているAzure Monitor APIの仕様により、通常、15分間隔粒度でのメトリックが取得されます。また、更新は最大で30分遅延します。

- seconds_behind_masterはレプリカサーバでのみカウントアップするメトリックです。<br>
そのため、マスタサーバではReplication lag in secondsのグラフは作成されません。
