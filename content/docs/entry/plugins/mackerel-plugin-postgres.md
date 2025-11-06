---
Title: Metric plugins - mackerel-plugin-postgres
Date: 2023-05-02T12:15:46+09:00
URL: https://mackerel.io/docs/entry/plugins/mackerel-plugin-postgres
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/4207575160645264248
---

mackerel-plugin-postgres is a plugin that posts PostgreSQL statistical status as a metric.
Depending on the version of PostgreSQL being monitored, additional roles may be required for users connecting to PostgreSQL. For more information, see [User roles required to run the plugin](https://mackerel.io/docs/entry/plugins/mackerel-plugin-postgres#role).

[:contents]

<h2 id="metrics">Monitorable metrics</h2>

This plugin posts PostgreSQL statistics as a metric. For statistics, please refer to the PostgreSQL documentation.

[https://www.postgresql.org/docs/current/:embed:cite]

### Postgres Connections

Post [pg_stat_activity](https://www.postgresql.org/docs/14/monitoring-stats.html#MONITORING-PG-STAT-ACTIVITY-VIEW) information as a metric.

| Metric Display Name | Metric Name                        | Diff | Stacked | Description                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Active | postgres.connections.active |  | ✓ | Number of active connections |
| Active waiting | postgres.connections.active_waiting |  | ✓ | Number of connections with active and waiting events |
| Idle | postgres.connections.idle |  | ✓ | Number of idle connections |
| Idle in transaction | postgres.connections.idle_in_transaction |  | ✓ | Number of connections in "Idle in transaction" |
| Idle in transaction (aborted) | postgres.connections.idle_in_transaction_aborted_ |  | ✓ | Number of connections in "Idle in transaction" state with errors during the transaction |
| fast-path function call | postgres.connections.fastpath_function_call |  | ✓ | Number of connections running the fast-path function |
| Disabled | postgres.connections.disabled |  | ✓ | Number of connections in disabled state |

- Disabled will be posted if track_activities is disabled.


### Postgres Commits

Post [pg_stat_database](https://www.postgresql.org/docs/14/monitoring-stats.html#MONITORING-PG-STAT-DATABASE-VIEW) information as a metric.

| Metric Display Name | Metric Name                        | Diff | Stacked | Description                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Xact Commit | postgres.commits.xact_commit | ✓ |  | Number of transactions committed in the database per minute |
| Xact Rollback | postgres.commits.xact_rollback | ✓ |  | Number of transactions rolled back in the database per minute |


### Postgres Blocks

Post [pg_stat_database](https://www.postgresql.org/docs/14/monitoring-stats.html#MONITORING-PG-STAT-DATABASE-VIEW) information as a metric.

| Metric Display Name | Metric Name                        | Diff | Stacked | Description                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Blocks Read | postgres.blocks.blks_read | ✓ |  | Number of blocks read from disk per minute |
| Blocks Hit | postgres.blocks.blks_hit | ✓ |  | Number of blocks read from cache per minute |


### Postgres Rows

Post [pg_stat_database](https://www.postgresql.org/docs/14/monitoring-stats.html#MONITORING-PG-STAT-DATABASE-VIEW) information as a metric.

| Metric Display Name | Metric Name                        | Diff | Stacked | Description                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Returned Rows | postgres.rows.tup_returned | ✓ |  | Number of rows returned by a query in the database per minute |
| Fetched Rows | postgres.rows.tup_fetched | ✓ | ✓ | Number of rows fetched by query in the database per minute |
| Inserted Rows | postgres.rows.tup_inserted | ✓ | ✓ | Number of rows inserted by a query in the database per minute |
| Updated Rows | postgres.rows.tup_updated | ✓ | ✓ | Number of rows updated by a query in the database per minute |
| Deleted Rows | postgres.rows.tup_deleted | ✓ | ✓ | Number of rows deleted by a query in the database per minute |


### Postgres Data Size

Post the results of running `SELECT sum(pg_database_size(datname)) from pg_database` as a metric.

| Metric Display Name | Metric Name                        | Diff | Stacked | Description                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Total Size | postgres.size.total_size |  |  | Total size of all databases, in bytes |


### Postgres Dead Locks

Post [pg_stat_database](https://www.postgresql.org/docs/14/monitoring-stats.html#MONITORING-PG-STAT-DATABASE-VIEW) information as a metric.

| Metric Display Name | Metric Name                        | Diff | Stacked | Description                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Deadlocks | postgres.deadlocks.deadlocks | ✓ |  | Number of deadlocks detected in the database per minute |


### Postgres Block I/O time

Post [pg_stat_database](https://www.postgresql.org/docs/14/monitoring-stats.html#MONITORING-PG-STAT-DATABASE-VIEW) information as a metric.

track_io_timing must be enabled to post this metric. If the setting is disabled, post 0.

| Metric Display Name | Metric Name                        | Diff | Stacked | Description                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Block Read Time (ms) | postgres.iotime.blk_read_time | ✓ |  | Time spent reading data file block per minute, in milliseconds |
| Block Write Time (ms) | postgres.iotime.blk_write_time | ✓ |  | Time spent writing data file block per minute, in milliseconds |


### Postgres Temporary file

Post [pg_stat_database](https://www.postgresql.org/docs/14/monitoring-stats.html#MONITORING-PG-STAT-DATABASE-VIEW) information as a metric.

| Metric Display Name | Metric Name                        | Diff | Stacked | Description                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Temporary file size (byte) | postgres.tempfile.temp_bytes | ✓ |  | Total size of data written to temporary files per minute, in bytes |


### Postgres Amount of Transaction location change

Post information about WAL as a metric. To post this metric, set wal_level in pg_settings to logical. Otherwise, post 0.

| Metric Display Name | Metric Name                        | Diff | Stacked | Description                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Amount of Transaction location change (byte) | postgres.xlog_location.xlog_location_bytes | ✓ |  | Size of WAL per minute, in bytes |

The value of the metric is calculated by the following query.

- PostgreSQL version 10 or higher
  - `SELECT pg_wal_lsn_diff(pg_current_wal_lsn(), '0/0')`
- PostgreSQL version 9.2 or higher
  - `SELECT pg_xlog_location_diff(pg_current_xlog_location(), '0/0')`


<h2 id="options">Configurable options</h2>

| Option             | Required  | Description                                                                                                  | Default       |
| ----------------- | --- | --------------------------------------------------------------------------------------------------- | --------- |
| -hostname          |     | Target host                                                                                            | localhost |
| -port              |     | Connection port                                                                                             | 5432      |
| -user              | ✓   | Postgres User                                                                                               |           |
| -password          |     | Postgres Password                                                                                               |           |
| -database          |     | Database name                                                                                           | postgres  |
| -metric-key-prefix |     | Specifies the prefix of the metric name                                                                                   | postgres  |
| -sslmode           |     | Whether or not to use SSL                                                                                         | disable   |
| -connect_timeout   |     | Maximum wait for connection, in seconds                                                                                   | 5 (seconds)      |
| -tempfile          |     | Specify the destination file path for tempfile |           |
| -h, -help          |     | Show help                                                                                              |           |

- If you specify a user other than postgres for user, you must specify the database to connect to with the -database option. 
  - The -database option is not an option that retrieves information only for the specified database.
- The tempfile records the latest execution results. By default, it is created under `/var/tmp/mackerel-agent` as `mackerel-plugin-postgres-<hash string>`

<h2 id="config">Example configuration</h2>

```toml
[plugin.metrics.postgres]
command = ["mackerel-plugin-postgres", "-user", "username", "-password", "password"]
```

<h2 id="role">User roles required to run the plugin</h2>

The Postgres user connecting to the database must have the following roles.

- PostgreSQL version 10 or higher
  - pg_monitor
- PostgreSQL version 9.6 or lower
  - Not require


<h2 id="tips">Tips</h2>

### How to write PostgreSQL connection information

If you want to avoid putting the connection information needed to run mackerel-plugin-postgres directly in the --password option, you can take the following approaches. Note that these approaches require that the contents of command be written as a string, not an array.

**Use the command**

If you specify a string, the contents of command will be executed via the shell, so you can use the command to output the password.

mackerel-agent.conf
```toml
[plugin.metrics.postgres]
command = "mackerel-plugin-postgres -user username -password <command to output password>"
```

**Use environment variables of check monitoring**

If an environment variable is defined in the env of check monitoring, the password can be written to the environment variable. For more information on env, see [Configuration items](https://mackerel.io/docs/entry/custom-checks#items).

mackerel-agent.conf
```toml
[plugin.metrics.postgres]
command = "mackerel-plugin-postgres -user username -password $PG_PASSWORD"
env = { "PG_PASSWORD" = "password" }
```

**Use environment variables of mackerel-agent**

Environment variables applied to mackerel-agent are also available.

The following is an example configuration in a Linux environment.

/etc/sysconfig/mackerel-agent
```
PG_PASSWORD=password
```

mackerel-agent.conf
```toml
[plugin.metrics.postgres]
command = "mackerel-plugin-postgres -user username -password $PG_PASSWORD"
```

<h2 id="repository">Repository</h2>

[https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-postgres]
