---
Title: PagerDuty notifications
Date: 2014-12-15T12:11:12+09:00
URL: https://mackerel.io/docs/entry/howto/alerts/pagerduty
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450077119625
---

[PagerDuty](http://www.pagerduty.com/) is an incident management service that aggregates alerts from monitoring tools like Mackerel and can send various notifications according to arbitrary monitors registered in PagerDuty. In the event of an incident, notifications can be received through email, telephone, SMS, or a push notification.

By integrating with PagerDuty, you can manage alerts that occur in Mackerel as PagerDuty incidents. 


<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20201109/20201109113940.png" alt="f:id:mackerelio:20201109113940p:plain" title="f:id:mackerelio:20201109113940p:plain" class="hatena-fotolife" itemprop="image"></span></p>

### Integrating PagerDuty

Integration with PagerDuty can be configured by accessing the [PagerDuty form in the Channels page](https://mackerel.io/my/channels/-/create#pagerduty).

<p><span itemscope=""http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20171219/20171219173818.png" alt="f:id:andyyk:20171219173818p:plain" title="f:id:andyyk:20171219173818p:plain" class="hatena-fotolife" itemprop="image"></span></p>

### Integration Key

Integration with PagerDuty is done through the [Events API](https://v2.developer.pagerduty.com/docs/events-api).
Input the Integration Key issued when creating a "Service" in PagerDuty and input it in Mackerel's Channel Settings screen.

- Select `Service` from the `Configuration` tab in the top of the PagerDuty screen.
- Click on `Add New Service` in the top right to create a new service to integrate with Mackerel
- For `Integration Type`, select `Use our API directly`
    - In this case, specify `Events API v2` as the API type
- After completion, the Integration Key will be displayed

### Notification Level

It's also possible to choose which alert notifications you want to be sent to PagerDuty based on status. The options are `Warning & Critical` or `Critical only`.

- `Warning & Critical`
  - If either a Warning or Critical alert occurs, an incident notification will be sent to PagerDuty.
  - The PagerDuty incident will resolve automatically when the status returns to normal.
  - If the status changes from Warning to Critical, a notification will be sent to PagerDuty, however a new incident will not be created. Rather, this change will appear as an update on the previous Warning incident.


- `Critical only`
  - An incident notification will be sent to PagerDuty only for a Critical alert, not for a Warning alert.
  - The PagerDuty incident will resolve automatically when the status returns to normal.
  - When the status transitions to Warning, PagerDuty will do nothing.

### Alert resolution

By default, when an alert is closed in Mackerel, the incident will also close in PagerDuty. However, if you turn off the option to "Close incident when alert closes", the incident in PagerDuty will not close even if the alert has been closed.

### Notification content unique to Events API v2

The following items are included in the notification content only when using API v2.

- Related graphs
- Monitor Memo
- custom_identifier

<h2 id="api-version">About the API Version</h2>

In the PagerDuty channel settings screen, you can select which version of the Events API to use, but we recommend using API v2 for new configurations. As of November 2017 there are no significant differences between the contents, but there is a possibility that notification parameters that can only be used with Events API v2 will be added in the future.

