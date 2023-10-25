---
Title: Azureインテグレーション - Database for PostgreSQL
Date: 2020-05-25T14:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/azure/database-for-postgresql
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613591881119
CustomPath: integrations/azure/database-for-postgresql
---

MackerelはAzureインテグレーションにて<a href="https://azure.microsoft.com/ja-jp/services/postgresql/" target="_blank" rel="noreferrer">Database for PostgreSQL</a>のメトリック取得や監視に対応しています。課金対象として 1リソース = 1マイクロホスト と換算します。 (現在サポートしているデプロイオプションは「シングルサーバー」「フレキシブルサーバー」のみとなります)

Azureインテグレーションの設定方法や対応Azureサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/azure">Azureインテグレーション</a>

## フレキシブルサーバー
インテグレーション設定画面の「メトリックを収集するサービス」にてDatabase for PostgreSQL Flexible Serverを選択することで設定できます。

### 取得メトリック
Database for PostgreSQLフレキシブルサーバーで取得できるメトリックは以下の通りです。

最大で34個（うち拡張メトリック16個）のメトリックが取得されます。
拡張メトリックはサーバーパラメーターmetrics.collector_database_activityをONにすることで取得可能になります。
実際に取得されるメトリックはサーバーのSKUやロールによって異なる場合があります。


 `メトリック` の説明や、拡張メトリックを有効にする方法に関しては<a href="https://learn.microsoft.com/ja-jp/azure/postgresql/flexible-server/concepts-monitoring" target="_blank" rel="noreferrer">Azureのドキュメント</a>をご確認ください。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|拡張メトリック|
|:---|:---|:---|:---|:---|:---|
|Availability|is_db_alive|azure.db_for_postgresql.availability.db|float|Average|x|
|Backends|numbackends|azure.db_for_postgresql.backends.count|float|Maximum|x|
|Backup Storage used|backup_storage_used|azure.db_for_postgresql.backup_storage_used.bytes|bytes|Average||
|Connections|active_connections<br>connections_succeeded<br>connections_failed|azure.db_for_postgresql.connections.active<br>azure.db_for_postgresql.connections.succeeded<br>azure.db_for_postgresql.connections.failed|float|Average<br>Total<br>Total||
|CPU credits Remaining/Consumed|cpu_credits_remaining<br>cpu_credits_consumed|azure.db_for_postgresql.cpu_credits.remaining<br>azure.db_for_postgresql.cpu_credits.consumed|float|Average||
|CPU percent|cpu_percent|azure.db_for_postgresql.cpu.percent|percentage|Average||
|Deadlocks|deadlocks|azure.db_for_postgresql.deadlocks.count|float|Total|x|
|Disk Bandwidth Consumed|disk_bandwidth_consumed_percentage|azure.db_for_postgresql.disk_bandwidth_consumed.percent|percentage|Average|x|
|Disk IOPS|read_iops<br>write_iops|azure.db_for_postgresql.disk_iops.read<br>azure.db_for_postgresql.disk_iops.write|iops|Average||
|Disk IOPS Consumed|disk_iops_consumed_percentage|azure.db_for_postgresql.disk_ios_consumed.percent|percentage|Average|x|
|Disk Throughput|read_throughput<br>write_throughput|azure.db_for_postgresql.disk_throughput.read<br>azure.db_for_postgresql.disk_throughput.write|bytes/sec|Average||
|Logical Replication Lag|logical_replication_delay_in_bytes|azure.db_for_postgresql.logical_replication_lag_bytes.max|bytes|Maximum|x|
|Memory percent|memory_percent|azure.db_for_postgresql.memory.percent|percentage|Average||
|Network In/Out|network_bytes_egress<br>network_bytes_ingress|azure.db_for_postgresql.network.out<br>azure.db_for_postgresql.network.in|bytes|Total||
|Operated Tuples by Queries|tup_deleted<br>tup_fetched<br>tup_inserted<br>tup_returned<br>tup_updated|azure.db_for_postgresql.tuples.deleted<br>azure.db_for_postgresql.tuples.fetched<br>azure.db_for_postgresql.tuples.inserted<br>azure.db_for_postgresql.tuples.returned<br>azure.db_for_postgresql.tuples.updated|float|Total|x|
|Physical Replication Lag|physical_replication_delay_in_bytes|azure.db_for_postgresql.replication_lag_bytes.max|bytes|Maximum|x|
|Read Replica Lag|physical_replication_delay_in_seconds|azure.db_for_postgresql.replication_lag.seconds|seconds|Maximum|x|
|Storage percent|storage_percent|azure.db_for_postgresql.storage.percent|percentage|Average||
|Storage limit/used|storage_limit<br>storage_used|azure.db_for_postgresql.storage_limit_used.limit<br>azure.db_for_postgresql.storage_limit_used.used|bytes|Maximum<br>Average||
|Transactions|xact_total<br>xact_commit<br>xact_rollback|azure.db_for_postgresql.transactions.total<br>azure.db_for_postgresql.transactions.commit<br>azure.db_for_postgresql.transactions.rollback|float|Total|x|
|Used Transaction IDs|maximum_used_transactionIDs|azure.db_for_postgresql.used_transaction_ids.max|float|Max||

### 注意事項
- Azureインテグレーションにより取得可能な上記のグラフ・メトリックのうち、価格レベルがBasicの場合でのみ取得されるBackup Storage usedについては、Azureインテグレーションが利用しているAzure Monitor APIの仕様により、通常、15分間隔粒度でのメトリックが取得されます。また、更新は最大で30分遅延します。

## シングルサーバー
インテグレーション設定画面の「メトリックを収集するサービス」にてDatabase for PostgreSQL Single Serverを選択することで設定できます。
旧来のDatabase for PostgreSQLのインテグレーション設定はDatabase for PostgreSQL Single Serverに引き継がれました。

### 取得メトリック
Database for PostgreSQLシングルサーバーのインテグレーションで取得できるメトリックは価格レベルによって異なります。
各価格レベルで取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://docs.microsoft.com/ja-jp/azure/postgresql/single-server/concepts-monitoring" target="_blank" rel="noreferrer">Azureのドキュメント</a>をご確認ください。

#### Basic 
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

#### General Purpose
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

#### Memory Optimized
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

### 注意事項
- Azureインテグレーションにより取得可能な上記のグラフ・メトリックのうち、価格レベルがBasicの場合でのみ取得されるBackup Storage usedについては、Azureインテグレーションが利用しているAzure Monitor APIの仕様により、通常、15分間隔粒度でのメトリックが取得されます。また、更新は最大で30分遅延します。<br>
加えて、レプリカサーバではグラフは作成されません。

- pg_replica_log_delay_in_bytesはマスターサーバでのみカウントアップするメトリックです。<br>
そのため、レプリカサーバではMax Lag Across Replicasのグラフは作成されません。

- pg_replica_log_delay_in_secondsはレプリカサーバでのみカウントアップするメトリックです。<br>
そのため、マスタサーバではReplica Lagのグラフは作成されません。
