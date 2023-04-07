---
Title: メトリックプラグイン - mackerel-plugin-mssql
Date: 2023-04-07T11:05:31+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/mackerel-plugin-mssql
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889978758853
CustomPath: plugins/mackerel-plugin-mssql
---

mackerel-plugin-mssql は Microsoft SQL Server の情報をメトリックとして投稿するプラグインです。

[:contents]

<h2 id="support-version">プラグインがサポートする SQL Server のバージョン</h2>

- Microsoft SQL Server 2017 以降
- Microsoft SQL Express 2017 以降


<h2 id="metrics">監視できるメトリック</h2>

SQL Server オブジェクトから取得できるカウンターの値を投稿します。

[https://docs.microsoft.com/ja-jp/sql/relational-databases/performance-monitor/use-sql-server-objects?view=sql-server-ver16#SQLServerPOs:embed:cite]


### MSSQL Buffer

SQL Server が使用するメモリバッファに関する情報。[Buffer Manager オブジェクト](https://learn.microsoft.com/ja-jp/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object?view=sql-server-ver16) の情報を取得。

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Cache Hit Ratio | mssql.buffer_cache_hit_ratio | - | ○ | ディスクから読み出すことなくバッファーキャッシュで見つかったページの割合 |
| Page Life Expectancy | mssql.buffer_page_life_expectancy | ○ | ○ | ページが参照されないままバッファープールに留まった秒数 |
| Checkpoint Pages | mssql.buffer_checkpoint_pages | - | ○ | チェックポイントや、すべてのダーティページをフラッシュする必要がある他のオペレーションによって、1秒間にディスクにフラッシュされたページ数 |


### MSSQL Stats

SQL Server の利用状況に関する情報。[SQL Statistics オブジェクト](https://learn.microsoft.com/ja-jp/sql/relational-databases/performance-monitor/sql-server-sql-statistics-object?view=sql-server-ver16) および [General Statistics オブジェクト](https://learn.microsoft.com/ja-jp/sql/relational-databases/performance-monitor/sql-server-general-statistics-object?view=sql-server-ver16) の情報を取得。

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Batch Requests | mssql.stats_batch_requests | ○ | ○ | 1 秒間に受信した Transact-SQL コマンドバッチの数 |
| SQL Compilations | mssql.stats_sql_compilations | ○ | ○ | 1 秒あたりの SQL コンパイル数 |
| SQL Re-Compilations | mssql.stats_sql_recompilations | ○ | ○ | 1 秒あたりのステートメント再コンパイル数 |
| Connections | mssql.stats_connections | - | ○ | SQL Server に現在接続しているユーザー数 |
| Lock Waits | mssql.stats_lock_waits | - | ○ | 1 秒あたりの File IO Provider のロックの待機数 |
| Procs Blocked | mssql.stats_procs_blocked | - | ○ | 現在ブロックされているプロセスの数 |


### MSSQL Access

[Access Methods オブジェクト](https://learn.microsoft.com/ja-jp/sql/relational-databases/performance-monitor/sql-server-access-methods-object?view=sql-server-ver16) の情報を取得。

| メトリック表示名 | メトリック名                        | 差分 | 積み上げ表示 | 説明                                          |
| ---------------- | ----------------------------------- | ---- | ------------ | --------------------------------------------- |
| Page Splits | mssql.access_page_splits | ○ | ○ | インデックスページがオーバーフローした結果、1 秒間に発生したページ分割の数 |


<h2 id="options">指定可能なオプション</h2>

| オプション | 省略形 | 説明 | デフォルト値  |
| --- | --- | --- | --- | 
| -metric-key-prefix |  | メトリック名の prefix を指定 | mssql |
| -instance |  | インスタンス名を SQLSERVER か SQLEXPRESS のいずれかで指定。その他の名前には非対応 | SQLSERVER |
| -tempfile |  | tempfile の保存先ファイルパスの指定 |  |

tempfile はデフォルトでは `C:\Windows\Temp` 配下に `mackerel-plugin-mssql-<hash文字列>` の形式で作成されます。


<h2 id="config">エージェントへの設定例</h2>

下記はインスタンス名が SQLEXPRESS の場合の設定例です。

```
[plugin.metrics.mssql]
command = ["mackerel-plugin-mssql", "-instance", "SQLEXPRESS"]
```

<h2 id="repository">リポジトリ</h2>

https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-mssql
