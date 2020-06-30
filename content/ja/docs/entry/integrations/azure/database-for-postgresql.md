---
Title: Azureインテグレーション - Database for PostgreSQL
Date: 2020-05-25T14:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/azure/database-for-postgresql
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613591881119
CustomPath: integrations/azure/database-for-postgresql
---

MackerelはAzureインテグレーションにて<a href="https://azure.microsoft.com/ja-jp/services/postgresql/" target="_blank">Database for PostgreSQL</a>のメトリック取得や監視に対応しています。課金対象として 1リソース = 1マイクロホスト と換算します。

Azureインテグレーションの設定方法や対応Azureサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/azure">Azureインテグレーション</a>

## 取得メトリック
AzureインテグレーションのDatabase for PostgreSQL対応で取得できるメトリックは価格レベルによって異なります。
各価格レベルで取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://docs.microsoft.com/ja-jp/azure/postgresql/concepts-monitoring" target="_blank">Azureのドキュメント</a>をご確認ください。

### Basic 
最大で15個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
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
最大で15個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
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
最大で15個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
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

## 注意事項
- Azureインテグレーションにより取得可能な上記のグラフ・メトリックのうち、価格レベルがBasicの場合でのみ取得されるBackup Storage usedについては、Azureインテグレーションが利用しているAzure Monitor APIの仕様により、通常、15分間隔粒度でのメトリックが取得されます。また、更新は最大で30分遅延します。<br>
加えて、レプリカサーバではグラフは作成されません。

- pg_replica_log_delay_in_bytesはマスターサーバでのみカウントアップするメトリックです。<br>
そのため、レプリカサーバではMax Lag Across Replicasのグラフは作成されません。

- pg_replica_log_delay_in_secondsはレプリカサーバでのみカウントアップするメトリックです。<br>
そのため、マスタサーバではReplica Lagのグラフは作成されません。
