---
Title: Query Monitoring
Date: 2024-02-05T16:07:16+09:00
URL: https://mackerel.io/docs/entry/query-monitoring
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6801883189090642255
---

Query monitoring is a feature that allows you to monitor labeled metrics posted to Mackerel. **This feature is a paid option. It is not available with the Free plan.** As of February 2024, this feature is provided as a public beta.

For information on how to post labeled metrics to Mackerel, see [Posting metrics to Mackerel](https://mackerel.io/docs/entry/howto/post-metrics).

## Setting up query monitoring

Click the “New Monitor” button located on the right-hand side of the Monitors menu screen. By clicking on the “Query monitoring” tab, the following items will be displayed. Fill out the value and title for each item, then click the “Create” button.

- Monitoring target: Enter the query to obtain the monitoring target metric in the editor. If the query is correctly interpreted and the calculation target metric is obtained, a graph will be drawn on the right-hand side of the preview.
- Alert conditions: Set the thresholds for Warning and Critical.
- Monitor Name: Enter the name of the monitor.
- Notification interval: If the alert conditions’ designated time is surpassed and no change is made, then a notification will be sent again.
- Latency of metrics to monitor: To absorb the difference in the reflection time of the monitoring metric, the time of the monitoring target is delayed.

For information on the PromQL features available in monitoring rules, see [Supported PromQL features](https://mackerel.io/docs/entry/howto/labeled-metrics/promql).

## Specifications for query monitoring

- The monitoring interval is 1 minute.
- For monitoring items, you can specify multiple series of graphs. If multiple series of graphs are specified, the thresholds for each series will be verified, and the occurrence of an alert will be determined. Therefore, multiple alerts may occur for a single monitoring rule.
- You can use the label's [template syntax](#template-syntax) in the monitoring rule name.
- The evaluation of the query is always performed within a 20-minute range. Therefore, due to the [constraints on range vector selectors](https://mackerel.io/docs/entry/howto/query/promql#constraints-on-range-vector-selectors), if a wide range (`[1w]`, etc.) is specified in the range vector selector, the query evaluation may not be performed correctly, and an `unknown` alert may occur.

## Template syntax

You can use the label's template syntax in the monitoring rule name. By specifying the key of the metric label in the part surrounded by `{{` and `}}`, you can embed the value of the label in the monitoring rule name when an alert occurs.

For example, set the monitoring rule name as follows:

```
http.server.request.duration [{{http.request.method}}] {{http.route}}
```

In this case, if an alert occurs for a metric with the label `http.request.method=GET` and `http.route=/api/v1/users`, the monitoring rule name will be as follows and will be displayed in the alert notification.

```
http.server.request.duration [GET] /api/v1/users
```

By using this feature, you can instantly understand which metric the alert occurred for.
