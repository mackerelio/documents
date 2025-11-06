---
Title: Metric plugins - mackerel-plugin-mysql
Date: 2023-07-26T17:10:46+09:00
URL: https://mackerel.io/docs/entry/plugins/mackerel-plugin-mysql
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/820878482952941545
---

mackerel-plugin-mysql can monitor various metrics about MySQL, such as the number of queries executed.  
The installation method for the plugin varies depending on the version of MySQL. Please check the [Plugin installation instructions](#install) before using it.

[:contents]

<h2 id="install">Plugin installation instructions</h2>

<h3 id="install-new">For MySQL 5.7 and later, MySQL 8.0 and later</h3>

Please follow the official plugin installation procedures as usual.

- [Using the official plugin pack to visualize middleware metrics](https://mackerel.io/docs/entry/howto/mackerel-agent-plugins)

<h3 id="install-old">For versions older than the above</h3>

Please install using the following steps with the mkr command.

```
sudo mkr plugin install mackerelio/mackerel-plugin-mysql@v1.0.0
```

For more information about the mkr command, please refer to the following.

- [Using the mkr CLI tool](https://mackerel.io/docs/entry/advanced/cli)
- [Using mkr plugin install](https://mackerel.io/docs/entry/advanced/install-plugin-by-mkr)

<h2 id="metrics">Metrics you can monitor</h2>

<h3 id="standard-metrics">Standard metrics</h3>

#### MySQL Command

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ---------------- | ----------------------------------- | - | - | --------------------------------------------------- |
| Insert           | custom.mysql.cmd.Com_insert         | ✓ | ✓ | Number of INSERT executions                         |
| Insert Select    | custom.mysql.cmd.Com_insert_select  | ✓ | ✓ | Number of INSERT ... SELECT executions              |
| Select           | custom.mysql.cmd.Com_select         | ✓ | ✓ | Number of SELECT executions (excluding query cache) |
| Update           | custom.mysql.cmd.Com_update         | ✓ | ✓ | Number of UPDATE executions                         |
| Update Multi     | custom.mysql.cmd.Com_update_multi   | ✓ | ✓ | Number of UPDATE executions for multi-table syntax  |
| Delete           | custom.mysql.cmd.Com_delete         | ✓ | ✓ | Number of DELETE executions                         |
| Delete Multi     | custom.mysql.cmd.Com_delete_multi   | ✓ | ✓ | Number of DELETE executions in multi-table syntax   |
| Replace          | custom.mysql.cmd.Com_replace        | ✓ | ✓ | Number of REPLACE executions                        |
| Replace Select   | custom.mysql.cmd.Com_replace_select | ✓ | ✓ | Number of REPLACE ... SELECT executions             |
| Load             | custom.mysql.cmd.Com_load           | ✓ | ✓ | Number of LOAD executions                           |
| Set Option       | custom.mysql.cmd.Com_set_option     | ✓ | ✓ | Number of SET OPTION executions                     |
| Query Cache Hits | custom.mysql.cmd.Qcache_hits        | ✓ |   | Number of query cache hits                          |
| Questions        | custom.mysql.cmd.Questions          | ✓ |   | Number of statements executed by the server         |

- The value of each variable is taken as a metric from the results of `SHOW /*!50002 GLOBAL */ STATUS`.

#### MySQL Join/Scan

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ---------------------- | ---------------------------------------- | - | - | ------------------------------------------------------------------ |
| Select Full JOIN       | custom.mysql.join.Select_full_join       | ✓ |   | Number of JOINs with table scans without using index |
| Select Full Range JOIN | custom.mysql.join.Select_full_range_join | ✓ |   | Number of JOINs using range lookups on referenced tables |
| Select Range           | custom.mysql.join.Select_range           | ✓ |   | Number of JOINs where a range of the first table was used |
| Select Range Check     | custom.mysql.join.Select_range_check     | ✓ |   | Number of JOINs without index keys |
| Select SCAN            | custom.mysql.join.Select_scan            | ✓ |   | Number of JOINs where a full scan was performed on the first table |

- The value of each variable is taken as a metric from the results of `SHOW /*!50002 GLOBAL */ STATUS`.

#### MySQL Threads

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ---------- | -------------------------------------- | - | - | ------------------------------------ |
| Cache Size | custom.mysql.threads.thread_cache_size |   |   | Thread cache size |
| Connected  | custom.mysql.threads.Threads_connected |   |   | Number of connections currently open |
| Running    | custom.mysql.threads.Threads_running   |   |   | Number of active threads |
| Created    | custom.mysql.threads.Threads_created   | ✓ |   | Number of new threads created |
| Cached     | custom.mysql.threads.Threads_cached    |   |   | Number of threads in thread cache |

- `thread_cache_size` is the value of the variable from the result of `SHOW VARIABLES` as a metric.
- The value of each variable is taken as a metric from the results of `SHOW /*!50002 GLOBAL */ STATUS`.

#### MySQL Connections

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| -------------------- | --------------------------------------------- | - | - | ---------------------------------------------------------------------------------------------------- |
| Max Connections      | custom.mysql.connections.max_connections      |   |   | Maximum number of simultaneous connections                                                           |
| Max Used Connections | custom.mysql.connections.Max_used_connections |   |   | Maximum number of simultaneous connections during operation                                          |
| Connections          | custom.mysql.connections.Connections          | ✓ |   | Number of connection attempts                                                                        |
| Threads Connected    | custom.mysql.connections.Threads_connected    |   |   | Number of connections currently open                                                                 |
| Aborted Clients      | custom.mysql.connections.Aborted_clients      | ✓ |   | Number of connections aborted because the client terminated without properly closing the connection  |
| Aborted Connects     | custom.mysql.connections.Aborted_connects     | ✓ |   | Number of failed connection attempts to MySQL Server                                                 |

- `max_connections` is the value of the variable from the result of `SHOW VARIABLES` as a metric.
- The value of each variable is taken as a metric from the results of `SHOW /*!50002 GLOBAL */ STATUS`.

#### MySQL Slave status

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| --------------------- | -------------------------------------------------------- | - | - | -------------------------------------------------------------------------- |
| Seconds Behind Master | custom.mysql.seconds_behind_master.Seconds_Behind_Master |   |   | Time difference between slave SQL thread and slave I/O thread (in seconds) |

- The result of `SHOW SLAVE STATUS` is used as the metric.

#### MySQL Table Locks/Slow Queries

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| --------------------- | ---------------------------------------------- | - | - | ------------------------------------------------------------------------ |
| Table Locks Immediate | custom.mysql.table_locks.Table_locks_immediate | ✓ |   | Number of times a table lock was acquired immediately                    |
| Table Locks Waited    | custom.mysql.table_locks.Table_locks_waited    | ✓ |   | Number of times table locks were blocked and required waiting to acquire |
| Slow Queries          | custom.mysql.table_locks.Slow_queries          | ✓ |   | Number of queries that took longer than "long_query_time" seconds        |

- The value of each variable is taken as a metric from the results of `SHOW /*!50002 GLOBAL */ STATUS`.

#### MySQL Traffic

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| -------------------- | ----------------------------------- | - | - | ----------------------------------------- |
| Sent Bytes           | custom.mysql.traffic.Bytes_sent     | ✓ |   | Number of bytes sent to all clients       |
| Bytes Received Bytes | custom.mysql.traffic.Bytes_received | ✓ |   | Number of bytes received from all clients |

- The value of each variable is taken as a metric from the result of `SHOW /*!50002 GLOBAL */ STATUS`.

#### MySQL Capacity

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ------------------------- | --------------------------------------------- | - | - | ----------------------------------  |
| Percentage Of Connections | custom.mysql.capacity.PercentageOfConnections |   |   | Percentage of connections utilized  |
| Percentage Of Buffer Pool | custom.mysql.capacity.PercentageOfBufferPool  |   |   | Percentage of buffer pools utilized |

- The metric is calculated from the results of `SHOW VARIABLES` and `SHOW /*!50002 GLOBAL */ STATUS`.
  - The percentage of connections is calculated by `Threads_connected / max_connections`.
  - The percentage of buffer pools is calculated by `database_pages / Innodb_buffer_pool_pages_total`.
- If `--disable_innodb=true` is specified in the plugin options, the buffer pool percentage is not output.

<h3 id="innodb-metrics">Metrics about InnoDB</h3>

Metrics about InnoDB are enabled by default, but can be disabled with the `--disable_innodb=true` option.

#### MySQL innodb Rows

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| -------- | --------------------------------------------- | - | - | --------------------------------------------- |
| Read     | custom.mysql.innodb_rows.Innodb_rows_read     | ✓ |   | Number of rows read from InnoDB table         |
| Inserted | custom.mysql.innodb_rows.Innodb_rows_inserted | ✓ |   | Number of rows inserted into the InnoDB table |
| Updated  | custom.mysql.innodb_rows.Innodb_rows_updated  | ✓ |   | Number of rows updated in the InnoDB table    |
| Deleted  | custom.mysql.innodb_rows.Innodb_rows_deleted  | ✓ |   | Number of rows deleted from the InnoDB table  |

- The value of each variable is taken as a metric from the result of `SHOW /*!50002 GLOBAL */ STATUS`.

#### MySQL innodb Row Lock Time

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| --------- | ------------------------------------------------------ | - | - | -------------------------------------------------------------------------- |
| Lock Time | custom.mysql.innodb_row_lock_time.Innodb_row_lock_time | ✓ |   | Total time (in milliseconds) spent acquiring a row lock on an InnoDB table |

- The value of each variable is taken as a metric from the result of `SHOW /*!50002 GLOBAL */ STATUS`.

#### MySQL innodb Row Lock Waits

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ---------- | -------------------------------------------------------- | - | - | -------------------------------------------------------------------- |
| Lock Waits | custom.mysql.innodb_row_lock_waits.Innodb_row_lock_waits | ✓ |   | Number of times an operation on an InnoDB table waits for a row lock |

- The value of each variable is taken as a metric from the result of `SHOW /*!50002 GLOBAL */ STATUS`.

#### MySQL innodb Adaptive Hash Index

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ---------------------- | -------------------------------------------------------------- | - | - | ---------------------------------------- |
| Hash Index Cells Total | custom.mysql.innodb_adaptive_hash_index.hash_index_cells_total |   |   | Total Adaptive Hash Index cells          |
| Hash Index Cells Used  | custom.mysql.innodb_adaptive_hash_index.hash_index_cells_used  |   |   | Amount of Adaptive Hash Index cells used |

- The `INSERT BUFFER AND ADAPTIVE HASH INDEX` entry from the results of `SHOW /*!50000 ENGINE*/ INNODB STATUS` is used as the metric.
- `hash_index_cells_used` is no longer available in MySQL 5.1.28 and later. It is handled differently depending on the plugin version as follows.
  - mackerel-plugin-mysql v1.2.1 or later does not output metrics.
  - By mackerel-plugin-mysql v1.2.0, it will always be `0`.

#### MySQL innodb Buffer Pool Read (/sec)

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ---------------------- | ------------------------------------------------------ | - | - | ------------------------------------------------------------------------------------------ |
| Pages Read Ahead       | custom.mysql.innodb_buffer_pool_read.read_ahead        | ✓ |   | Number of pages per second read into the InnoDB buffer pool                                |
| Evicted Without Access | custom.mysql.innodb_buffer_pool_read.read_evicted      | ✓ |   | Number of pages per second read into the InnoDB buffer pool that were erased due to unused |
| Random Read Ahead      | custom.mysql.innodb_buffer_pool_read.read_random_ahead | ✓ |   | Number of random reads per second by InnoDB (MySQL 5.7 or later)                           |
 
- The values of the following variables from the results of `SHOW /*!50002 GLOBAL */ STATUS` are used as the metric.
  - Innodb_buffer_pool_read_ahead
  - Innodb_buffer_pool_read_ahead_evicted
  - Innodb_buffer_pool_read_ahead_rnd

#### MySQL innodb Buffer Pool Activity (Pages)

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ------- | ------------------------------------------------------ | - | - | -------------------------------------------------- |
| Created | custom.mysql.innodb_buffer_pool_activity.pages_created | ✓ |   | Number of pages created by InnoDB table operations |
| Read    | custom.mysql.innodb_buffer_pool_activity.pages_read    | ✓ |   | Number of pages read by InnoDB table operations    |
| written | custom.mysql.innodb_buffer_pool_activity.pages_written | ✓ |   | Number of pages written by InnoDB table operations |

- The values of the following variables are used as metrics from the results of `SHOW /*!50002 GLOBAL */ STATUS`.
  - Innodb_pages_created
  - Innodb_pages_read
  - Innodb_pages_written

#### MySQL innodb Buffer Pool Efficiency

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ------------- | --------------------------------------------------------------------------- | - | - | ------------------------------------------------ |
| Reads         | custom.mysql.innodb_buffer_pool_efficiency.Innodb_buffer_pool_reads         | ✓ |   | Number of direct reads without using buffer pool |
| Read Requests | custom.mysql.innodb_buffer_pool_efficiency.Innodb_buffer_pool_read_requests | ✓ |   | Number of logical read requests                  |

- The value of each variable is taken as a metric from the result of `SHOW /*!50002 GLOBAL */ STATUS`.

#### MySQL innodb Buffer Pool (Pages)

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| --------- | ---------------------------------------------- | - | - | --------------------------------------------------------- |
| Pool Size | custom.mysql.innodb_buffer_pool.pool_size      |   |   | Total InnoDB buffer pool size (in pages)                  |
| Used      | custom.mysql.innodb_buffer_pool.database_pages |   |   | Number of pages in the InnoDB buffer pool containing data |
| Free      | custom.mysql.innodb_buffer_pool.free_pages     |   |   | Number of free pages in InnoDB buffer pool                |
| Modified  | custom.mysql.innodb_buffer_pool.modified_pages |   |   | Current number of dirty pages in the InnoDB buffer pool   |

- The values of the following variables from the results of `SHOW /*!50002 GLOBAL */ STATUS` are used as the metric.
  - Innodb_buffer_pool_pages_total
  - Innodb_buffer_pool_pages_data
  - Innodb_buffer_pool_pages_free
  - Innodb_buffer_pool_pages_dirty

#### MySQL innodb Checkpoint Age

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| -------------- | ------------------------------------------------------- | - | - | -------------------------------------- |
| Uncheckpointed | custom.mysql.innodb_checkpoint_age.uncheckpointed_bytes |   |   | Size not written by checkpoint (bytes) |

- Calculated from the result of `SHOW /*!50000 ENGINE*/ INNODB STATUS` using `log_bytes_written - last_checkpoint`.

#### MySQL innodb Current Lock Waits (secs)

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ---------------- | ------------------------------------------------------------ | - | - | ----------------------------------------------- |
| Innodb Lock Wait | custom.mysql.innodb_current_lock_waits.innodb_lock_wait_secs |   |   | Time waited for lock to be authorized (seconds) |

- From `TRANSACTIONS` of `SHOW /*!50000 ENGINE*/ INNODB STATUS`, the wait time before a lock is allowed is used as a metric.

#### MySQL innodb I/O

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ----------- | ---------------------------------- | - | - | ----------------------------------- |
| Log Writes  | custom.mysql.innodb_io.log_writes  | ✓ |   | Number of log write I/Os performed  |
| File Reads  | custom.mysql.innodb_io.file_reads  | ✓ |   | Total number of data reads          |
| File Writes | custom.mysql.innodb_io.file_writes | ✓ |   | Total number of data writes         |
| File fsyncs | custom.mysql.innodb_io.file_fsyncs | ✓ |   | Number of times fsync() is executed |

- `log_writes` is the value of the variable from `LOG` in `SHOW /*!50000 ENGINE*/ INNODB STATUS` as a metric.
- Others take the values of the following variables as metric from the results of `SHOW /*!50002 GLOBAL */ STATUS`.
  - Innodb_data_reads
  - Innodb_data_writes
  - Innodb_data_fsyncs

#### MySQL innodb I/O Pending

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ----------------------- | -------------------------------------------------------- | - | - | ------------------------------------------------------------- |
| Normal AIO Reads        | custom.mysql.innodb_io_pending.pending_normal_aio_reads  |   |   | Number of asynchronous I/O waits on normal reads              |
| Normal AIO Writes       | custom.mysql.innodb_io_pending.pending_normal_aio_writes |   |   | Number of asynchronous I/O waits for normal writes            |
| InnoDB Buffer AIO Reads | custom.mysql.innodb_io_pending.pending_ibuf_aio_reads    |   |   | Number of read waits due to asynchronous I/O in INSERT buffer |
| AIO Log IOs             | custom.mysql.innodb_io_pending.pending_aio_log_ios       |   |   | Number of log I/O waits in INSERT buffer                      |
| AIO Sync IOs            | custom.mysql.innodb_io_pending.pending_aio_sync_ios      |   |   | Number of synchronous I/O waits in INSERT buffer              |
| Log Flushes (fsync)     | custom.mysql.innodb_io_pending.pending_log_flushes       |   |   | Number of logs waiting to be flushed                          |
| Buffer Pool Flushes     | custom.mysql.innodb_io_pending.pending_buf_pool_flushes  |   |   | Number of buffer pools waiting to be flushed                  |
| Log Writes              | custom.mysql.innodb_io_pending.pending_log_writes        |   |   | Number of logs waiting to be written                          |
| Checkpoint Writes       | custom.mysql.innodb_io_pending.pending_chkp_writes       |   |   | Number of checkpoints waiting to be written                   |
| Log Flushes (log)       | custom.mysql.innodb_io_pending.log_pending_log_flushes   |   |   | Number of logs waiting to be written                          |

- The items related to `FILE I/O` and `LOG` from the results of `SHOW /*!50000 ENGINE*/ INNODB STATUS` are used as metrics.
- The metric to get depends on the version of MySQL.
  - `pending_log_write` is not output in MySQL 5.7 or later.
  - `pending_chkp_writes` is not output in MySQL 8 or later.
  - `log_pending_log_flushes` is output in MySQL 5.7 and later, but less than MySQL8.

#### MySQL innodb Insert Buffer

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ------- | ---------------------------------------------- | - | - | ----------------------------------- |
| Inserts | custom.mysql.innodb_insert_buffer.ibuf_inserts | ✓ |   | Number of buffered write operations |
| Merges  | custom.mysql.innodb_insert_buffer.ibuf_merges  | ✓ |   | Number of merge requests            |
| Merged  | custom.mysql.innodb_insert_buffer.ibuf_merged  | ✓ |   | Number of times merged              |

- The items related to `INSERT BUFFER AND ADAPTIVE HASH INDEX` from the results of `SHOW /*!50000 ENGINE*/ INNODB STATUS` are used as metrics.

#### MySQL innodb Insert Buffer Usage (Cells)

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ---------- | ------------------------------------------------------- | - | - | -------------------------------- |
| Cell Count | custom.mysql.innodb_insert_buffer_usage.ibuf_cell_count |   |   | Number of cells in INSERT buffer |
| Used       | custom.mysql.innodb_insert_buffer_usage.ibuf_used_cells |   | ✓ | Number of cells in use           |
| Free       | custom.mysql.innodb_insert_buffer_usage.ibuf_free_cells |   | ✓ | Number of free cells             |

- The items related to `INSERT BUFFER AND ADAPTIVE HASH INDEX` from the results of `SHOW /*!50000 ENGINE*/ INNODB STATUS` are used as metrics.

#### MySQL innodb Lock Structures

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ---------- | ------------------------------------------------------- | - | - | --------------------------------------------- |
| Structures | custom.mysql.innodb_lock_structures.innodb_lock_structs |   |   | Number of lock structs waiting to be released |

- The following items from `SHOW /*!50000 ENGINE*/ INNODB STATUS` regarding lock release are used as a metric.
  - `TRANSACTIONS`
  - `LATEST DETECTED DEADLOCK`
  - `LATEST FOREIGN KEY ERROR`

#### MySQL innodb Log

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ---------------- | ---------------------------------------------- | - | - | ------------------------------ |
| Written          | custom.mysql.innodb_log.log_bytes_written      | ✓ |   | Size of writes to log          |
| Flushed          | custom.mysql.innodb_log.log_bytes_flushed      | ✓ |   | Size written back from the log |
| Unflushed        | custom.mysql.innodb_log.unflushed_log          |   |   | Size not written out of log    |
| Buffer Size      | custom.mysql.innodb_log.innodb_log_buffer_size |   | ✓ | log buffer size                |

- The `innodb_log_buffer_size` is the value of the variable from the result of `SHOW VARIABLES` as a metric.
- Other items are taken as metrics from `LOG` in `SHOW /*!50000 ENGINE*/ INNODB STATUS`, such as log writes.
  - `unflushed_log` is calculated by `log_bytes_written - log_bytes_flushed`.

#### MySQL innodb Memory Allocation

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ------------------------- | ----------------------------------------------------------- | - | - | --------------------------------- |
| Additional Pool Allocated | custom.mysql.innodb_memory_allocation.additional_pool_alloc |   |   | Additional pool memory allocation |
| Total Memory Allocated    | custom.mysql.innodb_memory_allocation.total_mem_alloc       |   |   | Total memory allocated            |

- The items from `SHOW /*!50000 ENGINE*/ INNODB STATUS` `BUFFER POOL AND MEMORY` are used as metrics for the amount of memory allocated.

#### MySQL innodb Semaphores

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ----------- | ------------------------------------------ | - | - | -------------------------------------------------------------------------------------------------------------- |
| Spin Waits  | custom.mysql.innodb_semaphores.spin_waits  | ✓ |   | Number of exclusive lock requests                                                                              |
| Spin Rounds | custom.mysql.innodb_semaphores.spin_rounds | ✓ |   | Number of spin loops without obtaining an exclusive lock                                                       |
| OS Waits    | custom.mysql.innodb_semaphores.os_waits    | ✓ |   | Number of times that the lock is not acquired even after the specified number of spin loops and a wait is made |

- The items related to semaphore spins and waits from `SEMAPHORES` in `SHOW /*!50000 ENGINE*/ INNODB STATUS` are used as a metric.

#### MySQL innodb Tables In Use

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ------------- | ------------------------------------------------------ | - | - | ----------------------------------------------------- |
| Locked Tables | custom.mysql.innodb_tables_in_use.innodb_locked_tables |   |   | Total number of tables locked by running transactions |
| Table in Use  | custom.mysql.innodb_tables_in_use.innodb_tables_in_use |   |   | Total number of tables used by running transactions   |

- The entry for running transactions from `SHOW /*!50000 ENGINE*/ INNODB STATUS` `TRANSACTIONS` is used as the metric.

#### MySQL innodb Transactions Active/Locked

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ---------------- | ------------------------------------------------------------------- | - | - | ----------------------------------------------- |
| Current        | custom.mysql.innodb_transactions_active_locked.current_transactions |   |   | Number of running transactions                  |
| Active         | custom.mysql.innodb_transactions_active_locked.active_transactions  |   |   | Number of active transactions running           |
| Locked         | custom.mysql.innodb_transactions_active_locked.locked_transactions  |   |   | Number of transactions that are LOCK WAIT       |
| Read Views     | custom.mysql.innodb_transactions_active_locked.read_views           |   |   | Number of transactions that open Read View |

- The items from `TRANSACTIONS` and `ROW OPERATIONS` of `SHOW /*!50000 ENGINE*/ INNODB STATUS` about running transactions are used as metrics.

#### MySQL innodb Transactions

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| --------------------- | ---------------------------------------------------- | - | - | ---------------------- |
| History List        | custom.mysql.innodb_transactions.history_list        |   |   | Number of UNDO pages   |
| InnoDB Transactions | custom.mysql.innodb_transactions.innodb_transactions | ✓ |   | Number of Transactions |

- The items from `TRANSACTIONS` of `SHOW /*!50000 ENGINE*/ INNODB STATUS`  about running transactions are used as the metric.

<h3 id="extended-metrics">Extended Metrics</h3>

Extended metrics can be enabled with the `--enable_extended=true` option.

#### MySQL query Cache

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ----------------------- | ------------------------------------------------ | - | - | -------------------------------------------------------------------- |
| Qcache Queries In Cache | custom.mysql.query_cache.Qcache_queries_in_cache |   |   | Number of queries registered in the query cache                      |
| Qcache Hits             | custom.mysql.query_cache.Qcache_hits             | ✓ |   | Number of query cache hits                                           |
| Qcache Inserts          | custom.mysql.query_cache.Qcache_inserts          | ✓ |   | Number of queries added to the query cache                           |
| Qcache Not Cached       | custom.mysql.query_cache.Qcache_not_cached       | ✓ |   | Number of non-cached queries                                         |
| Qcache Lowmem Prunes    | custom.mysql.query_cache.Qcache_lowmem_prunes    | ✓ |   | Number of queries deleted from the query cache due to lack of memory |

- The value of each variable is taken as a metric from the result of `SHOW /*!50002 GLOBAL */ STATUS`.
- Not supported in MySQL 8.0.3 or later.

#### MySQL query Cache Memory

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ------------------- | --------------------------------------------------- | - | - | ------------------------------------------------- |
| Query Cache Size    | custom.mysql.query_cache_memory.query_cache_size    |   |   | Amount of memory allocated to cache query results |
| Qcache Free Memory  | custom.mysql.query_cache_memory.Qcache_free_memory  |   |   | Amount of free memory for query cache             |
| Qcache Total Blocks | custom.mysql.query_cache_memory.Qcache_total_blocks |   |   | Total number of blocks in the query cache         |
| Qcache Free Blocks  | custom.mysql.query_cache_memory.Qcache_free_blocks  |   |   | Number of free memory blocks in the query cache   |

- The `query_cache_size` takes the value from the `SHOW GLOBAL VARIABLES` result as a metric.
- The others are metric values for each variable from the `SHOW /*!50002 GLOBAL */ STATUS` results.
- Not supported in MySQL 8.0.3 and later.

#### MySQL temporary Objects

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ----------------------- | ------------------------------------------------------ | - | - | ------------------------------------------------------------------------------ |
| Created Tmp Tables      | custom.mysql.temporary_objects.Created_tmp_tables      | ✓ |   | Number of internal temporary tables created during statement execution         |
| Created Tmp Disk Tables | custom.mysql.temporary_objects.Created_tmp_disk_tables | ✓ |   | Number of internal temporary tables on disk created during statement execution |
| Created Tmp Files       | custom.mysql.temporary_objects.Created_tmp_files       | ✓ |   | Number of temporary files created by mysqld                                    |

- The value of each variable is taken as a metric from the result of `SHOW /*!50002 GLOBAL */ STATUS`.

#### MySQL files and Tables

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ------------- | ------------------------------------------- | - | - | -------------------------------------------------- |
| Table Cache   | custom.mysql.files_and_tables.table_cache   |   |   | Maximum number of table caches that can be held    |
| Open Tables   | custom.mysql.files_and_tables.Open_tables   |   |   | Number of open tables                              |
| Open Files    | custom.mysql.files_and_tables.Open_file     |   |   | Number of open files                               |
| Opened Tables | custom.mysql.files_and_tables.Opened_tables | ✓ |   | Total number of tables opened since server startup |

- The `table_cache` is the value of `table_open_cache` from the result of `SHOW VARIABLES` as a metric.
- The others are based on `SHOW /*!50002 GLOBAL */ STATUS` results with the value of each variable as the metric.

#### MySQL processlist

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| -------------------------- | --------------------------------------------------- | - | - | ------------------------------------------------------------------------------------------------------ |
| State Closing Tables       | custom.mysql.processlist.State_closing_tables       |   | ✓ | Number of tables being closed                                                                                |
| State Copying To Tmp Table | custom.mysql.processlist.State_copying_to_tmp_table |   | ✓ | Number of items being copied to temporary table                                                              |
| State End                  | custom.mysql.processlist.State_end                  |   | ✓ | Number of ALTER TABLE, CREATE VIEW, DELETE, INSERT, SELECT, and UPDATE statements before cleanup       |
| State Freeing Items        | custom.mysql.processlist.State_freeing_items        |   | ✓ | Number of cases being released                                                                         |
| State Init                 | custom.mysql.processlist.State_init                 |   | ✓ | Number of ALTER TABLE, DELETE, INSERT, SELECT, and UPDATE statements before initialization             |
| State Locked               | custom.mysql.processlist.State_locked               |   | ✓ | Number of locks being acquired                                                                         |
| State Login                | custom.mysql.processlist.State_login                |   | ✓ | Number of connections being authenticated                                                              |
| State Preparing            | custom.mysql.processlist.State_preparing            |   | ✓ | Number of queries in Prepare                                                                           |
| State Reading From Net     | custom.mysql.processlist.State_reading_from_net     |   | ✓ | Number of packets being read                                                                           |
| State Sending Data         | custom.mysql.processlist.State_sending_data         |   | ✓ | Number of SELECT results being sent to clients                                                         |
| State Sorting Result       | custom.mysql.processlist.State_sorting_result       |   | ✓ | Number of executions which sorting to non-temporary tables                                             |
| State Statistics           | custom.mysql.processlist.State_statistics           |   | ✓ | Number of queries being planned for execution                                                          |
| State Updating             | custom.mysql.processlist.State_updating             |   | ✓ | Number of rows being updated                                                                           |
| State Writing To Net       | custom.mysql.processlist.State_writing_to_net       |   | ✓ | Number of packets being written                                                                        |
| State None                 | custom.mysql.processlist.State_none                 |   | ✓ | Number of empty States                                                                                  |
| State Other                | custom.mysql.processlist.State_other                |   | ✓ | Number of unknown States                                                                               |

- The value of each variable is taken as a metric from the `SHOW PROCESSLIST` results.

#### MySQL sorts

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ----------------- | ------------------------------------ | - | - | ------------------------------------------------------------- |
| Sort Rows         | custom.mysql.sorts.Sort_rows         | ✓ |   | Number of rows sorted                                         |
| Sort Range        | custom.mysql.sorts.Sort_range        | ✓ |   | Number of sorts performed using the range                     |
| Sort Merge Passes | custom.mysql.sorts.Sort_merge_passes | ✓ |   | Number of merge passes that the sort algorithm had to perform |
| Sort Scan         | custom.mysql.sorts.Sort_scan         | ✓ |   | Number of sorts performed by scanning the table               |

- The value of each variable from the `SHOW /*!50002 GLOBAL */ STATUS` results as a metric.

#### MySQL handlers

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| --------------------- | ------------------------------------------- | - | - | --------------------------------------------------------------------------- |
| Handler Write         | custom.mysql.handlers.Handler_write         | ✓ | ✓ | Number of requests to insert rows into table                                |
| Handler Update        | custom.mysql.handlers.Handler_update        | ✓ | ✓ | Number of requests to update rows in a table                                |
| Handler Delete        | custom.mysql.handlers.Handler_delete        | ✓ | ✓ | Number of times a row is deleted from a table                               |
| Handler Read First    | custom.mysql.handlers.Handler_read_first    | ✓ | ✓ | Number of times the first entry in the index is read                        |
| Handler Read Key      | custom.mysql.handlers.Handler_read_key      | ✓ | ✓ | Number of requests to read rows based on key                                |
| Handler Read Last     | custom.mysql.handlers.Handler_read_last     | ✓ | ✓ | Number of requests to read the last key in the index (MySQL 5.6.1 or later) |
| Handler Read Next     | custom.mysql.handlers.Handler_read_next     | ✓ | ✓ | Number of requests to read the next row in key order                        |
| Handler Read Prev     | custom.mysql.handlers.Handler_read_prev     | ✓ | ✓ | number of requests for reading the previous row in key                      |
| Handler Read Rnd      | custom.mysql.handlers.Handler_read_rnd      | ✓ | ✓ | Number of row read requests based on fixed position                         |
| Handler Read Rnd Next | custom.mysql.handlers.Handler_read_rnd_next | ✓ | ✓ | Number of read requests for the next line in the data file                  |

- The value of each variable is taken as a metric from the result of `SHOW /*!50002 GLOBAL */ STATUS`.

#### MySQL transaction handler

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ----------------- | -------------------------------------------------- | - | - | --------------------------------------------------------------- |
| Handler Commit    | custom.mysql.transaction_handler.Handler_commit    | ✓ |   | Number of internal COMMIT statements                            |
| Handler Rollback  | custom.mysql.transaction_handler.Handler_rollback  | ✓ |   | Number of storage engine requests to perform rollback operation |
| Handler Savepoint | custom.mysql.transaction_handler.Handler_savepoint | ✓ |   | Number of requests to the storage engine to place the savepoint |

- The value of each variable is taken as a metric from the result of `SHOW /*!50002 GLOBAL */ STATUS`.

#### MySQL MyISAM Indexes

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ------------------ | ---------------------------------------------- | - | - | ------------------------------------------------------------------------- |
| Key Read Requests  | custom.mysql.myisam_indexes.Key_read_requests  | ✓ |   | Number of requests to read key blocks from the MyISAM key cache           |
| Key Reads          | custom.mysql.myisam_indexes.Key_reads          | ✓ |   | Number of physical reads of key blocks from disk to MyISAM key cache      |
| Key Write Requests | custom.mysql.myisam_indexes.Key_write_requests | ✓ |   | Number of requests to write key blocks to the MyISAM key cache            |
| Key Writes         | custom.mysql.myisam_indexes.Key_writes         | ✓ |   | Number of physical writes of key blocks from the MyISAM key cache to disk |

- The value of each variable is taken as a metric from the results of `SHOW /*!50002 GLOBAL */ STATUS`.

#### MySQL MyISAM Key Cache

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ----------------------- | ----------------------------------------------------- | - | - | -------------------------------------- |
| Key Buffer Size         | custom.mysql.myisam_key_cache.key_buffer_size         |   |   | Size of buffer used for index block    |
| Key Buf Bytes Used      | custom.mysql.myisam_key_cache.key_buf_bytes_used      |   |   | Size of key buffer in use              |
| Key Buf Bytes Unflushed | custom.mysql.myisam_key_cache.key_buf_bytes_unflushed |   |   | Size of key buffer not flushed to disk |

- The value of each variable is taken as a metric from the results of `SHOW VARIABLES`, `SHOW /*!50002 GLOBAL */ STATUS`.
- `key_buf_bytes_used` is calculated by `key_buffer_size - Key_blocks_unused * key_cache_block_size`.
  - Only if the `key_cache_block_size` variable is available.
- `key_buf_bytes_unflushed` is calculated by `Key_blocks_not_flushed * key_cache_block_size`.
  - Only if the `Key_blocks_not_flushed` variable is available.

<h2 id="options">Options that can be specified</h2>.

The options that can be specified for the plugin are as follows

| Option | Environment Variables | Description | Default |
| ------------------- | -------------- | ------------------------------------------------------------ | --------- |
| --host              |                | destination hostname                                         | localhost |
| --port              |                | destination port number                                      | 3306      |
| --socket            |                | path of UNIX Socket                                          |           |
| --username          |                | connection username                                          | root      |
| --password          | MYSQL_PASSWORD | connection password                                          |           |
| --tempfile          |                | path to save temporary files                                 |           |
| --disable_innodb    |                | disable getting metric key for InnoDB if `true` is specified | false     |
| --metric-key-prefix |                | prefix given to metric                                       | mysql     |
| --enable_extended   |                | enable getting extended metric with `true`                   | false     |
| --debug             |                | enable debug mode with `true`                                | false     |

<h2 id="config">Example configuration for an agent</h2>

To monitor MySQL running on port 3306 on the localhost, configure as follows.

```toml
[plugin.metrics.mysql]
command = ["mackerel-plugin-mysql", "--host", "localhost", "--port", "3306"]
```

With the above configuration, you can verify the operation by running the following.

```
$ mackerel-plugin-mysql --mysql localhost --port 3306
```

<h2 id="config">User privileges required to run the plugin</h2>

The following permissions are required for users connecting to the database.

- `PROCESS`
- `SUPER`
- `REPLICATION CLIENT`

<h2 id="repository">Repository</h2>

https://github.com/mackerelio/mackerel-plugin-mysql

<h2 id="reference">Reference</h2>

For more information on each variable referenced by the plugin, please refer to the following reference manuals.

- [MySQL :: MySQL 5.7 Reference Manual :: 5.1.7 Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html )
- [MySQL :: MySQL 5.7 Reference Manual :: 5.1.9 Server Status Variables](https://dev.mysql.com/doc/refman/5.7/en/server-status-variables.html )
- [MySQL ::: MySQL 5.7 Reference Manual :: 8.14.3 General Thread States](https://dev.mysql.com/doc/refman/5.7/en/general-thread-states.html)
- [MySQL ::: MySQL 5.7 Reference Manual :: 13.7.5.29 SHOW PROCESSLIST Statement](https://dev.mysql.com/doc/refman/5.7/en/show-processlist.html )
- [MySQL ::: MySQL 5.7 Reference Manual :: 13.7.5.35 SHOW STATUS Statement](https://dev.mysql.com/doc/refman/5.7/en/show-status.html)
- [MySQL ::: MySQL 5.7 Reference Manual :: 14.5.3 Adaptive Hash Index](https://dev.mysql.com/doc/refman/5.7/en/innodb-adaptive-hash.html)
- [MySQL ::: MySQL 5.7 Reference Manual :: 14.18.3 InnoDB Standard Monitor and Lock Monitor Output](https://dev.mysql.com/doc/refman/5.7/en/innodb-standard-monitor.html)
