---
Title: Setting up monitoring and alerts
Date: 2014-11-14T18:18:34+09:00
URL: https://mackerel.io/docs/entry/howto/alerts
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450073673768
---

With Mackerel it’s possible to monitor your registered hosts by receiving notifications of alerts regarding the host status of hosts.

[:contents]

## Using the monitoring function effectively

* In the event that you are not being notified of an alert, please check the [alert settings][alerts-settings] page and [Account settings][settings-user] page to make sure everything is configured correctly.

[org-setting]: https://mackerel.io/my?tab=setting
[alerts-settings]: https://mackerel.io/my/monitors
[settings-user]: https://mackerel.io/settings/user

<h2 id="host-statuses">Monitoring targeted hosts</h2>

* Only hosts with their status set to “working” or “standby” will be monitored, whereas hosts with “maintenance” or “poweroff” status will not be targeted by monitoring.
* Settings for the default starting status can be configured under the Settings tab in your organization's page or it can be accessed directly through this link (https://mackerel.io/my?tab=setting). By setting the starting status as "working" you can begin receiving alert notifications immediately upon host registration.
* Mackerel does not send alert notifications for hosts with a status of “standby”, however you can view these alerts on the web.

| status | monitoring | notifications |
| ---------- | ---- | ---- |
| <span style="background: #4dbddb;font-size: 12px;letter-spacing: .5px;color: #fff;text-align: center;text-shadow: 0 1px 0 rgba(0,0,0,.2);width: 90px;border-radius: 2px;display: inline-block;font-weight: bold;">working</span> | yes | yes |
| <span style="background: #70bf16;font-size: 12px;letter-spacing: .5px;color: #fff;text-align: center;text-shadow: 0 1px 0 rgba(0,0,0,.2);width: 90px;border-radius: 2px;display: inline-block;font-weight: bold;">standby</span> | yes | no |
| <span style="background: #f4bd0b;font-size: 12px;letter-spacing: .5px;color: #fff;text-align: center;text-shadow: 0 1px 0 rgba(0,0,0,.2);width: 90px;border-radius: 2px;display: inline-block;font-weight: bold;">maintenance</span> | no | no |
| <span style="background: #c6cacc;font-size: 12px;letter-spacing: .5px;color: #fff;text-align: center;text-shadow: 0 1px 0 rgba(0,0,0,.2);width: 90px;border-radius: 2px;display: inline-block;font-weight: bold;">poweroff</span> | no | no |

### About alerts when a host retires or the host status changes

When a host retires, alerts related to that host will automatically be closed. Recovery notifications for this automatically closed alert will not be sent. 

When a host status changes, automatic operations regarding the alert will not be performed.

Notifications will not be sent for alerts that occur when the host status is “standby”, even if the host status is changed to “working”. In this case, either the host status of the alert has changed afterward or the re-sending interval has been configured and a notification will be sent if the interval is passed.

In the event that a host status is changed to "maintenance" or "poweroff", alerts that have already occurred will not close automatically, even if threshold abnormalities and communications are restored. Returning the host status to "working" or "standby", alerts will automatically close once the abnormal state recovers.

## Creating monitors

You can view a list of monitors in the [Monitors][monitors] page. To create a new monitor, click the "New Monitor" button in the upper right corner.

[monitors]: https://mackerel.io/my/monitors

### Connectivity monitor

Once [mackerel-agent](https://mackerel.io/docs/entry/howto/install-agent) has been installed and launched, connectivity monitoring of hosts will be done automatically.

Connectivity monitoring works by looking at intervals of metric data from mackerel-agent. In the event that no data is being reported from a host, Mackerel will determine the source of the problem and send an alert.

[install-agent]: https://mackerel.io/docs/entry/howto/install-agent

### Host metric monitor and Service metric monitor

You can monitor metrics posted to hosts or services by setting thresholds, which are configured in two levels: Warning and Critical.

The following items are included in monitoring rules.

- Metric
  - Specify the metric to be monitored.
    - For metric types, see [Metric specifications](https://mackerel.io/docs/entry/spec/metrics).
    - The target metric cannot be changed after creating the monitoring rule.
  - Some metrics can be monitored by usage rate.
    - [About "metric rate monitors"](https://support.mackerel.io/hc/en-us/articles/360040107951)
- Threshold
  - Warning condition and Critical condition
    - Specify the comparison operator and threshold. If Warning and Critical are set to the same condition, a Critical alert will occur.
  - Average value monitoring
    - Monitor the average value from the latest metric value, going back the specified number of points in the past. If you specify 1 point, it will monitor the latest metric value.
    - For details, see [How to calculate average values in average value monitoring](#calculate-average-value).
- Maximum number of attempts before alert occurs
  - An alert will occur when the metric value continuously exceeds the threshold. The alert will close if the value falls below the threshold even once.
- Filter (Host metric monitoring only)
  - Specify the services and roles to which the monitored host belongs. If none are specified, all hosts will be monitored.
- Interruption monitoring (Service metric monitoring only)
  - An alert will occur when the specified time elapses after metric posting is interrupted.
- Basic settings
  - Monitor name
    - The name of the monitoring rule. Duplicates are allowed.
  - Monitor memo
    - You can freely describe the intent of the monitoring rule or how to respond when an alert occurs. This content will be displayed on the alert details screen and in alert notifications.
- Options
  - Notification re-sending interval
    - While the alert is still occurring, you can continue to send notifications at the set time interval.

- Notes:
  - Monitoring rules are applied to hosts where the target metric has been posted. They will not be applied to hosts where the target metric has never been posted.
    - If average value monitoring is specified, the rule will be applied when the specified number of metrics have been posted.
  - If a threshold is changed, alerts which were already raised will not be closed automatically, so it is necessary to close those alerts manually.
  - In the case of one metric being targeted by multiple monitors, each monitor will operate individually with its respective parameters.
  - If the timestamp of the latest metric is more than 20 minutes older than the current time or if metrics with a timestamp even older than previously sent metrics are sent, monitoring will not be carried out.

<h4 id="calculate-average-value">How to calculate average values in average value monitoring</h4>

The average value is calculated from the total value of the most recent specified number of metrics, including the latest metric. Note that it is not based on the specified time (in minutes).

The following is an example showing which timestamp values are used by average value monitoring to calculate the average when the timestamp of the latest metric is 15:10.

| Timestamp | Value | 3-points avg | 5-points avg | 10-points avg |
|-----------|-------|-------------|-------------|--------------|
| 15:00     | 1     |             |             | ✓            |
| 15:01     | 1     |             |             | ✓            |
| 15:02     | 1     |             |             | ✓            |
| 15:03     | 1     |             |             | ✓            |
| 15:04     | 1     |             |             | ✓            |
| 15:05     | 1     |             | ✓           | ✓            |
| 15:06     | No post |           |             |              |
| 15:07     | 1     |             | ✓           | ✓            |
| 15:08     | 1     | ✓           | ✓           | ✓            |
| 15:09     | 1     | ✓           | ✓           | ✓            |
| 15:10     | 1     | ✓           | ✓           | ✓            |

Additionally, when the timestamp of the latest metric becomes 15:11 at the next monitoring timing, the average value will be calculated from the values at the following timestamps.

- 3-points average
  - 15:11, 15:10, 15:09
- 5-points average
  - 15:11, 15:10, 15:09, 15:08, 15:07
- 10-points average
  - 15:11, 15:10, 15:09, 15:08, 15:07, 15:05, 15:04, 15:03, 15:02, 15:01

**Note**

Even if the metric timestamps are not at 1-minute intervals, the calculation method is the same, but if the specified number of metrics do not exist within 24 hours, the average value cannot be calculated. For example, if a metric is posted only once a day, average value monitoring of 2 or more points will not work.

#### Behavior when multiple metrics with different timestamps are posted simultaneously

When multiple metrics with different timestamps are posted simultaneously via cloud integration or the metric posting API, the monitoring rule monitors the value of the latest metric at that time.

For example, when 5 minutes of metrics are posted simultaneously as shown below, the monitoring rule operates on the value at 15:05. Even if values before 15:05 exceed the monitoring condition threshold, an alert will not occur.

| Timestamp | Value |
|-----------|-------|
| 15:01     | 1     |
| 15:02     | 2     |
| 15:03     | 3     |
| 15:04     | 4     |
| 15:05     | 0     |

Average value monitoring and the maximum number of attempts before alert occurs also operate based on the latest metric value.

- When average value monitoring is 3 points, it is calculated from the values at 15:03, 15:04, and 15:05.
  - Even if the average value exceeds the threshold before 15:05, an alert will not occur if the average value including 15:05 is below the threshold.
- When the maximum number of attempts before alert occurs is 3 times, it determines whether the values at 15:03, 15:04, and 15:05 continuously exceed the threshold.
  - Even if the threshold is continuously exceeded before 15:05, an alert will not occur if the value at 15:05 is below the threshold.

Please note that due to this specification, there may be cases where an alert does not occur even though it appears on the graph that an alert should occur.

### External URL monitor

See [External URL monitoring](https://mackerel.io/docs/entry/external-monitoring).

### Expression monitor

See [Expression monitoring](https://mackerel.io/docs/entry/expression-monitoring).

### Anomaly Detection for roles

See [Anomaly detection for roles](https://mackerel.io/docs/entry/howto/anomaly-detection-for-roles).

### Query monitor

See [Query monitoring](https://mackerel.io/docs/entry/query-monitoring).

## Checking alerts

Alerts can be checked by going to the [“Alerts”][alerts] page.

### Closing alerts

If an alert has come up but is no longer meeting the parameters set for that alert (i.e. if connectivity to a host has been restored, or if a metric has returned to a value within the threshold) that alert will automatically be closed. Alerts can also be closed manually by clicking the "Close this alert" button either in the ["Alerts"][alerts] page or in an alert dialogue window.

[alerts]: https://mackerel.io/my/alerts

### Disabling alert notifications

Alert notifications can be temporarily or permanently suspended by going to [Monitors][alerts-settings] and opening the drop down menu by clicking the arrow next to the “Turn off notifications” button. If alert notification is set to "disabled" or "suspended", notifications will not be sent in the event an alert is raised. (Alerts, however, will continue to function as normal)

### Consolidate alerts with Alert groups
Please refer to the document below about the Alert group feature that lets you consolidate alerts.
[https://mackerel.io/docs/entry/howto/alert-groups:embed]

## Notifications

Mackerel will notify you in the event that an alert has been raised, resolved/closed, or if the host status of an alert has changed.

Notification settings can be edited in [Channels Settings][channels-settings]. Supported notification channels other than email and Webhook include Slack, Chatwork, PagerDuty and many more. We will continue to add more supported notification channels as need dictates.

[channels-settings]: https://mackerel.io/my/channels/-/create
[alerts-settings]: https://mackerel.io/my/monitors

With notification by email broadcast, all members belonging to your organization will be notified simultaneously (this can be changed in [Account Settings](#optout-broadcast-notification-email) so as not to be notified). For details on setting up other notification channels, please refer to the Alerts section of the Help Documents sidebar.

<h3 id="optout-broadcast-notification-email">To stop receiving notification emails (Account Settings)</h3>

To stop receiving notification emails you can change your preferences in [Account Settings][settings-user].

You can choose not to receive notification emails by setting "Alert Notification" to Off in the [Email Notifications](https://mackerel.io/settings/user?tab=emailNotifications) section of the [Account Settings][settings-user] page. This setting applies to all "Events to be notified" in the channel settings.

[settings-user]: https://mackerel.io/settings/user
[alerts-settings]: https://mackerel.io/my/monitors

<h3 id="mute-notification">Suppress monitor notifications</h3>
You can suppress (mute) notifications for individual monitors by operating from the [monitor list screen][monitors].

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20171213/20171213163701.png)

This can also be configured from the [Web API](https://mackerel.io/api-docs/entry/monitors). Utilize this when wanting to temporarily suppress notifications.

<h2 id="notification-group">Notification Groups</h2>

With Mackerel’s notification groups, it’s possible to configure even more flexible custom notifications, for example setting alert notifications from only one specific service to be sent to one team’s notification channel. Just like regular notification channels, notification groups can also be created in the [Channels Settings][channels-settings] page.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190204/20190204165519.png)

Only notifications pertaining to the services and monitoring rules which have been configured will be sent to the channels included in that notification group.

### Notification group that receives all notifications

In Mackerel there is a default notification group that will receive all notifications associated with that whole organization. When setting up a new notification channel it will automatically be included in the default notification group.

### Limit the target notification group

When setting a monitoring rule as the notification target for a notification group, if the "Send notifications to this notification group only." option is enabled by clicking the icon to the left of the monitoring rule name, notifications from the set monitoring rule will be notified only to that notification group and not to other notification groups.

<figure class="figure-image figure-image-fotolife" title="Configuration"><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230314/20230314112410.png" width="500" height="auto" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span><figcaption>Configuration</figcaption></figure>


### Example configuration

For example, the configuration for [alerts from `CPU%` and `connectivity` monitoring rules are sent to the appropriate team and all other notifications go to email and Slack] would look like this:

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190205/20190205151657.png)

### Notification level
If `Critical only` is On, Warning notifications will not be sent. Detailed notification conditions are as follows.

- Notifications will be sent for error levels of Critical or higher (Critical, Unknown)
- OK notifications will be sent for alerts that have previously exceeded Critical

## Channel Suspension

When notification failures to a specific notification channel continue for a certain period of time, that notification channel will be put into a **suspended** state. Notifications to suspended notification channels will not be sent.

When there are suspended notification channels, a warning will be displayed in the "Channels" section of the side menu and on the channel list page.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20260120/20260120124009.png)

To unsuspend, click the ⚠️ icon next to the notification channel.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20260120/20260120124107.png)

**Note**: If you unsuspend without resolving the cause of the notification failure, it will be suspended again. Be sure to resolve the cause of the notification failure before unsuspending.
