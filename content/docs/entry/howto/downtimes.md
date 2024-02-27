---
Title: Configuring Downtime
Date: 2019-07-16T13:00:00+09:00
URL: https://mackerel.io/docs/entry/howto/downtimes
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613439795642
---

You can temporarily pause monitoring for specified times or periods, preventing alerts from being issued for anticipated events such as batch processing or maintenance periods.

[:contents]

## Specifications

- During the downtime period, no alerts will be generated or notified for monitoring rules that match the specified conditions.
  - This feature does not inhibit the operations of agents, such as posting metrics or executing check monitoring; these activities will continue throughout the downtime period.
- There is no limit to the number of downtime configurations.

## Configuring downtime

This can be configured from the [Create Downtime](https://mackerel.io/my/downtimes) screen.

[f:id:mackerelio:20240220114636p:plain]

### Basic configuration

| Configuration item | Description |
| --- | --- |
| Name | Specify the name of downtime configuration. |
| Note | Leave notes about the downtime configuration, including details and objectives. |

### Downtime period

| Configuration item | Description |
| --- | --- |
| Start date and time | Specify the start date and time for the downtime.<br />If you want to enable downtime on the last day of the month, set the date to the `31st`; downtime will be enabled even if the last day of the month is the 28th, 29th, or 30th. |
| Downtime period | Specify the duration of the downtime starting from the start date and time. |
| Recurrence settings | Set to `Enabled` if the downtime should repeat at regular intervals. |

When repeat setting is set to `Enabled`, the following items can be specified:

| Configuration item | Description |
| --- | --- |
| Every | Specify the interval at which the downtime repeats as `hour`, `day`, `week`, `month`, `year`.<br />If `week` is selected, please check the specific days of the week. |
| End date and time of the downtime | Specify the end date and time for the downtime. Select `Disabled` for indefinite repetition. |

### Scope refinement

| Configuration item | Description |
| --- | --- |
| Services / Roles | Specify the services / roles to be included or excluded from the downtime. |
| Monitors | Specify the monitoring rules to be included or excluded from the downtime. |

Specifying refinement settings are AND conditions.

## Notes

- Monitoring matching the conditions will be completely halted during the downtime period. No alerts will be issued or notified retrospectively after the period ends.
  - Even if events related to suppressed alerts are resolved after the downtime period, no notification to close the alert will be issued since no alert was raised.
- Please note that due to Mackerel specifications, if downtime starts on Sunday and exceeds 24 hours, downtime will be ignored after 24 hours.
  - For example, if the downtime start time is set to Sunday `00:00` and the downtime period is set to `24 hours and 30 minutes`, monitoring will not stop on Monday from 00:00 to 00:30. The same is true for a repeating setting.
  - For downtime beginning on Sunday, the duration should not exceed 24 hours.
