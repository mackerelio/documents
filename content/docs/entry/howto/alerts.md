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

When a host retires, alerts related to that host will automatically be closed.

When a host status changes, automatic operations regarding the alert will not be performed.

Notifications will not be sent for alerts that occur when the host status is “standby”, even if the host status is changed to “working”. In this case, either the host status of the alert has changed afterward or the re-sending interval has been configured and a notification will be sent if the interval is passed.

In the event that a host status is changed to “maintenance” or “poweroff”, alerts that have already occurred will not close automatically, even if threshold abnormalities and communications are restored. Returning the host status to “working” or “standby”, alerts will automatically close once the abnormal state recovers.

## Connectivity Monitoring

Once [mackerel-agent](https://mackerel.io/docs/entry/howto/install-agent) has been installed and launched, connectivity monitoring of hosts will be done automatically.

Connectivity monitoring works by looking at intervals of metric data from mackerel-agent. In the event that no data is being reported from a host, Mackerel will determine the source of the problem and send an alert.

[install-agent]: https://mackerel.io/docs/entry/howto/install-agent

## Metric monitoring

Metric data (including custom metrics) can be monitored by setting thresholds, which are configured in two levels: warning and critical.

A threshold is generally configured as a limit greater than/less than a designated value, while CPU usage and memory usage thresholds will be set in relation to the percentage of the total value.

## Monitors

The following items are included in monitoring rules.

- Name
- Metric targeted by monitoring
- Warning parameters
  - Greater than/less than value
  - Threshold
- Critical parameters
  - Greater than/less than value
  - Threshold
- Average value monitoring
  - Monitor the average value of multiple metric values
- Services and roles to which the host being monitored belongs
- Number of maximum attempts
  - Have an alert will occur when the threshold is continously exceeded. The alert will close if the current state falls below the threshold even once.
- Notes
  - Write memos describing the intent of certain monitoring settings or how to deal with an alert when it occurs.
- Interval of notification retransmissions
  - Continue to have notifications sent at a set time interval while the alert is still occurring.

- A few things to keep in mind:
  - Monitoring threshold values can be changed at any time, but the target metric cannot be changed.
  - If a threshold is changed, alerts which were already raised will not be closed automatically, so it is necessary to close those alerts manually.
  - Hosts can be assigned to multiple services and roles. If no services or roles are designated, all hosts will be targeted by monitoring.
  - In the case that all roles to which a monitor’s target have been designated are deleted, the monitor’s target will become all hosts.
  - In the case of one metric being targeted by multiple monitors, each monitor will operate individually with its respective parameters.
  - If the timestamp of the latest metric is more than 20 minutes older than the current time or if metrics with a timestamp even older than previously sent metrics are sent, monitoring will not be carried out.

### Creating monitors

The list of all monitors can be viewed in the [Monitors][monitors] page. To create a new monitor, click the “New Monitor” button in the upper right hand corner, enter the new monitor’s parameters and then click “Create”.

[monitors]: https://mackerel.io/my/monitors

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

Notification settings can be edited in [Channels Settings][channels-settings]. Supported notification channels other than email and Webhook include Slack, Typetalk, Chatwork, PagerDuty and many more. We will continue to add more supported notification channels as need dictates.

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


-

If you have any questions please contact our support team at support@mackerel.io
