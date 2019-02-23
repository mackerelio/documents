---
Title: FAQ・Calculating the number of hosts
Date: 2017-12-15T15:50:08+09:00
URL: https://mackerel.io/docs/entry/faq/contracts/calculate-host-number
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812326837124
---

The number of hosts will be calculated as follows.

* The number of hosts will be calculated using the moving average of the past month.
  * It is the average number of hosts, not the maximum number of concurrent active hosts or the total number of hosts.
  * For example, even if there is a temporary increase in the number of servers due to high-load countermeasures, the usage fee will not increase significantly.
* The number of active hosts will be counted periodically in time intervals of about 1 hour.
  * The number of unique hosts that have accessed the metric posting API will be included [regardless of those hosts’ statuses](https://mackerel.io/docs/entry/howto/alerts#host-statuses).
      * Hosts with metric posts are counted as active hosts even if the host status is `poweroff`.
  * Normally this number will equal the number of mackerel-agents being run.
  * Hosts with a “retired” status / hosts with no metric posts will not be counted as active hosts.
* When using a paid plan, even if the the number of active hosts is zero, the minimum usage fee (one host’s worth) will be charged once.


---

[< Return to FAQ](https://mackerel.io/docs/entry/faq)
