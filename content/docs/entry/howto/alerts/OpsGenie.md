---
Title: Opsgenie notifications
Date: 2015-07-13T18:18:12+09:00
URL: https://mackerel.io/docs/entry/howto/alerts/OpsGenie
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450101373362
---

[Opsgenie](https://www.atlassian.com/software/opsgenie) is an alert management service that aggregates and sends notifications from a variety of monitoring tools such as Mackerel in accordance with notification rules configured in Opsgenie. Mobile notifications can be received as voice, SMS, or email.

By using Opsgenie in cooperation with Mackerel, alert notifications from alerts that are raised by monitoring rules that have been set in Mackerel can be sent to Opsgenie. More specifically, when an alert is raised in Mackerel, a new alert will be created by Opsgenie. Additionally, when an alert is resolved in Mackerel, the alert which was created by Opsgenie will be closed.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150709/20150709113705.png)

## Adding Opsgenie Integration
Integration settings can be configured in the [Opsgenie form](https://mackerel.io/my/channels/-/create#opsgenie).

[f:id:mackerelio:20210428161556p:plain]

For integration we are using the [Opsgenie Alert API](https://docs.opsgenie.com/docs/alert-api). The following steps explain how to obtain the API Key.

- Select the Integrations tab from the left side of the Opsgenie main screen.
- Click the API tab on the left side of the Integrations screen.
- The API Key will be displayed as shown below.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150709/20150709114619.png)

In the new channel form enter the desired notification targets in the Recipients section separated by commas as shown here: email address, group name, team name
