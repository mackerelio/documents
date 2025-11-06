---
Title: Metric plugins - mackerel-plugin-mssql
Date: 2023-04-07T11:04:57+09:00
URL: https://mackerel.io/docs/entry/plugins/mackerel-plugin-mssql
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/4207112889978758667
---

mackerel-plugin-mssql is a plugin that posts Microsoft SQL Server information as a metric.

[:contents]

<h2 id="support-version">SQL Server versions supported by the plugin</h2>

- Microsoft SQL Server 2017 or later
- Microsoft SQL Express 2017 or later


<h2 id="metrics">Monitorable metrics</h2>

Post counter values for SQL Server objects.

[https://learn.microsoft.com/en-us/sql/relational-databases/performance-monitor/use-sql-server-objects?view=sql-server-ver16#SQLServerPOs:embed:cite]


### MSSQL Buffer

Information about the memory buffers used by SQL Server. Get information on [Buffer Manager object](https://learn.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object?view=sql-server-ver16).

| Metric Display Name | Metric Name                       | Diff | Stacked | Description                                         |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Cache Hit Ratio | mssql.buffer_cache_hit_ratio |  | ✓| Percentage of pages found in the buffer cache without reading from disk |
| Page Life Expectancy | mssql.buffer_page_life_expectancy | ✓ | ✓ | Number of seconds a page remains in the buffer pool without referenced |
| Checkpoint Pages | mssql.buffer_checkpoint_pages |  | ✓ | Number of pages flushed to disk per second by a checkpoint or other operation that require all dirty pages to be flushed |


### MSSQL Stats

Information on SQL Server usage. Get information on [SQL Statistics object](https://learn.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-sql-statistics-object?view=sql-server-ver16) and [General Statistics object](https://learn.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-general-statistics-object?view=sql-server-ver16).

| Metric Display Name | Metric Name                       | Diff | Stacked | Description                                         |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Batch Requests | mssql.stats_batch_requests | ✓ | ✓ | Number of Transact-SQL command batches received per second |
| SQL Compilations | mssql.stats_sql_compilations | ✓ | ✓ | SQL compiles per second |
| SQL Re-Compilations | mssql.stats_sql_recompilations | ✓ | ✓ | Number of statement recompilations per second |
| Connections | mssql.stats_connections |  | ✓ | Number of users currently connected to SQL Server |
| Lock Waits | mssql.stats_lock_waits |  | ✓ | Number of waits for the File IO Provider lock per second |
| Procs Blocked | mssql.stats_procs_blocked |  | ✓ | Number of processes currently blocked |


### MSSQL Access

Get information on [Access Methods object](https://learn.microsoft.com/en-us/sql/relational-databases/performance-monitor/sql-server-access-methods-object?view=sql-server-ver16).

| Metric Display Name | Metric Name                       | Diff | Stacked | Description                                         |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Page Splits | mssql.access_page_splits | ✓ | ✓ | Number of page splits that occurred per second as a result of index page overflow. |


<h2 id="options">Configurable options</h2>

| Option | Short | Description | Default  |
| --- | --- | --- | --- | 
| -metric-key-prefix |  | Specifies the prefix of the metric name | mssql |
| -instance |  | Specify the instance name as either SQLSERVER or SQLEXPRESS. Other names are not supported. | SQLSERVER |
| -tempfile |  | Specify the destination file path for tempfile |  |

The tempfile is created by default under `C:\Windows\Temp` in the format `mackerel-plugin-mssql-<hash string>`.


<h2 id="config">Example configuration</h2>

The following is an example configuration for an instance name of SQLEXPRESS.

```toml
[plugin.metrics.mssql]
command = ["mackerel-plugin-mssql", "-instance", "SQLEXPRESS"]
```

<h2 id="repository">Repository</h2>

[https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-mssql]
