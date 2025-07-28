---
Title: Expression Monitoring
Date: 2016-08-01T17:01:45+09:00
URL: https://mackerel.io/docs/entry/expression-monitoring
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10328749687177048258
---

This feature allows you to monitor metrics calculated by an expression.
**This feature is a paid option. It is not available with the Free plan.** Also, it is currently an [experimental feature](https://mackerel.io/docs/entry/advanced/experimental-features).

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20160801/20160801115334.png)

## Expression monitoring specifications

- Metrics from two minutes prior to the current time will be monitored.
- The monitoring interval is every 5 minutes.
- Expressions configurable as monitoring items are limited to those that end up as a graph with a single line.
- If the metric used in the expression cannot be obtained, an Unknown alert will be generated.
- The maximum number of monitoring items is 20.
- Only available with the trial and paid plans.


## Configuring expression monitoring

Click the “New Monitor” button located on the right-hand side of the Monitors menu screen. By clicking on the “Expression monitoring”, the following items will be displayed. Enter each item and click the “Create” button.

- Scope
  - Expression
    - Enter the expression for calculating the monitored metric in the expression editor. If the expression is interpreted correctly, a graph will be drawn in the preview on the right.
  - Threshold
    - Configure the thresholds for Warning and Critical.
- Basic Settings
  - Monitor Name
    - Enter the name of the monitor.
  - Monitor Memo
    - You can freely describe the purpose of monitoring rules and how to respond when an alert occurs. This information will be displayed in the alert details screen and alert notifications.
- Options
  - Notification interval
    - If the alert conditions’ designated time is surpassed and no change is made, then a notification will be sent again.
  - Delay time until the expression result stabilizes
    - Monitors metrics from the specified time in the past.

Please refer to the following help for functions available in monitoring rules.

[https://mackerel.io/docs/entry/advanced/advanced-graph#functions:embed:cite]


<h2 id="future-predictions">Monitoring example：Monitoring the days remaining until the file system capacity is insufficient based on future prediction</h2>

In Mackerel, you can use linear regression functions such as `linearRegression()` and `timeLeftForecast()`.  As one example of expression monitoring, let’s try monitoring the number of days left until the file system’s free space is used up based on the future prediction features using these functions.

Use `timeLeftForecast()` to obtain the number of seconds before the linear regression value reaches the threshold.

Below is an example expression. 

```
scale(timeLeftForecast(host(host_id, filesystem.drive.used), 3mo, 2000000000000), 1/86400)
```

The expression above corresponds to "the value calculated with linear regression using three months of the `filesystem.drive.used` metric, to predict the days remaining until filesystem reaches 2TB."

Let’s take the expression apart and explain it piece by piece.

First of all, `host(host_id, filesystem.drive.used)` is used to obtain the `filesystem.drive.used` metric value for the specified host.

Let’s apply `timeLeftForecast()` to this. The parameters of `timeLeftForecast` are "metrics", "duration", and "threshold", so the example above corresponds to the number of seconds until the value reaches 2000000000000byte(2TB), using linear regression with three months worth data of the specified host's metric value.

Lastly, the number of seconds until the obtained file system becomes empty is converted into days using the `scale()` function.

In this way, expression monitoring lets the user monitor in a variety of different ways using the obtained metrics.
