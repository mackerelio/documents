---
Title: カスタマイズしたグラフを表示する
Date: 2015-12-24T18:50:38+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/advanced-graph
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653586347149722196
---

拡張グラフパーマリンクのURLのクエリパラメータに簡単な式を書くことで、関数を使った柔軟なグラフを表示することが出来ます。([実験的機能](https://mackerel.io/ja/docs/entry/advanced/experimental-features))

拡張グラフパーマリンク以下のURLにて利用できます (title, unitパラメータは省略可能です)。

`https://mackerel.io/orgs/<オーガニゼーション名>/advanced-graph?query=<メトリック>&unit=<単位>&title=<タイトル>`

<h2 id="examples">例</h2>

### `example` オーガニゼーションの `example` サービス `db` ロールの `loadavg5` メトリックの平均値と、一週間前の同じメトリックを重ねたもの

```
query: group(alias(avg(role(example:db, loadavg5)), 'loadavg5'),
             alias(timeShift(avg(role(example:db, loadavg5)), 1w), '1 week ago'))
URL: https://mackerel.io/orgs/example/advanced-graph
       ?query=group(alias(avg(role(example:db, loadavg5)), 'loadavg5'),
                    alias(timeShift(avg(role(example:db, loadavg5)), 1w), '1%20week%20ago'))
       &title=example:db:loadavg5
```

<img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20151224/20151224190116_original.png" class="hatena-fotolife" itemprop="image" width=457>

<h2 id="functions">拡張グラフパーマリンクで使用可能な関数</h2>

| 関数                                  | 説明                                                                                                                                                       | 例                                                                                 |
| ----                                  | ----                                                                                                                                                       | ----                                                                               |
| `host(hostId, metricName)`            | ホストメトリックを返します                                                                                                                                 | `host(22CXRB3pZmu, memory.*)`                                                      |
| `service(serviceName, metricName)`    | サービスメトリックを返します                                                                                                                               | `service(Blog, access_count)`                                                      |
| `role(roleFullname, metricName)`      | ロールに現在所属しているホストのメトリックを返します                                                                                                       | `role(Blog:db, memory.*)`                                                          |
| `roleSlots(roleFullname, metricName)` | ロールのメトリックを返します。過去にロールに所属していたホストから送られたメトリックも取得できますが、一部のメトリック<a href="#1">*1</a>のみ使用できます。| `roleSlots(Blog:db, loadavg5)`                                                     |
| `avg(metrics)`                        | 各時刻ごとに引数のメトリックの平均したメトリックを返します                                                                                                 | `avg(group(host(22CXRB3pZmu, loadavg5), host(22CXRB3pZmu, loadavg5)))`             |
| `max(metrics)`                        | 各時刻ごとに引数のメトリックの最大値のメトリックを返します                                                                                                 | `max(host(22CXRB3pZmu, custom.foo.jobs.*))`                                        |
| `min(metrics)`                        | 各時刻ごとに引数のメトリックの最小値のメトリックを返します                                                                                                 | `min(host(22CXRB3pZmu, custom.foo.jobs.*))`                                        |
| `sum(metrics)`                        | 各時刻ごとに引数のメトリックの合計したメトリックを返します                                                                                                 | `sum(host(22CXRB3pZmu, custom.foo.jobs.*))`                                        |
| `product(metrics)`                    | 各時刻ごとに引数のメトリックを掛けあわせたメトリックを返します                                                                                             | `product(group(service(Blog, foo.bar), service(Blog, foo.baz)))`                   |
| `diff(metrics, metrics)`              | 各時刻ごとに1つ目の引数のメトリックから2つ目のメトリックを引いたメトリックを返します                                                                       | `diff(service(Blog, foo.bar), service(Blog, foo.baz))`                             |
| `divide(metrics, metrics)`            | 各時刻ごとに1つ目の引数のメトリックを2つ目の引数のメトリックで割ったメトリックを返します                                                                   | `divide(service(Blog, foo.bar), service(Blog, foo.baz))`                           |
| `scale(metrics, factor)`              | 定数倍したメトリックを返します                                                                                                                             | `scale(service(Blog, foo.bar), 10.0)`                                              |
| `offset(metrics, factor)`             | 定数足したメトリックを返します                                                                                                                             | `offset(service(Blog, foo.bar), 10.0)`                                             |
| `percentile(metrics, percentage)`     | 各時刻ごとに引数のメトリックの`percentage`パーセンタイルの値のメトリックを返します                                                                         | `percentile(role(Blog:db, loadavg5), 80)`                                          |
| `timeShift(metrics, duration)`        | 指定した期間分時刻をずらしたメトリックを返します                                                                                                           | `timeShift(service(Blog, foo.bar), 1d)`                                            |
| `movingAverage(metrics, duration)`    | 移動平均です                                                                                                                                               | `movingAverage(service(Blog, foo.bar), 1d)`                                        |
| `linearRegression(metrics, duration)` | 現在時刻から `duration` 前 までのメトリック値をもとに線形回帰したメトリックを返します                                                                      | `linearRegression(host(22CXRB3pZmu, loadavg5), 7d)`                                |
| `timeLeftForecast(metrics, duration, threshold)` | 現在時刻から `duration` 前 までのメトリック値をもとに線形回帰した値が `threshold` になるまでの残り秒数を返します                                | `scale(timeLeftForecast(host(22CXRB3pZmu, cpu.system.percentage), 7d, 10), 1/86400)` |
| `group(metrics, metrics, ...)`        | 引数のメトリック列を１つにまとめます                                                                                                                       | `group(service(Blog, foo.bar), service(Blog, foo.baz))`                            |
| `stack(metrics)`                      | グラフをスタック表示します                                                                                                                                 | `stack(service(Blog, foo.bar))`                                                    |
| `alias(metrics, displayName)`         | メトリックの表示名をカスタマイズします                                                                                                                     | `alias(service(Blog, foo.bar), 'Blog foo bar')`                                    |

<div id="1" style="position:relative; top:-80px;"></div>
<a href="#1">*1</a> `loadavg5`, `processor_queue_length`, `cpu.user.percentage`, `cpu.iowait.percentage`, `cpu.system.percentage`, `interface.rxBytes.delta`, `interface.txBytes.delta`, `disk.reads.delta`, `disk.writes.delta`, `memory.used`, `memory.cached`

全ての関数は metrics を返します。また関数の引数のパラメータは以下の通りです。

| 引数         | 型      | 説明                                                                                                               | 例                                             |
| ----         | ----    | ----                                                                                                               | ----                                           |
| hostId       | string  | ホストID                                                                                                           | `2CXRB3pZmu`, `'2CXRB3pZmu'`, `"22CXRB3pZmu"`  |
| metricName   | string  | メトリック名                                                                                                       | `loadavg5`, `'loadavg5'`                       |
| serviceName  | string  | サービス名                                                                                                         | `Blog`, `'Blog'`                               |
| roleFullname | string  | サービス名とロール名を`:`で連結したもの                                                                            | `Blog:db`, `'Blog:db'`                         |
| metrics      | metrics | メトリック列                                                                                                       | `alias(host(22CXRB3pZmu, loadavg5), 'L5')`     |
| displayName  | string  | 表示名                                                                                                             | `'blog max loadavg5'`                          |
| duration     | string  | 整数に続けて単位を指定した期間です。使用可能な単位は `m`(分), `h`(時間), `d`(日), `w`(週), `mo`(月), `y`(年)です。 | `5m`, `1d`                                     |
| factor       | float   | 係数                                                                                                               | `1.5`, `200`, `1/86400`                        |
| threshold    | float   | 閾値                                                                                                               | `1.5`, `200`, `1/86400`                        |
| percentage   | float   | パーセンテージ (0 < `percentage` < 100)                                                                            | `80`, `99.9`                                   |
