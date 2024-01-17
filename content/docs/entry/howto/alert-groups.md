---
Title: Consolidate alerts with Alert groups
Date: 2018-06-29T20:51:36+09:00
URL: https://mackerel.io/docs/entry/howto/alert-groups
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10257846132596404203
---

Alert groups is a feature that lets you consolidate alerts that occur within a particular role or alerts with the same monitor settings.
Additionally, enabling notifications for alert groups makes it easier to get notifications with the chat tool.



For more on monitoring settings, alerts, and notification channels, refer to the following document.
[https://mackerel.io/docs/entry/howto/alerts:embed]


## Creating alert group settings
The alert group feature consolidates alerts based on the rules explicitly created by the user.
First, create a alert group setting in the [Alert group setting’s creation page](https://mackerel.io/my/alert-group-settings/-/create).

In the Alert group settings page, specify the target service, role, or monitor setting.

- Specify the service: Create an alert group to consolidate alerts that occur within the target service. Multiple services can be specified. This is useful for looking back on alerts in chronological order after a failure occurs.
- Specify the role: Create an alert group to consolidate alerts that occur for hosts belonging to the target role. By doing so, you can prevent the chat tool from being filled with alert notifications, particularly when a large number of hosts belong to the role.
- Specify monitor settings: If notifications with specific monitor settings often occur at the same time on multiple hosts, you can filter the monitor settings and create an alert group. If this is configured together with service and role specification, it becomes an AND condition.

## Verifying alert groups
Once an alert group is created, you can check the alert groups that are currently Open in the [Alerts list](https://mackerel.io/my/alerts).
[f:id:mackerelio:20180627160114p:plain:w750]
[f:id:mackerelio:20180627155033p:plain:w750]

You can also check past alert groups from the [Alert groups list page](https://mackerel.io/my/alert-groups).

## Notifying alert groups with notification channels
By enabling alert group notifications in the [Channels List](https://mackerel.io/my/channels), alert group notifications will be sent.
Alerts that are affiliated with the alert group will no longer be notified.
If you want to receive affiliated alert notifications while also receiving alert group notifications, a separate channel needs to be created.
Channels that can notify alert groups are: [Slack](https://mackerel.io/docs/entry/howto/alerts/slack), email, [Chatwork](https://mackerel.io/docs/entry/howto/alerts/chatwork), [LINE](https://mackerel.io/docs/entry/howto/alerts/line), [Typetalk](https://mackerel.io/docs/entry/howto/alerts/typetalk), [Microsoft Teams](https://mackerel.io/docs/entry/howto/alerts/microsoft-teams), and [Webhook](https://mackerel.io/docs/entry/howto/alerts/webhook).

[f:id:mackerelio:20180627152007p:plain:w500]

| Alert notifications | Alert group notifications |                                                                                    |
| ------------ | -------------------- | ---------------------------------------------------------------------------------- |
| Disabled          | Disabled                  | Alerts · Alert groups will not be notified                                         |
| Enabled         | Disabled                  | Alerts will be notified individually                                                       |
| Disabled          | Enabled                 | Alert groups will be notified. Alerts unaffiliated to an alert group will not be notified |
| Enabled         | Enabled                 | Alert groups and unaffiliated alerts will be notified               |

