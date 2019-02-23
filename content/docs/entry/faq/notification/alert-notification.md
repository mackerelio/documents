---
Title: FAQ・How many times will notifications be sent until an alert is resolved?
Date: 2017-12-15T15:26:04+09:00
URL: https://mackerel.io/docs/entry/faq/notification/alert-notification
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812326830231
---

* Notifications will be sent only once each time an alert's status changes.
* For example, if an alert's status changes from OK→Warning→Critical→OK, a total of 3 notifications will be sent, once each time the status changed.

However, in the case of alerts of external monitors, in addition to the above specifications, a notification will also be sent if there are fluctuations in the monitoring result message.

---

[< Return to FAQ](https://mackerel.io/docs/entry/faq)
