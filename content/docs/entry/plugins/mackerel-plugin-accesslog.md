---
Title: Metric plugins - mackerel-plugin-accesslog
Date: 2022-12-15T17:59:58+09:00
URL: https://mackerel.io/docs/entry/plugins/mackerel-plugin-accesslog
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/4207112889945342147
CustomPath: plugins/mackerel-plugin-accesslog
---

mackerel-plugin-accesslog aggregates the web server access logs every minute and allows you to monitor metrics such as the number of accesses per status code.

[:contents]

<h2 id="log-format">Supported log formats</h2>

- Logs in common Apache formats, such as Apache and Nginx (Common / Combined)
- LTSV


<h2 id="metrics">Monitorable metrics</h2>

### Access Num

Counts of accesses per status code.

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| --- | --- | --- | --- | --- |
| Total Count | accesslog.access_num.total_count |  |  | Total number of accesses |
| HTTP 2xx Count | accesslog.access_num.2xx_count |  | ✓ | Number of status code 2xx |
| HTTP 3xx Count | accesslog.access_num.3xx_count |  | ✓ | Number of status code 3xx |
| HTTP 4xx Count | accesslog.access_num.4xx_count |  | ✓ | Number of status code 4xx |
| HTTP 5xx Count | accesslog.access_num.5xx_count |  | ✓ | Number of status code 5xx |

### Access Rates

Percentage of each status code.

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| --- | --- | --- | --- | --- |
| HTTP 2xx Percentage | accesslog.access_rate.2xx_percentage |  | ✓ | Percentage of 2xx |
| HTTP 3xx Percentage | accesslog.access_rate.3xx_percentage |  | ✓ | Percentage of 3xx |
| HTTP 4xx Percentage | accesslog.access_rate.4xx_percentage |  | ✓ | Percentage of 4xx |
| HTTP 5xx Percentage | accesslog.access_rate.5xx_percentage |  | ✓ | Percentage of 5xx |

### Latency (Available only LTSV)

Latency.

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| --- | --- | --- | --- | --- |
| Average | accesslog.latency.average |  |  | Average |
| 90 Percentile | accesslog.latency.90_percentile |  |  | 90 percentile |
| 95 Percentile | accesslog.latency.95_percentile |  |  | 95 percentile |
| 99 Percentile | accesslog.latency.99_percentile |  |  | 99 percentile |


<h2 id="options">Configurable options</h2>

| Option | Description | Default |
| --- | --- | --- |
| --format | Specify the format of the access log at apache or ltsv. | auto |
| --metric-key-prefix | Specify metric name prefix. | accesslog |
| --posfile | Directory path where posfile are saved. | See beside the column. |
| --no-posfile | Aggregate for all logs without using posfile. | false |

- About posfile
  - The aggregate results of the latest log will be saved. The next time it is run, the difference will be posted as a metric
  - By default, it is stored in `/var/tmp/mackerel-agent/mackerel-plugin-accesslog.d`


<h2 id="config">Example configuration</h2>

To monitor the access log `/path/to/access.log`, do the following.

```
[plugin.metrics.accesslog]
command = ["mackerel-plugin-accesslog", "/path/to/access.log"]
```


<h2 id="repository">Repository</h2>

[https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-accesslog]
