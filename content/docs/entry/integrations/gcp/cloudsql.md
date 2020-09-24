---
Title: Google Cloud Integration - Cloud SQL
Date: 2020-09-10T12:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/gcp/cloudsql
EditURL: 
---

Mackerel supports obtaining and monitoring <a href="https://cloud.google.com/sql" target="_blank">Cloud SQL</a> metrics with Google Cloud Integration. When integrating with Google Cloud Integration, billable targets are determined using the conversion 1 Instance = 1 Micro Host. In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for Google Cloud Integration configuration methods and a list of supported services.<br>
<a href="https://mackerel.io/docs/entry/integrations/gcp">Google Cloud Integration</a>

## Required API
The following API must be enabled in order to integrate Cloud SQL.

- Cloud SQL Admin API(sqladmin.googleapis.com)

## Required role
The following role is required for the service account in order integrate Cloud SQL.

- Cloud SQL Viewer(roles/cloudsql.viewer)

## Obtaining metrics
The metrics obtainable with Google Cloud Integration’s Cloud SQL support are as follows. For `Metric` explanations, refer to the <a href="https://cloud.google.com/monitoring/api/metrics_gcp" target="_blank">Google Cloud documentation</a>.

### CloudSQL for MySQL

The maximum number of obtainable metrics is `27`.

|Graph name|Index type|Metric name in Mackerel|Unit|
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

### CloudSQL for PostgreSQL

The maximum number of obtainable metrics is `15 + 3 × [database name] + 4 × [replica name]`

|Graph name|Index type|Metric name in Mackerel|Unit|
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
|PostgreSQL backends|database/postgresql/num_backends|cloudsql.postgresql.backends.[database name].count|integer|
|PostgreSQL replica byte lag|database/postgresql/replication/replica_byte_lag|cloudsql.postgresql.replica_byte_lag.[replica name].[latency type].bytes|bytes|
|PostgreSQL transaction|database/postgresql/transaction_count|cloudsql.postgresql.transaction.[database name].[transaction type].count|integer|
|CloudSQL replication lag|database/replication/replica_lag|cloudsql.replication.lag|integer|
|Uptime|database/uptime|cloudsql.uptime.seconds|integer|

* Enter the PostgreSQL database name in place of [database name] in "Metric name on Mackerel".
* Enter either `replay_location`,` flush_location`, `write_location`, or ` sent_location` in place of [latency type] in "Metric name on Mackerel".
* Enter the replica name in place of [replica name] in "Metric name on Mackerel".
* Enter either `commit` or `rollback` in place of [transaction type] in "Metric Name on Mackerel".
