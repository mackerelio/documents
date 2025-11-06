---
Title: Slack notifications
Date: 2014-11-11T12:43:29+09:00
URL: https://mackerel.io/docs/entry/howto/alerts/slack
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450073190825
---

[Slack](https://slack.com/) is a popular chat service used to speed up real-time communication within a development team or company setting.

Using Slack notifications, you can have alert notifications from your Mackerel organization sent directly to any Slack channel or private group you designate where they will appear in the channel or group's feed.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150409/20150409174311.png" alt="f:id:mackerelio:20150409174311p:plain" title="f:id:mackerelio:20150409174311p:plain" class="hatena-fotolife cboxElement" itemprop="image"></span></p>

When a notification appears in Slack, a link to the alert page and links to the services and roles pertaining to the host that triggered the alert as well as an image of the graph showing the alert will be displayed.

### Adding Slack to your notification channels

To add a new Slack notification channel, click on “Monitors,” then “Channel Settings,” then “New Channel”. Choose Slack from the list of integrations and give the new notification channel a name. (e.g. "slack (#dev-team)")

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190204/20190204181054.png)

Use Incoming Webhooks for notifications to Slack channels.

For the URL, enter the Incoming Webhook URL you can obtain by referring to the following document.

- [Sending messages using incoming webhooks | Slack Developer Docs](https://docs.slack.dev/messaging/sending-messages-using-incoming-webhooks/)

- In the “Mentions” drop down menu, you can decide what types of alerts you would like to be notified of and who can see notifications.
- Click “Create” to complete set-up. To test the new notification channel, go back to the “Channels” page and click the “Test” button next to the Slack channel. If everything was configured correctly, you should receive a notification in Slack instantly.

