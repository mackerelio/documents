---
Title: メトリックプラグイン - mackerel-plugin-accesslog
Date: 2022-12-15T18:00:15+09:00
URL: https://mackerel.io/ja/docs/entry/plugins/mackerel-plugin-accesslog
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/4207112889945342597
---

mackerel-plugin-accesslog は Web サーバーのアクセスログを毎分集計し、ステータスコード毎の件数などをメトリックとして監視できます。

[:contents]

<h2 id="log-format">対応するログフォーマット</h2>

- Apache や Nginx などの一般的な Apache 形式のログ （Common / Combined）
- LTSV


<h2 id="metrics">監視できるメトリック</h2>

### Access Num

ステータスコード毎のアクセス数を監視できます。

| メトリック表示名 | メトリック名 | 差分 | 積み上げ表示 | 説明 |
| --- | --- | --- | --- | --- |
| Total Count | custom.accesslog.access_num.total_count | - | - | アクセスの総数 |
| HTTP 2xx Count | custom.accesslog.access_num.2xx_count | - | ◯ | 2xx のアクセス数 |
| HTTP 3xx Count | custom.accesslog.access_num.3xx_count | - | ◯ | 3xx のアクセス数 |
| HTTP 4xx Count | custom.accesslog.access_num.4xx_count | - | ◯ | 4xx のアクセス数 |
| HTTP 5xx Count | custom.accesslog.access_num.5xx_count | - | ◯ | 5xx のアクセス数 |

### Access Rates

ステータスコード毎の割合を監視できます。

| メトリック表示名 | メトリック名 | 差分 | 積み上げ表示 | 説明 |
| --- | --- | --- | --- | --- |
| HTTP 2xx Percentage | custom.accesslog.access_rate.2xx_percentage | - | ◯ | 2xx の割合 |
| HTTP 3xx Percentage | custom.accesslog.access_rate.3xx_percentage | - | ◯ | 3xx の割合 |
| HTTP 4xx Percentage | custom.accesslog.access_rate.4xx_percentage | - | ◯ | 4xx の割合 |
| HTTP 5xx Percentage | custom.accesslog.access_rate.5xx_percentage | - | ◯ | 5xx の割合 |

### Latency（LTSV フォーマットのみ対応）

レイテンシを監視できます。

| メトリック表示名 | メトリック名 | 差分 | 積み上げ表示 | 説明 |
| --- | --- | --- | --- | --- |
| Average | custom.accesslog.latency.average | - | - | 平均 |
| 90 Percentile | custom.accesslog.latency.90_percentile | - | - | 90 パーセンタイル |
| 95 Percentile | custom.accesslog.latency.95_percentile | - | - | 95 パーセンタイル |
| 99 Percentile | custom.accesslog.latency.99_percentile | - | - | 99 パーセンタイル |


<h2 id="options">指定可能なオプション</h2>

| オプション | 説明 | デフォルト値 |
| --- | --- | --- | 
| --format | アクセスログのフォーマットを apache もしくは ltsv で指定 | 自動判別 |
| --metric-key-prefix | メトリック名の接頭辞の指定 | accesslog |
| --posfile | pos ファイルの保存先ディレクトリパスを指定 | 欄外参照 |
| --no-posfile | pos ファイルを使用せずにすべてのログを集計の対象にする | 指定なし |

- pos ファイルについて
  - 最新のログの集計結果が保存されます。次回実行時はその差分がメトリックとして投稿されます。
  - デフォルトでは `/var/tmp/mackerel-agent/mackerel-plugin-accesslog.d` に保存されます。

<h2 id="config">エージェントへの設定例</h2>

アクセスログ `/path/to/access.log` を監視する場合は以下のようになります。

```
[plugin.metrics.accesslog]
command = ["mackerel-plugin-accesslog", "/path/to/access.log"]
```


<h2 id="repository">リポジトリ</h2>

[https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-accesslog]
