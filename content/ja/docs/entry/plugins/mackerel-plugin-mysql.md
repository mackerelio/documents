---
Title: メトリックプラグイン - mackerel-plugin-mysql
Date: 2023-06-09T15:42:20+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/mackerel-plugin-mysql
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/820878482940092156
CustomPath: plugins/mackerel-plugin-mysql
---

mackerel-plugin-mysqlはクエリの実行回数など、MySQLに関する各種メトリックを監視できます。

[:contents]

<h2 id="metrics">監視できるメトリック</h2>

<h3 id="standard-metrics">標準メトリック</h3>

#### MySQL Command

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                         |
| ---------------- | ----------------------------------- | ---- | ------------ | -------------------------------------------- |
| Insert           | custom.mysql.cmd.Com_insert         | ◯    | ◯            | INSERTの実行回数                             |
| Insert Select    | custom.mysql.cmd.Com_insert_select  | ◯    | ◯            | INSERT ... SELECTの実行回数                  |
| Select           | custom.mysql.cmd.Com_select         | ◯    | ◯            | SELECTの実行回数 (クエリキャッシュを除く)    |
| Update           | custom.mysql.cmd.Com_update         | ◯    | ◯            | UPDATEの実行回数                             |
| Update Multi     | custom.mysql.cmd.Com_update_multi   | ◯    | ◯            | 複数テーブル構文のUPDATEの実行回数           |
| Delete           | custom.mysql.cmd.Com_delete         | ◯    | ◯            | DELETEの実行回数                             |
| Delete Multi     | custom.mysql.cmd.Com_delete_multi   | ◯    | ◯            | 複数テーブル構文のDELETEの実行回数           |
| Replace          | custom.mysql.cmd.Com_replace        | ◯    | ◯            | REPLACEの実行回数                            |
| Replace Select   | custom.mysql.cmd.Com_replace_select | ◯    | ◯            | REPLACE ... SELECTの実行回数                 |
| Load             | custom.mysql.cmd.Com_load           | ◯    | ◯            | LOADの実行回数                               |
| Set Option       | custom.mysql.cmd.Com_set_option     | ◯    | ◯            | SET OPTIONの実行回数                         |
| Query Cache Hits | custom.mysql.cmd.Qcache_hits        | ◯    | -            | クエリキャッシュのヒット数                   |
| Questions        | custom.mysql.cmd.Questions          | ◯    | -            | サーバーによって実行されたステートメントの数 |

- `SHOW /*!50002 GLOBAL */ STATUS`の結果から各変数の値をメトリックとしています。

#### MySQL Join/Scan

| メトリック表示名       | メトリック名                             | 差分 | 積み上げ表示 | 説明                                                   |
| ---------------------- | ---------------------------------------- | ---- | ------------ | ------------------------------------------------------ |
| Select Full JOIN       | custom.mysql.join.Select_full_join       | ◯    | -            | インデックスを使用せず、テーブルスキャンでJOINした回数 |
| Select Full Range JOIN | custom.mysql.join.Select_full_range_join | ◯    | -            | 参照テーブル上で範囲検索を使用したJOINの回数           |
| Select Range           | custom.mysql.join.Select_range           | ◯    | -            | 最初のテーブルの範囲が使用されたJOINの回数             |
| Select Range Check     | custom.mysql.join.Select_range_check     | ◯    | -            | インデックスキーなしのJOINの回数                       |
| Select SCAN            | custom.mysql.join.Select_scan            | ◯    | -            | 最初のテーブルでフルスキャンが実行されたJOINの回数     |

- `SHOW /*!50002 GLOBAL */ STATUS`の結果から各変数の値をメトリックとしています。

#### MySQL Threads

| メトリック表示名 | メトリック名                           | 差分 | 積み上げ表示 | 説明                             |
| ---------------- | -------------------------------------- | ---- | ------------ | -------------------------------- |
| Cache Size       | custom.mysql.threads.thread_cache_size | -    | -            | スレッドキャッシュサイズ         |
| Connected        | custom.mysql.threads.Threads_connected | -    | -            | 現在開いている接続数             |
| Running          | custom.mysql.threads.Threads_running   | -    | -            | 稼働中のスレッド数               |
| Created          | custom.mysql.threads.Threads_created   | ◯    | -            | 新規作成されたスレッド数         |
| Cached           | custom.mysql.threads.Threads_cached    | -    | -            | スレッドキャッシュ内のスレッド数 |

- `thread_cache_size`は`SHOW VARIABLES`の結果から変数の値をメトリックとしています。
- `SHOW /*!50002 GLOBAL */ STATUS`の結果から各変数の値をメトリックとしています。

#### MySQL Connections

| メトリック表示名     | メトリック名                                  | 差分 | 積み上げ表示 | 説明                                                                 |
| -------------------- | --------------------------------------------- | ---- | ------------ | -------------------------------------------------------------------- |
| Max Connections      | custom.mysql.connections.max_connections      | -    | -            | 最大同時接続数                                                       |
| Max Used Connections | custom.mysql.connections.Max_used_connections | -    | -            | 稼働中に同時接続された最大数                                         |
| Connections          | custom.mysql.connections.Connections          | ◯    | -            | 接続の試行数                                                         |
| Threads Connected    | custom.mysql.connections.Threads_connected    | -    | -            | 現在開いている接続数                                                 |
| Aborted Clients      | custom.mysql.connections.Aborted_clients      | ◯    | -            | クライアントが接続を適切に閉じることなく終了したため中止された接続数 |
| Aborted Connects     | custom.mysql.connections.Aborted_connects     | ◯    | -            | MySQL Server への接続に失敗した試行数                                |

- `max_connections`は`SHOW VARIABLES`の結果から変数の値をメトリックとしています。
- `SHOW /*!50002 GLOBAL */ STATUS`の結果から各変数の値をメトリックとしています。

#### MySQL Slave status

| メトリック表示名      | メトリック名                                             | 差分 | 積み上げ表示 | 説明                                                              |
| --------------------- | -------------------------------------------------------- | ---- | ------------ | ----------------------------------------------------------------- |
| Seconds Behind Master | custom.mysql.seconds_behind_master.Seconds_Behind_Master | -    | -            | スレーブ SQL スレッドとスレーブ I/O スレッドの間の時間差 (秒単位) |

- `SHOW SLAVE STATUS`の結果をメトリックとしています。

#### MySQL Table Locks/Slow Queries

| メトリック表示名      | メトリック名                                   | 差分 | 積み上げ表示 | 説明                                                   |
| --------------------- | ---------------------------------------------- | ---- | ------------ | ------------------------------------------------------ |
| Table Locks Immediate | custom.mysql.table_locks.Table_locks_immediate | ◯    | -            | テーブルロックを即座に取得した回数                     |
| Table Locks Waited    | custom.mysql.table_locks.Table_locks_waited    | ◯    | -            | テーブルロックがブロックされ取得に待機が必要だった回数 |
| Slow Queries          | custom.mysql.table_locks.Slow_queries          | ◯    | -            | long_query_time 秒よりも時間を要したクエリ数           |

- `SHOW /*!50002 GLOBAL */ STATUS`の結果から各変数の値をメトリックとしています。

#### MySQL Traffic

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                     |
| ---------------- | ----------------------------------- | ---- | ------------ | ---------------------------------------- |
| Sent Bytes       | custom.mysql.traffic.Bytes_sent     | ◯    | -            | すべてのクライアントに送信されたバイト数 |
| Received Bytes   | custom.mysql.traffic.Bytes_received | ◯    | -            | すべてのクライアントから受信したバイト数 |

- `SHOW /*!50002 GLOBAL */ STATUS`の結果から各変数の値をメトリックとしています。

#### MySQL Capacity

| メトリック表示名          | メトリック名                                  | 差分 | 積み上げ表示 | 説明                   |
| ------------------------- | --------------------------------------------- | ---- | ------------ | ---------------------- |
| Percentage Of Connections | custom.mysql.capacity.PercentageOfConnections | -    | -            | 接続数の割合           |
| Percentage Of Buffer Pool | custom.mysql.capacity.PercentageOfBufferPool  | -    | -            | バッファープールの割合 |

- `SHOW VARIABLES`, `SHOW /*!50002 GLOBAL */ STATUS`の結果から算出した値をメトリックとしています。
  - 接続数の割合は`Threads_connected / max_connections`で算出しています。
  - バッファープールの割合は`database_pages / Innodb_buffer_pool_pages_total`で算出しています。
- プラグインのオプションに`--disable_innodb=true`が指定されている場合、バッファープールの割合は出力されません。


<h3 id="innodb-metrics">InnoDBに関するメトリック</h3>

InnoDBに関するメトリックはデフォルトで有効ですが、`--disable_innodb=true`オプションを付与することで無効化できます。

#### MySQL innodb Rows

| メトリック表示名 | メトリック名                                  | 差分 | 積み上げ表示 | 説明                                |
| ---------------- | --------------------------------------------- | ---- | ------------ | ----------------------------------- |
| Read             | custom.mysql.innodb_rows.Innodb_rows_read     | ◯    | -            | InnoDB テーブルから読み取られた行数 |
| Inserted         | custom.mysql.innodb_rows.Innodb_rows_inserted | ◯    | -            | InnoDB テーブルに挿入された行数     |
| Updated          | custom.mysql.innodb_rows.Innodb_rows_updated  | ◯    | -            | InnoDB テーブル内で更新された行数   |
| Deleted          | custom.mysql.innodb_rows.Innodb_rows_deleted  | ◯    | -            | InnoDB テーブルから削除された行数   |

- `SHOW /*!50002 GLOBAL */ STATUS`の結果から各変数の値をメトリックとしています。

#### MySQL innodb Row Lock Time

| メトリック表示名 | メトリック名                                           | 差分 | 積み上げ表示 | 説明                                                         |
| ---------------- | ------------------------------------------------------ | ---- | ------------ | ------------------------------------------------------------ |
| Lock Time        | custom.mysql.innodb_row_lock_time.Innodb_row_lock_time | ◯    | -            | InnoDB テーブルの行ロックの取得に要した合計時間 (ミリ秒単位) |

- `SHOW /*!50002 GLOBAL */ STATUS`の結果から各変数の値をメトリックとしています。

#### MySQL innodb Row Lock Waits

| メトリック表示名 | メトリック名                                             | 差分 | 積み上げ表示 | 説明                                            |
| ---------------- | -------------------------------------------------------- | ---- | ------------ | ----------------------------------------------- |
| Lock Waits       | custom.mysql.innodb_row_lock_waits.Innodb_row_lock_waits | ◯    | -            | InnoDB テーブル上の操作が行ロックを待機した回数 |

- `SHOW /*!50002 GLOBAL */ STATUS`の結果から各変数の値をメトリックとしています。

#### MySQL innodb Adaptive Hash Index

| メトリック表示名       | メトリック名                                                   | 差分 | 積み上げ表示 | 説明                          |
| ---------------------- | -------------------------------------------------------------- | ---- | ------------ | ----------------------------- |
| Hash Index Cells Total | custom.mysql.innodb_adaptive_hash_index.hash_index_cells_total | -    | -            | Adaptive Hash Indexの総Cell量 |
| Hash Index Cells Used  | custom.mysql.innodb_adaptive_hash_index.hash_index_cells_used  | -    | -            | Adaptive Hash Indexの使用量   |

- `SHOW /*!50000 ENGINE*/ INNODB STATUS`の結果から、`INSERT BUFFER AND ADAPTIVE HASH INDEX`の項目をメトリックとしています。
- `hash_index_cells_used`はMySQL 5.1.28以降では使用できなくなりました。プラグインのバージョンによって次のように取り扱いが変わります。
  - mackerel-plugin-mysql v1.2.1以降はメトリックが出力されません。
  - mackerel-plugin-mysql v1.2.0以前では常に`0`となります。

#### MySQL innodb Buffer Pool Read (/sec)

| メトリック表示名       | メトリック名                                           | 差分 | 積み上げ表示 | 説明                                                                           |
| ---------------------- | ------------------------------------------------------ | ---- | ------------ | ------------------------------------------------------------------------------ |
| Pages Read Ahead       | custom.mysql.innodb_buffer_pool_read.read_ahead        | ◯    | -            | InnoDB バッファープールに読み取られた1秒あたりのページ数                       |
| Evicted Without Access | custom.mysql.innodb_buffer_pool_read.read_evicted      | ◯    | -            | 未使用により消去されたInnoDB バッファープールに読み取られた1秒あたりのページ数 |
| Random Read Ahead      | custom.mysql.innodb_buffer_pool_read.read_random_ahead | ◯    | -            | InnoDBによってランダムに読み取りされた1秒あたりの数（※MySQL5.7以降）           |

- `SHOW /*!50002 GLOBAL */ STATUS`の結果から以下の変数の値をメトリックとしています。
  - Innodb_buffer_pool_read_ahead
  - Innodb_buffer_pool_read_ahead_evicted
  - Innodb_buffer_pool_read_ahead_rnd

#### MySQL innodb Buffer Pool Activity (Pages)

| メトリック表示名 | メトリック名                                           | 差分 | 積み上げ表示 | 説明                                              |
| ---------------- | ------------------------------------------------------ | ---- | ------------ | ------------------------------------------------- |
| Created          | custom.mysql.innodb_buffer_pool_activity.pages_created | ◯    | -            | InnoDB テーブルの操作によって作成されるページ数   |
| Read             | custom.mysql.innodb_buffer_pool_activity.pages_read    | ◯    | -            | InnoDB テーブルの操作によって読み取られるページ数 |
| Written          | custom.mysql.innodb_buffer_pool_activity.pages_written | ◯    | -            | InnoDB テーブルの操作によって書き込まれるページ数 |

- `SHOW /*!50002 GLOBAL */ STATUS`の結果から以下の変数の値をメトリックとしています。
  - Innodb_pages_created
  - Innodb_pages_read
  - Innodb_pages_written

#### MySQL innodb Buffer Pool Efficiency

| メトリック表示名 | メトリック名                                                                | 差分 | 積み上げ表示 | 説明                                             |
| ---------------- | --------------------------------------------------------------------------- | ---- | ------------ | ------------------------------------------------ |
| Reads            | custom.mysql.innodb_buffer_pool_efficiency.Innodb_buffer_pool_reads         | ◯    | -            | バッファープールが利用せず直接読み取りを行った数 |
| Read Requests    | custom.mysql.innodb_buffer_pool_efficiency.Innodb_buffer_pool_read_requests | ◯    | -            | 論理読み取りリクエスト数                         |

- `SHOW /*!50002 GLOBAL */ STATUS`の結果から各変数の値をメトリックとしています。

#### MySQL innodb Buffer Pool (Pages)

| メトリック表示名 | メトリック名                                   | 差分 | 積み上げ表示 | 説明                                                  |
| ---------------- | ---------------------------------------------- | ---- | ------------ | ----------------------------------------------------- |
| Pool Size        | custom.mysql.innodb_buffer_pool.pool_size      | -    | -            | InnoDB バッファープールの合計サイズ (ページ単位)      |
| Used             | custom.mysql.innodb_buffer_pool.database_pages | -    | -            | データを含む InnoDB バッファープール内のページ数      |
| Free             | custom.mysql.innodb_buffer_pool.free_pages     | -    | -            | InnoDB バッファープール内の空きページの数             |
| Modified         | custom.mysql.innodb_buffer_pool.modified_pages | -    | -            | InnoDB バッファープール内のダーティーページの現在の数 |

- `SHOW /*!50002 GLOBAL */ STATUS`の結果から以下の変数の値をメトリックとしています。
  - Innodb_buffer_pool_pages_total
  - Innodb_buffer_pool_pages_data
  - Innodb_buffer_pool_pages_free
  - Innodb_buffer_pool_pages_dirty

#### MySQL innodb Checkpoint Age

| メトリック表示名 | メトリック名                                            | 差分 | 積み上げ表示 | 説明                                                 |
| ---------------- | ------------------------------------------------------- | ---- | ------------ | ---------------------------------------------------- |
| Uncheckpointed   | custom.mysql.innodb_checkpoint_age.uncheckpointed_bytes | -    | -            | チェックポイントで書き込みされていないサイズ（byte） |

- `SHOW /*!50000 ENGINE*/ INNODB STATUS`の結果から、`log_bytes_written - last_checkpoint`で算出しています。

#### MySQL innodb Current Lock Waits (secs)

| メトリック表示名 | メトリック名                                                 | 差分 | 積み上げ表示 | 説明                                     |
| ---------------- | ------------------------------------------------------------ | ---- | ------------ | ---------------------------------------- |
| Innodb Lock Wait | custom.mysql.innodb_current_lock_waits.innodb_lock_wait_secs | -    | -            | ロックが許可されるまで待機した時間（秒） |

- `SHOW /*!50000 ENGINE*/ INNODB STATUS`の`TRANSACTIONS`から、ロックが許可されるまで待機時間をメトリックとしています。

#### MySQL innodb I/O

| メトリック表示名 | メトリック名                       | 差分 | 積み上げ表示 | 説明                      |
| ---------------- | ---------------------------------- | ---- | ------------ | ------------------------- |
| Log Writes       | custom.mysql.innodb_io.log_writes  | ◯    | -            | ログ書き込みI/Oの実行回数 |
| File Reads       | custom.mysql.innodb_io.file_reads  | ◯    | -            | データ読み取りの合計数    |
| File Writes      | custom.mysql.innodb_io.file_writes | ◯    | -            | データ書き込みの合計数    |
| File fsyncs      | custom.mysql.innodb_io.file_fsyncs | ◯    | -            | fsync() の実行回数        |

- `log_writes`は`SHOW /*!50000 ENGINE*/ INNODB STATUS`の`LOG`から、変数の値をメトリックとしています。
- その他は`SHOW /*!50002 GLOBAL */ STATUS`の結果から以下の変数の値をメトリックとしています。
  - Innodb_data_reads
  - Innodb_data_writes
  - Innodb_data_fsyncs

#### MySQL innodb I/O Pending

| メトリック表示名        | メトリック名                                             | 差分 | 積み上げ表示 | 説明                                              |
| ----------------------- | -------------------------------------------------------- | ---- | ------------ | ------------------------------------------------- |
| Normal AIO Reads        | custom.mysql.innodb_io_pending.pending_normal_aio_reads  | -    | -            | 通常読み込みでの非同期I/O待ちの数                 |
| Normal AIO Writes       | custom.mysql.innodb_io_pending.pending_normal_aio_writes | -    | -            | 通常書き込みでの非同期I/O待ちの数                 |
| InnoDB Buffer AIO Reads | custom.mysql.innodb_io_pending.pending_ibuf_aio_reads    | -    | -            | INSERTバッファーの非同期I/Oによる読み込み待ちの数 |
| AIO Log IOs             | custom.mysql.innodb_io_pending.pending_aio_log_ios       | -    | -            | INSERTバッファーのログI/O待ちの数                 |
| AIO Sync IOs            | custom.mysql.innodb_io_pending.pending_aio_sync_ios      | -    | -            | INSERTバッファーの同期I/O待ちの数                 |
| Log Flushes (fsync)     | custom.mysql.innodb_io_pending.pending_log_flushes       | -    | -            | ログのフラッシュ待ちの数                          |
| Buffer Pool Flushes     | custom.mysql.innodb_io_pending.pending_buf_pool_flushes  | -    | -            | バッファープールのフラッシュ待ちの数              |
| Log Writes              | custom.mysql.innodb_io_pending.pending_log_writes        | -    | -            | ログ書き込み待ちの数                              |
| Checkpoint Writes       | custom.mysql.innodb_io_pending.pending_chkp_writes       | -    | -            | チェックポイントの書き込み待ちの数                |
| Log Flushes (log)       | custom.mysql.innodb_io_pending.log_pending_log_flushes   | -    | -            | ログ書き込み待ちの数                              |

- `SHOW /*!50000 ENGINE*/ INNODB STATUS`の結果から、`FILE I/O`と`LOG`に関する項目をメトリックとしています。
- MySQLのバージョンによって取得するメトリックが異なります。
  - `pending_log_write`はMySQL5.7以降では出力されません。
  - `pending_chkp_writes`はMySQL8以降では出力されません。
  - `log_pending_log_flushes`はMySQL5.7以降、MySQL8未満のバージョンで出力されます。

#### MySQL innodb Insert Buffer

| メトリック表示名 | メトリック名                                   | 差分 | 積み上げ表示 | 説明 |
| ---------------- | ---------------------------------------------- | ---- | ------------ | ---- |
| Inserts          | custom.mysql.innodb_insert_buffer.ibuf_inserts | ◯    | -            | バッファされた書き込み操作の回数     |
| Merges           | custom.mysql.innodb_insert_buffer.ibuf_merges  | ◯    | -            | マージ要求された回数     |
| Merged           | custom.mysql.innodb_insert_buffer.ibuf_merged  | ◯    | -            | マージされた回数     |

- `SHOW /*!50000 ENGINE*/ INNODB STATUS`の結果から、`INSERT BUFFER AND ADAPTIVE HASH INDEX`に関する項目をメトリックとしています。

#### MySQL innodb Insert Buffer Usage (Cells)

| メトリック表示名 | メトリック名                                            | 差分 | 積み上げ表示 | 説明                     |
| ---------------- | ------------------------------------------------------- | ---- | ------------ | ------------------------ |
| Cell Count       | custom.mysql.innodb_insert_buffer_usage.ibuf_cell_count | -    | -            | INSERTバッファーのセル数 |
| Used             | custom.mysql.innodb_insert_buffer_usage.ibuf_used_cells | -    | ◯            | 利用中のセル数           |
| Free             | custom.mysql.innodb_insert_buffer_usage.ibuf_free_cells | -    | ◯            | 空きセル数               |

- `SHOW /*!50000 ENGINE*/ INNODB STATUS`の結果から、`INSERT BUFFER AND ADAPTIVE HASH INDEX`に関する項目をメトリックとしています。

#### MySQL innodb Lock Structures

| メトリック表示名 | メトリック名                                            | 差分 | 積み上げ表示 | 説明                      |
| ---------------- | ------------------------------------------------------- | ---- | ------------ | ------------------------- |
| Structures       | custom.mysql.innodb_lock_structures.innodb_lock_structs | -    | -            | 解放待ちのlock structの数 |

- `SHOW /*!50000 ENGINE*/ INNODB STATUS`の以下からロック解放に関する項目をメトリックとしています。
  - `TRANSACTIONS`
  - `LATEST DETECTED DEADLOCK`
  - `LATEST FOREIGN KEY ERROR`

#### MySQL innodb Log

| メトリック表示名 | メトリック名                                   | 差分 | 積み上げ表示 | 説明                             |
| ---------------- | ---------------------------------------------- | ---- | ------------ | -------------------------------- |
| Written          | custom.mysql.innodb_log.log_bytes_written      | ◯    | -            | ログに書き込みをしたサイズ       |
| Flushed          | custom.mysql.innodb_log.log_bytes_flushed      | ◯    | -            | ログから書き戻しをしたサイズ     |
| Unflushed        | custom.mysql.innodb_log.unflushed_log          | -    | -            | ログから書き出されていないサイズ |
| Buffer Size      | custom.mysql.innodb_log.innodb_log_buffer_size | -    | ◯            | ログバッファーサイズ             |

- `innodb_log_buffer_size`は`SHOW VARIABLES`の結果から変数の値をメトリックとしています。
- その他は`SHOW /*!50000 ENGINE*/ INNODB STATUS`の`LOG`からログ書き込みなどに関する項目をメトリックとしています。
  - `unflushed_log`は`log_bytes_written - log_bytes_flushed`で算出しています。

#### MySQL innodb Memory Allocation

| メトリック表示名          | メトリック名                                                | 差分 | 積み上げ表示 | 説明                              |
| ------------------------- | ----------------------------------------------------------- | ---- | ------------ | --------------------------------- |
| Additional Pool Allocated | custom.mysql.innodb_memory_allocation.additional_pool_alloc | -    | -            | additional poolのメモリ割り当て量 |
| Total Memory Allocated    | custom.mysql.innodb_memory_allocation.total_mem_alloc       | -    | -            | メモリの総割り当て量              |

- `SHOW /*!50000 ENGINE*/ INNODB STATUS`の`BUFFER POOL AND MEMORY`からメモリ割り当て量に関する項目をメトリックとしています。

#### MySQL innodb Semaphores

| メトリック表示名 | メトリック名                               | 差分 | 積み上げ表示 | 説明                                                         |
| ---------------- | ------------------------------------------ | ---- | ------------ | ------------------------------------------------------------ |
| Spin Waits       | custom.mysql.innodb_semaphores.spin_waits  | ◯    | -            | 排他ロックを要求した回数                                     |
| Spin Rounds      | custom.mysql.innodb_semaphores.spin_rounds | ◯    | -            | 排他ロックのが獲得できずスピンループした回数                 |
| OS Waits         | custom.mysql.innodb_semaphores.os_waits    | ◯    | -            | 規定回数のスピンループでもロック獲得できずに待機となった回数 |

- `SHOW /*!50000 ENGINE*/ INNODB STATUS`の`SEMAPHORES`からセマフォのスピンや待機に関する項目をメトリックとしています。

#### MySQL innodb Tables In Use

| メトリック表示名 | メトリック名                                           | 差分 | 積み上げ表示 | 説明                                                         |
| ---------------- | ------------------------------------------------------ | ---- | ------------ | ------------------------------------------------------------ |
| Locked Tables    | custom.mysql.innodb_tables_in_use.innodb_locked_tables | -    | -            | 実行中のトランザクションによりロックされている延べテーブル数 |
| Table in Use     | custom.mysql.innodb_tables_in_use.innodb_tables_in_use | -    | -            | 実行中のトランザクションにより使用されている延べテーブル数   |

- `SHOW /*!50000 ENGINE*/ INNODB STATUS`の`TRANSACTIONS`から実行中のトランザクションに関する項目をメトリックとしています。

#### MySQL innodb Transactions Active/Locked

| メトリック表示名 | メトリック名                                                        | 差分 | 積み上げ表示 | 説明                                    |
| ---------------- | ------------------------------------------------------------------- | ---- | ------------ | --------------------------------------- |
| Current          | custom.mysql.innodb_transactions_active_locked.current_transactions | -    | -            | 実行中のトランザクション数              |
| Active           | custom.mysql.innodb_transactions_active_locked.active_transactions  | -    | -            | 実行中のアクティブなトランザクション数  |
| Locked           | custom.mysql.innodb_transactions_active_locked.locked_transactions  | -    | -            | LOCK WAITとなっているトランザクション数 |
| Read Views       | custom.mysql.innodb_transactions_active_locked.read_views           | -    | -            | Read Viewを開いているトランザクション数 |

- `SHOW /*!50000 ENGINE*/ INNODB STATUS`の`TRANSACTIONS`と`ROW OPERATIONS`から実行中のトランザクションに関する項目をメトリックとしています。

#### MySQL innodb Transactions

| メトリック表示名    | メトリック名                                         | 差分 | 積み上げ表示 | 説明               |
| ------------------- | ---------------------------------------------------- | ---- | ------------ | ------------------ |
| History List        | custom.mysql.innodb_transactions.history_list        | -    | -            | UNDOページ数       |
| InnoDB Transactions | custom.mysql.innodb_transactions.innodb_transactions | ◯    | -            | トランザクション数 |

- `SHOW /*!50000 ENGINE*/ INNODB STATUS`の`TRANSACTIONS`から実行中のトランザクションに関する項目をメトリックとしています。


<h3 id="extended-metrics">拡張メトリック</h3>

拡張メトリックは`--enable_extended=true`オプションで有効化できます。

#### MySQL query Cache

| メトリック表示名        | メトリック名                                     | 差分 | 積み上げ表示 | 説明                                                     |
| ----------------------- | ------------------------------------------------ | ---- | ------------ | -------------------------------------------------------- |
| Qcache Queries In Cache | custom.mysql.query_cache.Qcache_queries_in_cache | -    | -            | クエリキャッシュ内に登録されたクエリの数                 |
| Qcache Hits             | custom.mysql.query_cache.Qcache_hits             | ◯    | -            | クエリキャッシュヒットの数                               |
| Qcache Inserts          | custom.mysql.query_cache.Qcache_inserts          | ◯    | -            | クエリキャッシュに追加されるクエリの数                   |
| Qcache Not Cached       | custom.mysql.query_cache.Qcache_not_cached       | ◯    | -            | 非キャッシュクエリの数                                   |
| Qcache Lowmem Prunes    | custom.mysql.query_cache.Qcache_lowmem_prunes    | ◯    | -            | メモリ不足のためクエリキャッシュから削除されたクエリの数 |

- `SHOW /*!50002 GLOBAL */ STATUS`の結果から各変数の値をメトリックとしています。
- MySQL8.0.3以降では非対応となりました。

#### MySQL query Cache Memory

| メトリック表示名    | メトリック名                                        | 差分 | 積み上げ表示 | 説明                                                     |
| ------------------- | --------------------------------------------------- | ---- | ------------ | -------------------------------------------------------- |
| Query Cache Size    | custom.mysql.query_cache_memory.query_cache_size    | -    | -            | クエリ結果をキャッシュするために割り当てられたメモリの量 |
| Qcache Free Memory  | custom.mysql.query_cache_memory.Qcache_free_memory  | -    | -            | クエリキャッシュ用の空きメモリの量                       |
| Qcache Total Blocks | custom.mysql.query_cache_memory.Qcache_total_blocks | -    | -            | クエリキャッシュ内のブロックの合計数                     |
| Qcache Free Blocks  | custom.mysql.query_cache_memory.Qcache_free_blocks  | -    | -            | クエリキャッシュ内の空きメモリブロックの数               |

- `query_cache_size`は`SHOW GLOBAL VARIABLES`結果から値をメトリックとしています。
- その他は`SHOW /*!50002 GLOBAL */ STATUS`の結果から各変数の値をメトリックとしています。
- MySQL8.0.3以降では非対応となりました。

#### MySQL temporary Objects

| メトリック表示名        | メトリック名                                           | 差分 | 積み上げ表示 | 説明                                                               |
| ----------------------- | ------------------------------------------------------ | ---- | ------------ | ------------------------------------------------------------------ |
| Created Tmp Tables      | custom.mysql.temporary_objects.Created_tmp_tables      | ◯    | -            | ステートメントの実行中に作成された内部一時テーブルの数             |
| Created Tmp Disk Tables | custom.mysql.temporary_objects.Created_tmp_disk_tables | ◯    | -            | ステートメントの実行中に作成されたディスク上の内部一時テーブルの数 |
| Created Tmp Files       | custom.mysql.temporary_objects.Created_tmp_files       | ◯    | -            | mysqld が生成した一時ファイルの数                                  |

- `SHOW /*!50002 GLOBAL */ STATUS`の結果から各変数の値をメトリックとしています。

#### MySQL files and Tables

| メトリック表示名 | メトリック名                                | 差分 | 積み上げ表示 | 説明                                           |
| ---------------- | ------------------------------------------- | ---- | ------------ | ---------------------------------------- |
| Table Cache      | custom.mysql.files_and_tables.table_cache   | -    | -            | 保持できるテーブルキャッシュの最大数 |
| Open Tables      | custom.mysql.files_and_tables.Open_tables   | -    | -            | 開いているテーブルの数 |
| Open Files       | custom.mysql.files_and_tables.Open_files    | -    | -            | 開いているファイルの数 |
| Opened Tables    | custom.mysql.files_and_tables.Opened_tables | ◯    | -            | サーバー起動以降に開かれたテーブルの総数 |

- `table_cache`は`SHOW VARIABLES`の結果から`table_open_cache`の値をメトリックとしています。
- その他は`SHOW /*!50002 GLOBAL */ STATUS`の結果から各変数の値をメトリックとしています。

#### MySQL processlist

| メトリック表示名           | メトリック名                                        | 差分 | 積み上げ表示 | 説明                                                                                |
| -------------------------- | --------------------------------------------------- | ---- | ------------ | ----------------------------------------------------------------------------------- |
| State Closing Tables       | custom.mysql.processlist.State_closing_tables       | -    | ◯            | テーブルをクローズしている件数                                                      |
| State Copying To Tmp Table | custom.mysql.processlist.State_copying_to_tmp_table | -    | ◯            | 一時テーブルにコピーしている件数                                                    |
| State End                  | custom.mysql.processlist.State_end                  | -    | ◯            | ALTER TABLE、CREATE VIEW、DELETE、INSERT、SELECT、UPDATE ステートメントのクリーンアップ前の件数 |
| State Freeing Items        | custom.mysql.processlist.State_freeing_items        | -    | ◯            | 解放中の件数                                                                        |
| State Init                 | custom.mysql.processlist.State_init                 | -    | ◯            | ALTER TABLE、DELETE、INSERT、SELECT、UPDATEステートメントの初期化前の件数           |
| State Locked               | custom.mysql.processlist.State_locked               | -    | ◯            | ロック獲得中の件数                                                                  |
| State Login                | custom.mysql.processlist.State_login                | -    | ◯            | 接続認証中の件数                                                                    |
| State Preparing            | custom.mysql.processlist.State_preparing            | -    | ◯            | クエリのPrepare中の件数                                                             |
| State Reading From Net     | custom.mysql.processlist.State_reading_from_net     | -    | ◯            | パケット読み取り中の件数                                                            |
| State Sending Data         | custom.mysql.processlist.State_sending_data         | -    | ◯            | SELECT結果をクライアントに送信中の件数                                              |
| State Sorting Result       | custom.mysql.processlist.State_sorting_result       | -    | ◯            | 非一時テーブルにソートを実行している件数                                            |
| State Statistics           | custom.mysql.processlist.State_statistics           | -    | ◯            | クエリの実行計画中の件数                                                            |
| State Updating             | custom.mysql.processlist.State_updating             | -    | ◯            | 行を更新中の件数                                                                    |
| State Writing To Net       | custom.mysql.processlist.State_writing_to_net       | -    | ◯            | パケット書き込み中の件数                                                            |
| State None                 | custom.mysql.processlist.State_none                 | -    | ◯            | Stateが空の件数                                                                     |
| State Other                | custom.mysql.processlist.State_other                | -    | ◯            | 未知のStateの件数                                                                   |

- `SHOW PROCESSLIST`の結果から各変数の値をメトリックとしています。

#### MySQL sorts

| メトリック表示名  | メトリック名                         | 差分 | 積み上げ表示 | 説明                                                   |
| ----------------- | ------------------------------------ | ---- | ------------ | ------------------------------------------------------ |
| Sort Rows         | custom.mysql.sorts.Sort_rows         | ◯    | -            | ソートされた行の数                                     |
| Sort Range        | custom.mysql.sorts.Sort_range        | ◯    | -            | 範囲を使用して実行されたソートの数                     |
| Sort Merge Passes | custom.mysql.sorts.Sort_merge_passes | ◯    | -            | ソートアルゴリズムが実行する必要があったマージパスの数 |
| Sort Scan         | custom.mysql.sorts.Sort_scan         | ◯    | -            | テーブルをスキャンすることで実行されたソートの数       |

- `SHOW /*!50002 GLOBAL */ STATUS`の結果から各変数の値をメトリックとしています。

#### MySQL handlers

| メトリック表示名      | メトリック名                                | 差分 | 積み上げ表示 | 説明                                                              |
| --------------------- | ------------------------------------------- | ---- | ------------ | ----------------------------------------------------------------- |
| Handler Write         | custom.mysql.handlers.Handler_write         | ◯    | ◯            | テーブルへの行挿入リクエスト数                                    |
| Handler Update        | custom.mysql.handlers.Handler_update        | ◯    | ◯            | テーブルの行更新リクエスト数                                      |
| Handler Delete        | custom.mysql.handlers.Handler_delete        | ◯    | ◯            | テーブルから行が削除された回数                                    |
| Handler Read First    | custom.mysql.handlers.Handler_read_first    | ◯    | ◯            | インデックスの最初のエントリが読み取られた回数                    |
| Handler Read Key      | custom.mysql.handlers.Handler_read_key      | ◯    | ◯            | キーに基づいて行を読み取るリクエスト数                            |
| Handler Read Last     | custom.mysql.handlers.Handler_read_last     | ◯    | ◯            | インデックスの最後のキーを読み取るリクエスト数（MySQL 5.6.1以降） |
| Handler Read Next     | custom.mysql.handlers.Handler_read_next     | ◯    | ◯            | キー順で次の行の読み取りリクエスト数                              |
| Handler Read Prev     | custom.mysql.handlers.Handler_read_prev     | ◯    | ◯            | キー順で前の行の読み取りリクエスト数                              |
| Handler Read Rnd      | custom.mysql.handlers.Handler_read_rnd      | ◯    | ◯            | 固定された位置に基づいた行読み取りリクエスト数                    |
| Handler Read Rnd Next | custom.mysql.handlers.Handler_read_rnd_next | ◯    | ◯            | データファイル内で次の行の読み取りリクエスト数                    |

- `SHOW /*!50002 GLOBAL */ STATUS`の結果から各変数の値をメトリックとしています。

#### MySQL transaction handler

| メトリック表示名  | メトリック名                                       | 差分 | 積み上げ表示 | 説明                                                             |
| ----------------- | -------------------------------------------------- | ---- | ------------ | ---------------------------------------------------------------- |
| Handler Commit    | custom.mysql.transaction_handler.Handler_commit    | ◯    | -            | 内部 COMMIT ステートメントの数                                   |
| Handler Rollback  | custom.mysql.transaction_handler.Handler_rollback  | ◯    | -            | ロールバック操作を実行するためのストレージエンジンのリクエスト数 |
| Handler Savepoint | custom.mysql.transaction_handler.Handler_savepoint | ◯    | -            | セーブポイントを配置するためのストレージエンジンへのリクエスト数 |

- `SHOW /*!50002 GLOBAL */ STATUS`の結果から各変数の値をメトリックとしています。

#### MySQL MyISAM Indexes

| メトリック表示名   | メトリック名                                   | 差分 | 積み上げ表示 | 説明                                                                     |
| ------------------ | ---------------------------------------------- | ---- | ------------ | ------------------------------------------------------------------------ |
| Key Read Requests  | custom.mysql.myisam_indexes.Key_read_requests  | ◯    | -            | MyISAM キーキャッシュからキーブロックを読み取るリクエスト数              |
| Key Reads          | custom.mysql.myisam_indexes.Key_reads          | ◯    | -            | ディスクから MyISAM キーキャッシュへのキーブロックの物理的な読み取りの数 |
| Key Write Requests | custom.mysql.myisam_indexes.Key_write_requests | ◯    | -            | MyISAM キーキャッシュにキーブロックを書き込むリクエスト数                |
| Key Writes         | custom.mysql.myisam_indexes.Key_writes         | ◯    | -            | MyISAM キーキャッシュからディスクへのキーブロックの物理的な書き込みの数  |

- `SHOW /*!50002 GLOBAL */ STATUS`の結果から各変数の値をメトリックとしています。

#### MySQL MyISAM Key Cache

| メトリック表示名        | メトリック名                                          | 差分 | 積み上げ表示 | 説明                                                   |
| ----------------------- | ----------------------------------------------------- | ---- | ------------ | ------------------------------------------------------ |
| Key Buffer Size         | custom.mysql.myisam_key_cache.key_buffer_size         | -    | -            | インデックスブロックに使用されるバッファーのサイズ     |
| Key Buf Bytes Used      | custom.mysql.myisam_key_cache.key_buf_bytes_used      | -    | -            | 使用中のキーバッファーのサイズ                         |
| Key Buf Bytes Unflushed | custom.mysql.myisam_key_cache.key_buf_bytes_unflushed | -    | -            | ディスクにフラッシュされていないキーバッファーのサイズ |

- `SHOW VARIABLES`, `SHOW /*!50002 GLOBAL */ STATUS`の結果から各変数の値をメトリックとしています。
- `key_buf_bytes_used`は`key_buffer_size - Key_blocks_unused * key_cache_block_size`で算出しています。
  - `key_cache_block_size`の変数が利用可能な場合のみ
- `key_buf_bytes_unflushed`は`Key_blocks_not_flushed * key_cache_block_size`で算出しています。
  - `Key_blocks_not_flushed`の変数が利用可能な場合のみ

<h2 id="options">指定可能なオプション</h2>

プラグインに指定可能なオプションは以下の通りです。

| オプション          | 環境変数       | 説明                                                   | 初期値    |
| ------------------- | -------------- | ------------------------------------------------------ | --------- |
| --host              |                | 接続先ホスト名                                         | localhost |
| --port              |                | 接続先ポート番号                                       | 3306      |
| --socket            |                | UNIX Socketのパス                                      |           |
| --username          |                | 接続ユーザー名                                         | root      |
| --password          | MYSQL_PASSWORD | 接続パスワード                                         |           |
| --tempfile          |                | 一時ファイルの保存先パス                               |           |
| --disable_innodb    |                | InnoDBに関するメトリック取得を`true`を指定すると無効化 | false     |
| --metric-key-prefix |                | メトリックに付与するprefix                             | mysql     |
| --enable_extended   |                | 拡張メトリックの取得を`true`で有効化                     | false     |
| --debug             |                | デバッグモードを`true`で有効化                         | false     |

<h2 id="config">エージェントへの設定例</h2>

ローカルホスト上で3306番ポートで動作しているMySQLを監視するには次のように設定します。

```
[plugin.metrics.mysql]
command = ["mackerel-plugin-mysql", "--host", "localhost", "--port", "3306"]
```

上記設定の場合、次のように実行して動作確認できます。

```
$ mackerel-plugin-mysql --mysql localhost --port 3306
```

<h2 id="config">プラグインの実行に必要なユーザ権限</h2>

データベースへ接続するユーザに下記の権限が必要です。

- `PROCESS`
- `SUPER`
- `REPLICATION CLIENT`

<h2 id="repository">リポジトリ</h2>

https://github.com/mackerelio/mackerel-plugin-mysql

<h2 id="reference">参考</h2>

プラグインが参照する各変数の詳細については、以下のリファレンスマニュアルをご確認ください。

- [MySQL :: MySQL 5.7 Reference Manual :: 5.1.7 Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)
- [MySQL :: MySQL 5.7 Reference Manual :: 5.1.9 Server Status Variables](https://dev.mysql.com/doc/refman/5.7/en/server-status-variables.html)
- [MySQL :: MySQL 5.7 Reference Manual :: 8.14.3 General Thread States](https://dev.mysql.com/doc/refman/5.7/en/general-thread-states.html)
- [MySQL :: MySQL 5.7 Reference Manual :: 13.7.5.29 SHOW PROCESSLIST Statement](https://dev.mysql.com/doc/refman/5.7/en/show-processlist.html)
- [MySQL :: MySQL 5.7 Reference Manual :: 13.7.5.35 SHOW STATUS Statement](https://dev.mysql.com/doc/refman/5.7/en/show-status.html)
- [MySQL :: MySQL 5.7 Reference Manual :: 14.5.3 Adaptive Hash Index](https://dev.mysql.com/doc/refman/5.7/en/innodb-adaptive-hash.html)
- [MySQL :: MySQL 5.7 Reference Manual :: 14.18.3 InnoDB Standard Monitor and Lock Monitor Output](https://dev.mysql.com/doc/refman/5.7/en/innodb-standard-monitor.html)
