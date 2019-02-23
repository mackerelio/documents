---
Title: OpsGenie notifications
Date: 2015-07-13T18:18:12+09:00
URL: https://mackerel.io/docs/entry/howto/alerts/OpsGenie
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450101373362
---

[OpsGenie](https://www.opsgenie.com/) is an alert management service that aggregates and sends notifications from a variety of monitoring tools such as Mackerel in accordance with notification rules configured in OpsGenie. Mobile notifications can be received as voice, SMS, or email.

By using OpsGenie in cooperation with Mackerel, alert notifications from alerts that are raised by monitoring rules that have been set in Mackerel can be sent to OpsGenie. More specifically, when an alert is raised in Mackerel, a new alert will be created by OpsGenie. Additionally, when an alert is resolved in Mackerel, the alert which was created by OpsGenie will be closed.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150709/20150709113705.png)

## Adding OpsGenie Integration
Integration settings can be configured in the [OpsGenie form](https://mackerel.io/my/channels).

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20160516/20160516155048.png)

For integration we are using the [OpsGenie Alert API](https://www.opsgenie.com/docs/web-api/alert-api). The following steps explain how to obtain the API Key.

- Select the Integrations tab from the left side of the OpsGenie main screen.
- Click the API tab on the left side of the Integrations screen.
- The API Key will be displayed as shown below.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150709/20150709114619.png)

In the new channel form enter the desired notification targets in the Recipients section separated by commas as shown here: email address, group name, team name
