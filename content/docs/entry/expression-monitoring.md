---
Title: Expression Monitoring
Date: 2016-08-01T17:01:45+09:00
URL: https://mackerel.io/docs/entry/expression-monitoring
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10328749687177048258
---

This feature allows you to monitor metrics calculated by an expression.
**This feature is a paid option. It is not available with the Free plan.** Additionally, as of April 2018, this feature has become an [experimental feature](https://mackerel.io/docs/entry/advanced/experimental-features). 

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20160801/20160801115334.png)

## Configuring expression monitoring 
Click the “New Monitor” button located on the right-hand side of the Monitors menu screen. By clicking on the “Expression monitoring” tab, the following items will be displayed. Fill out the value and title for each item, then click the “Create” button. 

- Monitoring target: Enter the expression for calculating the target metrics in the expression editor. If the expression is read correctly and the target metrics are obtained, then a graph will be drawn up in the right side preview.  
- Alert conditions：Configure the thresholds for Warning and Critical.
- Notification interval：If the alert conditions’ designated time is surpassed and no change is made, then a notification will be sent again.
- Monitor Name：Enter the name of the monitor. 

Please refer here for available functions in the monitoring settings.

[https://mackerel.io/docs/entry/advanced/advanced-graph#functions:embed:cite]

## Expression monitoring specifications

- The monitoring interval is every 5 minutes 
- Expressions configurable as monitoring items are limited to those that end up as a graph with a single line
- If the expression metric values can’t be obtained or a time-out occurs because the expression is too complex etc., an alert will occur with the status as Unknown
- The maximum number of monitoring items is 20
- Only available with the trial and paid plans

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
