---
Title: FAQ・Graphs aren't being displayed in Mackerel and it seems like data isn't
  being posted.
Date: 2017-12-15T15:41:26+09:00
URL: https://mackerel.io/docs/entry/faq/troubleshoot/cannot-view-graph
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812326834499
---

* First check and see if the mackerel-agent is running or not.
* If the agent is running, check to see if the server's time is configured correctly.
    * If there is a discrepancy between the configured time and the actual current time, there is possibility that data will not be posted correctly.
* There is a short time lag between registering a new host and the first metrics post from that host.

---

[< Return to FAQ](https://mackerel.io/docs/entry/faq)
