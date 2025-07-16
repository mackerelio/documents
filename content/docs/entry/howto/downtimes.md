---
Title: Configuring Downtime
Date: 2019-07-16T13:00:00+09:00
URL: https://mackerel.io/docs/entry/howto/downtimes
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613439795642
---

The downtime feature suppresses alerts that match specified conditions during a specified period. This is useful for suppressing alerts that would otherwise be generated due to planned server maintenance, for example.

[:contents]

## Specifications

- During the downtime period, alerts won't be generated for hosts or monitors that match the specified "filter" conditions.
- Alerts suppressed during downtime won't be triggered even after the downtime period ends.
  - For example, when using host metric monitoring, alerts won't be triggered in the following scenarios:
    - If a threshold is exceeded during downtime and remains exceeded when the downtime period concludes.
    - If a threshold is exceeded during downtime, then falls below it while downtime is still active.
    - If a threshold is exceeded during downtime and then falls below it after the downtime period concludes.
  - For uptime monitoring only, if communication with a host is lost during downtime and remains lost after the downtime period ends, an alert will be triggered.
- If a monitor has a interval of notification retransmissions, notifications will be paused during the downtime period, even if the alert was triggered before downtime began.
- There's no upper limit to the number of downtime settings you can create.

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

#### Notes

- If a downtime period is 24 hours or longer and spans from Sunday to Monday, a portion of the downtime will become invalid, depending on the start time. The invalid portion will be either after 24 hours from the start, or after Monday 0:00 (midnight), whichever comes first based on the start day.
  - If the downtime starts on a Sunday, the downtime after 24 hours will be invalid.
    - Example: If downtime begins at 7:00 AM on Sunday and the duration is 25 hours, the period from Monday 7:00 AM onwards (7:00 AM to 8:00 AM) will be invalid.
  - If the downtime starts from Monday to Saturday, the period from Monday 0:00 AM (midnight) onwards will be invalid.
    - Example: If downtime begins at 7:00 AM on Saturday and the duration is 49 hours, the period from Monday 0:00 AM (midnight) onwards (0:00 AM to 8:00 AM) will be invalid.
  - If your downtime duration exceeds 24 hours, please be careful to avoid having it span from Sunday to Monday.

### Scope refinement

| Configuration item | Description |
| --- | --- |
| Services / Roles | Specify the services / roles to be included or excluded from the downtime. |
| Monitors | Specify the monitors to be included or excluded from the downtime. |

Specifying refinement settings are AND conditions. 

Filter conditions are applied with an "AND" logic. For monitoring types such as Expression Monitoring or External URL Monitoring not linked to a service, please ensure that you do not specify both a service and a role simultaneously.