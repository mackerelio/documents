---
Title: Monitoring with Anomaly Detection for roles
Date: 2018-05-21T14:23:05+09:00
URL: https://mackerel.io/docs/entry/howto/anomaly-detection-for-roles
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10257846132619083809
---

With this feature, hosts can be monitored using anomaly detection.

## Anomaly detection with machine learning
If the system administrator is familiar with server monitoring, [monitoring features](https://mackerel.io/docs/entry/howto/alerts) can be used to set a static monitor for server metrics (eg: a Warning alert occurs when loadavg5 exceeds 1). However, setting a monitor for individual metrics requires a certain amount of effort, and false alerts may occur unless periodically adjusted. Monitoring using machine learning can lower the cost of creating and maintaining monitors. Even if you’re not familiar with server monitoring, monitoring with anomaly detection is effective. With the anomaly detection feature, machine learning is used to train from past data to identify what kind of metric is normal/abnormal and automatically judge whether the newly sent metric is abnormal or not. If the metric is determined to be abnormal, an alert can be issued.

With the anomaly detection feature, metrics are judged to be abnormal or not based on machine learning (specifically, Gaussian mixture distribution). Gaussian mixture distribution considers multiple distributions, therefore it’s possible to handle cases where there is multimodality in server load (eg: weekday/weekend or daytime/nighttime).

In alerts for anomaly detection within the role, a graph is displayed that shows the most differential metrics of the corresponding host. This graph will be displayed in every type of notification and can be used for the initial stage response to a failure (This does not necessarily signify the root cause of the failure).

## Configure a monitor for anomaly detection for roles
Go to the Monitors screen, located on the left side menu, and click the “Add Monitor” button. Open the "Anomaly Detection for roles" tab and the following items will be displayed. Fill in a value/name for each item and click “Create”.

- Target role: Select the Service or Role you would like to monitor
- Sensitivity: `sensitive` will react to small changes and an alert will be reported. Set the sensitive value to `insensitive` if you only want alerts for large changes
- Monitor name：Enter a name for this monitor
- Monitor memo：Leave an optional note
- Notification interval：Alerts will be resent, even if the specified time is exceeded, if the status of the alert does not change
- Anomaly detection model training period: The anomaly detection model is trained using past metric data (about several weeks). Updating the training period when changes in trends occur as with deployment etc., may reduce false positives (eg: CPU utilization decreasing due to improvements in performance). The following three training period options are available.
  - Default training period (Uses metric data starting from past few weeks)
  - Keep the last specified training period (Uses metric data starting from the previously specified time)
    - Select this option if you do not want the training starting point to change when adjusting the sensitivity
  - Specified training period (Uses metric data starting from the specified time)
    - Select this option if a trend has changed significantly from the latest deployment

<img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190228/20190228114828_original.png" class="hatena-fotolife" itemprop="image" width=457>

## Monitoring specifications with anomaly detection for roles
- Only available for Linux hosts running mackerel-agent.
  - Training may fail when the role has unavailable hosts for anomaly detection
  - System metrics collected from mackerel-agent are used ([reference](https://mackerel.io/docs/entry/spec/metrics). Custom metrics and service metrics are not.
- Monitoring will not continue until the anomaly detection training has completed.
- Multiple monitors with anomaly detection can not be specified for the same role
- Only available for the Trial and Paid plans
  - Every 5 hosts targeted for monitoring will count as 1 Standard host
  - The same host will be counted multiple times if it is monitored through multiple roles
  - A usage charge will incur starting with one monitored host
