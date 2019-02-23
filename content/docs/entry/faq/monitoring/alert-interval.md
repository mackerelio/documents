---
Title: FAQ・What is the time interval between alert checks?
Date: 2017-12-15T15:27:31+09:00
URL: https://mackerel.io/docs/entry/faq/monitoring/alert-interval
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812326830705
---

* When the Mackerel management server is not receiving transmissions from the agent for a 10 minute period of time, it will be judged as NG.
* Host metric monitors, and check monitors will be judged as OK/NG when data is sent to the Mackerel server.  
* Service metric monitors will be judged as OK/NG when metrics are sent to the Mackerel server.
* External monitors will be checked and judged as OK/NG every minute.
* There is a possibility that in the future the aforementioned check intervals will be revised.

---

[< Return to FAQ](https://mackerel.io/docs/entry/faq)
