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

Because Mackerel uses [Incoming Webhooks](https://my.slack.com/services/new/incoming-webhook) to send notifications to Slack, we will need to obtain the Webhook URL. To obtain the Webhook URL, follow the steps below:

- In Slack, open the drop down menu by clicking the down arrow next to your Slack domain's name and select “Configure Integrations.”
  - <p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141003/20141003120426.png" alt="f:id:mackerelio:20141003120426p:plain" title="f:id:mackerelio:20141003120426p:plain" class="hatena-fotolife" itemprop="image"></span></p>
- From the “All Services” tab, click the green “Add” button next to “Incoming Webhooks.”
  - <p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/motemen/20141029/20141029155413.png" alt="f:id:motemen:20141029155413p:plain" title="f:id:motemen:20141029155413p:plain" class="hatena-fotolife" itemprop="image"></span></p>
- Select the channel or private group you’d like to have notifications sent to and click “Add Incoming Webhooks Integration.”
  - <p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/motemen/20141029/20141029155439.png" alt="f:id:motemen:20141029155439p:plain" title="f:id:motemen:20141029155439p:plain" class="hatena-fotolife" itemprop="image"></span></p>
- Copy the Webhook URL which is then displayed and paste the URL back in Mackerel.
  - <p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/motemen/20141029/20141029155456.png" alt="f:id:motemen:20141029155456p:plain" title="f:id:motemen:20141029155456p:plain" class="hatena-fotolife" itemprop="image"></span></p>

- In the “Mentions” drop down menus you can decide what types of alerts you would like to be notified of and who should be able to see notifications.
- Click “Create” to complete set-up. To test the new notification channel, go back to the “Channels” page and click the “Test” button next to the Slack channel. If everything was configured correctly, you should receive a notification in Slack instantly.

If you have any questions please contact our support team at support@mackerel.io
