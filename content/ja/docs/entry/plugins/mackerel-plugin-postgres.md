---
Title: メトリックプラグイン - mackerel-plugin-postgres
Date: 2023-04-28T11:41:53+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/mackerel-plugin-postgres
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889985220876
CustomPath: plugins/mackerel-plugin-postgres
---

mackerel-plugin-postgres は PostgreSQL の統計状態をメトリックとして投稿するプラグインです。監視対象の PostgreSQL のバージョンによっては、実行するユーザに権限を与える必要があります。詳しくは [プラグインの実行に必要なユーザ権限](https://mackerel.io/ja/docs/entry/plugins/mackerel-plugin-postgres#role) を参照してください。

[:contents]

<h2 id="metrics">監視できるメトリック</h2>

PostgreSQL の統計情報をメトリックとして投稿します。統計情報については PostgreSQL のドキュメントを参照してください。

[https://www.postgresql.jp/document/:embed:cite]


### Postgres Connections

[pg_stat_activity](https://www.postgresql.jp/document/14/html/monitoring-stats.html#MONITORING-PG-STAT-ACTIVITY-VIEW) から取得できる情報を投稿します。

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Active | postgres.connections.active | - | ○ | active 状態のコネクション数 |
| Active waiting | postgres.connections.active_waiting | - | ○ | active かつ waiting イベントが存在する状態のコネクション数 |
| Idle | postgres.connections.idle | - | ○ | idle 状態のコネクション数 |
| Idle in transaction | postgres.connections.idle_in_transaction | - | ○ | idle in transaction（トランザクション中で idle）状態のコネクション数  |
| Idle in transaction (aborted) | postgres.connections.idle_in_transaction_aborted_ | - | ○ | idle in transaction 状態でトランザクション中にエラーが発生したコネクション数 |
| fast-path function call | postgres.connections.fastpath_function_call | - | ○ | 近道関数を実行中のコネクション数 |
| Disabled | postgres.connections.disabled | - | ○ | Disabled 状態のコネクション数（track_activities が無効になっている場合） |


### Postgres Commits

[pg_stat_database](https://www.postgresql.jp/document/14/html/monitoring-stats.html#MONITORING-PG-STAT-DATABASE-VIEW) から取得できる情報を投稿します。

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Xact Commit | postgres.commits.xact_commit | ○ | - | 1分あたりのデータベースでコミットされたトランザクション数 |
| Xact Rollback | postgres.commits.xact_rollback | ○ | - | 1分あたりのデータベースでロールバックされたトランザクション数 |


### Postgres Blocks

[pg_stat_database](https://www.postgresql.jp/document/14/html/monitoring-stats.html#MONITORING-PG-STAT-DATABASE-VIEW) から取得できる情報を投稿します。

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Blocks Read | postgres.blocks.blks_read | ○ | - | 1分あたりのディスクから読み込まれたブロック数 |
| Blocks Hit | postgres.blocks.blks_hit | ○ | - | 1分あたりのキャッシュから読み込まれたブロック数 |


### Postgres Rows

[pg_stat_database](https://www.postgresql.jp/document/14/html/monitoring-stats.html#MONITORING-PG-STAT-DATABASE-VIEW) から取得できる情報を投稿します。

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Returned Rows | postgres.rows.tup_returned | ○ | - | 1分あたりのデータベース内の問い合わせで返された行数 |
| Fetched Rows | postgres.rows.tup_fetched | ○ | ○ | 1分あたりのデータベース内の問い合わせで取り出された行数 |
| Inserted Rows | postgres.rows.tup_inserted | ○ | ○ | 1分あたりのデータベース内の問い合わせで挿入された行数 |
| Updated Rows | postgres.rows.tup_updated | ○ | ○ | 1分あたりのデータベース内の問い合わせで更新された行数 |
| Deleted Rows | postgres.rows.tup_deleted | ○ | ○ | 1分あたりのデータベース内の問い合わせで削除された行数 |


### Postgres Data Size

`SELECT sum(pg_database_size(datname)) from pg_database` を実行した結果を投稿します。

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Total Size | postgres.size.total_size | - | - | 全データベースの合計サイズ（バイト） |


### Postgres Dead Locks

[pg_stat_database](https://www.postgresql.jp/document/14/html/monitoring-stats.html#MONITORING-PG-STAT-DATABASE-VIEW) から取得できる情報を投稿します。

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Deadlocks | postgres.deadlocks.deadlocks | ○ | - | 1分あたりのデータベース内で検知されたデッドロック数 |


### Postgres Block I/O time

[pg_stat_database](https://www.postgresql.jp/document/14/html/monitoring-stats.html#MONITORING-PG-STAT-DATABASE-VIEW) から取得できる情報を投稿します。このメトリックを投稿するためには track_io_timing を有効にする必要があります。設定が無効な場合は 0 を投稿します。

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Block Read Time (ms) | postgres.iotime.blk_read_time | ○ | - | 1分間あたりのデータファイルブロックの読み取り時間（ミリ秒） |
| Block Write Time (ms) | postgres.iotime.blk_write_time | ○ | - | 1分間あたりのデータファイルブロックの書き出し時間（ミリ秒） |


### Postgres Temporary file

[pg_stat_database](https://www.postgresql.jp/document/14/html/monitoring-stats.html#MONITORING-PG-STAT-DATABASE-VIEW) から取得できる情報を投稿します。

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Temporary file size (byte) | postgres.tempfile.temp_bytes | ○ | - | 1分あたりの一時ファイルに書き出されたファイルサイズ（バイト） |


### Postgres Amount of Transaction location change

WAL に関する情報を投稿します。このメトリックを投稿するためには pg_settings の wal_level を logical に設定する必要があります。logical 以外が設定されている場合は 0 を投稿します。

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Amount of Transaction location change (byte) | postgres.xlog_location.xlog_location_bytes | ○ | - | 1分あたりのログ先行書き込みのサイズ（バイト） |

- PostgreSQL バージョン 10 以上
  - `SELECT pg_wal_lsn_diff(pg_current_wal_lsn(), '0/0')` の結果
- PostgreSQL バージョン 9.2 以上
  - `SELECT pg_xlog_location_diff(pg_current_xlog_location(), '0/0')` の結果


<h2 id="options">指定可能なオプション</h2>

| オプション             | 必須  | 説明                                                                                                  | デフォルト値       |
| ----------------- | --- | --------------------------------------------------------------------------------------------------- | --------- |
| -hostname          |     | 監視対象のホスト                                                                                            | localhost |
| -port              |     | 接続先ポート                                                                                              | 5432      |
| -user              | ◯   | ユーザ名                                                                                                |           |
| -password          |     | パスワード                                                                                               |           |
| -database          |     | 接続先データベース                                                                                           | postgres  |
| -metric-key-prefix |     | カスタムメトリック名の接頭辞                                                                                   | postgres  |
| -sslmode           |     | SSL 接続を使用する                                                                                         | disable   |
| -connect_timeout   |     | タイムアウトまでの時間                                                                                   | 5（秒）      |
| -tempfile          |     | tempfile の保存先ファイルパスの指定 |           |
| -h, -help          |     | ヘルプを表示                                                                                              |           |

- -user に postgres 以外を指定する場合、-database オプションで接続先を指定する必要があります。なお、指定したデータベースの情報のみを取得するオプションではありません。
- tempfile には前回の結果が保存されます。デフォルトでは `/var/tmp/mackerel-agen` 配下に `mackerel-plugin-postgres-<ハッシュ文字列>` の形式で作成されます。


<h2 id="config">エージェントへの設定例</h2>

```
[plugin.metrics.postgres]
command = ["mackerel-plugin-postgres", "-user", "username", "-password", "password"]
```


<h2 id="role">プラグインの実行に必要なユーザ権限</h2>

データベースへ接続するユーザに下記の権限が必要です。

- PostgreSQL バージョン 10 以上
  - pg_monitor ロールが必要
- PostgreSQL バージョン 9.6 以下
  - 追加のロールは不要


<h2 id="tips">Tips</h2>

### PostgreSQL の接続情報の記述方法

mackerel-plugin-postgres の実行に必要な接続情報を -password オプションに直接記述することを避けたい場合は、下記のような方法があります。下記の方法を利用する場合は、command の内容を配列ではなく、サンプルのように文字列で記述する必要があります。

**コマンドを利用する**

文字列で記述した場合 command の内容はシェル経由の実行となるので、引数にパスワードを出力するコマンドが指定できます。

mackerel-agent.conf の内容
```
[plugin.metrics.postgres]
command = "mackerel-plugin-postgres -user username -password パスワードを出力するコマンド"
```

**チェック監視の環境変数を利用する**

チェック監視の env を利用すれば環境変数として記述できます。env についてはチェック監視の [設定項目](https://mackerel.io/ja/docs/entry/custom-checks#items) を参照してください。

mackerel-agent.conf の内容
```
[plugin.metrics.postgres]
command = "mackerel-plugin-postgres -user username -password $PG_PASSWORD"
env = { "PG_PASSWORD" = "password" }
```

**エージェントの環境変数を利用する**

mackerel-agent に適用した環境変数も利用できます。詳しくは [mackerel-agent仕様](https://mackerel.io/ja/docs/entry/spec/agent#environment-variables) の [環境変数を適用する](https://mackerel.io/ja/docs/entry/spec/agent#environment-variables) を参照してください。

下記は Linux 環境で設定する場合の例です。

/etc/sysconfig/mackerel-agent の内容
```
PG_PASSWORD=password
```

mackerel-agent.conf の内容
```
[plugin.metrics.postgres]
command = "mackerel-plugin-postgres -user username -password $PG_PASSWORD"
```


<h2 id="repository">リポジトリ</h2>

[https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-postgres]
