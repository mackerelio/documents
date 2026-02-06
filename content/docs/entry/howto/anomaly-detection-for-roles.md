---
Title: Monitoring with Anomaly Detection for roles
Date: 2018-05-21T14:23:05+09:00
URL: https://mackerel.io/docs/entry/howto/anomaly-detection-for-roles
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10257846132619083809
---

"Anomaly Detection for roles" is a feature that learns the trend of past metrics in a specific role and issues an alert when a newly posted metric deviates from past trends.

## Anomaly detection with machine learning
If the system administrator is familiar with server monitoring, [monitoring features](https://mackerel.io/docs/entry/howto/alerts) can be used to set a static monitor for server metrics (eg: a Warning alert occurs when loadavg5 exceeds 1). However, setting a monitor for individual metrics requires a certain amount of effort, and false alerts may occur unless periodically adjusted. Monitoring using machine learning can lower the cost of creating and maintaining monitors. Even if you’re not familiar with server monitoring, monitoring with anomaly detection is effective. With the anomaly detection feature, machine learning is used to train from past data to identify what kind of metric is normal/abnormal and automatically judge whether the newly sent metric is abnormal or not. If the metric is determined to be abnormal, an alert can be issued.

With the anomaly detection feature, metrics are judged to be abnormal or not based on machine learning (specifically, Gaussian mixture distribution). Gaussian mixture distribution considers multiple distributions, therefore it’s possible to handle cases where there is multimodality in server load (eg: weekday/weekend or daytime/nighttime).

In alerts for anomaly detection within the role, a graph is displayed that shows the most differential metrics of the corresponding host. This graph will be displayed in every type of notification and can be used for the initial stage response to a failure (This does not necessarily signify the root cause of the failure).

For more information about anomaly detection for roles, please refer to the following blog post.

[https://mackerel.io/blog/entry/anomaly-detection-for-roles/about:embed:cite]

## Configure a monitor for anomaly detection for roles
On the [Monitor Settings screen](https://mackerel.io/my/monitors), click the "Add Monitor" button. On the screen to select the type of monitoring rule, click "Anomaly Detection for roles".

The following items can be configured when creating a monitoring rule:

- Target role
  - Select the Service and Role you would like to monitor
  - **Note**: Multiple anomaly detection monitors cannot be configured for a single role
- Sensitivity
  - Changes will be more reactive in the order of sensitive > normal > insensitive. If you only want alerts for large changes, set this to insensitive
- Monitor name
  - Enter a name for this monitor
- Monitor memo
  - Leave an optional note for this monitor. This will be displayed on the alert details screen and in alert notifications
- Notification interval
  - While an alert is occurring, notifications will be resent at the specified interval

### About the Anomaly detection model training period

This option cannot be selected when creating a monitoring rule. By default, "Use the default period" is configured.

After creating the monitoring rule, you can select from the following options on the edit screen:

- Use the default period
  - Training will begin a few days after configuration
- Data starting from the specified time will be used for training
  - Training will be conducted on the period from the specified date and time to the present (immediate execution)
  - When trends in metrics change significantly due to deployment etc., specifying a date after deployment as the training period target may reduce false positives
    - Example: CPU utilization decreased due to performance improvements

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20260202/20260202143611.png)

<h2 id="hosttype">Monitoring specifications with anomaly detection for roles</h2>

- Available only for the Trial period and Paid plans
  - For usage fees, please refer to the "Anomaly Detection for roles" section of [How usage fees are calculated](https://support.mackerel.io/hc/en-us/articles/31304727432729-How-usage-fees-are-calculated)
- Hosts running mackerel-agent are subject to anomaly detection for roles
  - **Windows hosts are offered as an experimental feature.** During its period as an experimental feature, they will not be counted in the number of hosts
- The anomaly detection model learns trends from past metrics
  - [System metrics](https://mackerel.io/docs/entry/spec/metrics#system-metric) collected from mackerel-agent are used for learning and detection. Custom metrics and service metrics are not used
  - Training is performed periodically (every few days) after configuring the monitoring rule
    - Monitoring will not be performed until the initial training is completed
    - By hovering over the icon next to the monitoring rule name, you can check the current status. When monitoring is ready, it will be displayed as follows:
    ![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20260202/20260202143602.png)
  - Training data older than several tens of days will no longer be used
- If a role contains hosts with different functions (such as applications and databases), anomalies may not be detected correctly. **When using anomaly detection for roles, we recommend assigning roles for each host function**
