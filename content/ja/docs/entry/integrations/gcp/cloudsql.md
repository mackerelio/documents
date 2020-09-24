---
Title: Google Cloudインテグレーション - Cloud SQL
Date: 2020-09-10T12:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/gcp/cloudsql
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613631816655
---

MackerelはGoogle Cloudインテグレーションにて<a href="https://cloud.google.com/sql" target="_blank">Cloud SQL</a>のメトリック取得や監視に対応しています。
Google Cloudインテグレーションで連携をおこなった場合、課金対象として 1インスタンス = 1マイクロホスト と換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

Google Cloudインテグレーションの設定方法や対応済みGoogle Cloudサービスの一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/gcp">Google Cloudインテグレーション</a>

## 必要なAPI
Cloud SQL連携を利用するためには、以下のAPIが有効になっている必要があります。

- Cloud SQL Admin API(sqladmin.googleapis.com)

## 必要なロール
Cloud SQL連携を利用するためには、サービスアカウントに以下のロールが必要です。

- Cloud SQL 閲覧者(roles/cloudsql.viewer)

## 取得メトリック
Google CloudインテグレーションのCloud SQL対応で取得できるメトリックは以下の通りです。`メトリック` の説明に関しては<a href="https://cloud.google.com/monitoring/api/metrics_gcp" target="_blank">Google Cloudのドキュメント</a>をご確認ください。

### Cloud SQL for MySQLの場合

最大で `27` 個のメトリックが取得されます。

|グラフ名|指標タイプ|Mackerel上のメトリック名|単位|
|:---|:---|:---|:---|
|Auto failover request count|database/auto_failover_request_count|cloudsql.database.auto_failover.request_count|integer|
|CPU|database/cpu/utilization|cloudsql.database.cpu.used|percentage|
|Disk bytes|database/disk/bytes_used<br>database/disk/quota|cloudsql.disk.bytes.used<br>cloudsql.disk.bytes.quota|bytes|
|Disk utilization|database/disk/utilization|cloudsql.disk.utilization.utilization|percentage|
|Disk ops count|database/disk/read_ops_count<br>database/disk/write_ops_count|cloudsql.disk.ops.read<br>cloudsql.disk.ops.write|integer|
|Memory|database/memory/usage<br>database/memory/quota|cloudsql.memory.usage<br>cloudsql.memory.quota|bytes|
|Memory utilization|database/memory/utilization|cloudsql.memory_utilization.utilization|percentage|
|Innodb buffer pool|database/mysql/innodb_buffer_pool_pages_dirty<br>database/mysql/innodb_buffer_pool_pages_free<br>database/mysql/innodb_buffer_pool_pages_total|cloudsql.mysql.innodb_buffer_pool_pages.dirty<br>cloudsql.mysql.innodb_buffer_pool_pages.free<br>cloudsql.mysql.innodb_buffer_pool_pages.total|integer|
|Innodb fsyncs|database/mysql/innodb_data_fsyncs<br>database/mysql/innodb_os_log_fsyncs|cloudsql.mysql.innodb_fsyncs.data<br>cloudsql.mysql.innodb_fsyncs.os_log|integer|
|Innodb pages|database/mysql/innodb_pages_read<br>database/mysql/innodb_pages_written|cloudsql.mysql.innodb_pages.read<br>cloudsql.mysql.innodb_pages.write|integer|
|Queries|database/mysql/queries<br>database/mysql/questions|cloudsql.mysql.queries.query<br>cloudsql.mysql.queries.question|integer|
|MySQL bytes received and sent|database/mysql/received_bytes_count<br>database/mysql/sent_bytes_count|cloudsql.mysql.bytes.received<br>cloudsql.mysql.bytes.snet|bytes|
|Seconds behind master|database/mysql/replication/seconds_behind_master|cloudsql.mysql.replication.seconds_behind_master.seconds|integer|
|Connections|database/network/connections|cloudsql.network.connections.count|integer|
|Network bytes count|database/network/received_bytes_count<br>database/network/sent_bytes_count|cloudsql.network.bytes.received<br>cloudsql.network.bytes.sent|bytes|
|CloudSQL replication lag|database/replication/replica_lag|cloudsql.replication.lag|integer|
|Uptime|database/uptime|cloudsql.uptime.seconds|integer|

### Cloud SQL for PostgreSQLの場合

最大で `15 + 3 × [データベース名] + 4 × [レプリカ名]` 個のメトリックが取得されます。

|グラフ名|指標タイプ|Mackerel上のメトリック名|単位|
|:---|:---|:---|:---|
|Auto failover request count|database/auto_failover_request_count|cloudsql.database.auto_failover.request_count|integer|
|CPU|database/cpu/utilization|cloudsql.database.cpu.used|percentage|
|Disk bytes|database/disk/bytes_used<br>database/disk/quota|cloudsql.disk.bytes.used<br>cloudsql.disk.bytes.quota|bytes|
|Disk utilization|database/disk/utilization|cloudsql.disk.utilization.utilization|percentage|
|Disk ops count|database/disk/read_ops_count<br>database/disk/write_ops_count|cloudsql.disk.ops.read<br>cloudsql.disk.ops.write|integer|
|Memory|database/memory/usage<br>database/memory/quota|cloudsql.memory.usage<br>cloudsql.memory.quota|bytes|
|Memory utilization|database/memory/utilization|cloudsql.memory_utilization.utilization|percentage|
|Connections|database/network/connections|cloudsql.network.connections.count|integer|
|Network bytes count|database/network/received_bytes_count<br>database/network/sent_bytes_count|cloudsql.network.bytes.received<br>cloudsql.network.bytes.sent|bytes|
|PostgreSQL backends|database/postgresql/num_backends|cloudsql.postgresql.backends.[データベース名].count|integer|
|PostgreSQL replica byte lag|database/postgresql/replication/replica_byte_lag|cloudsql.postgresql.replica_byte_lag.[レプリカ名].[遅延の種類].bytes|bytes|
|PostgreSQL transaction|database/postgresql/transaction_count|cloudsql.postgresql.transaction.[データベース名].[トランザクションタイプ].count|integer|
|CloudSQL replication lag|database/replication/replica_lag|cloudsql.replication.lag|integer|
|Uptime|database/uptime|cloudsql.uptime.seconds|integer|

* "Mackerel上のメトリック名"の[データベース名]にはPostgreSQLデータベースの名前が入ります。
* "Mackerel上のメトリック名"の[遅延の種類]には`replay_location`, `flush_location`, `write_location`, `sent_location`が入ります。
* "Mackerel上のメトリック名"の[レプリカ名]にはレプリカの名前が入ります。
* "Mackerel上のメトリック名"の[トランザクションタイプ]には`commit`または`rollback`が入ります。
