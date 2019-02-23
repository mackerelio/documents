---
Title: FAQ・How do the Mackerel server and the mackerel-agent communicate?
Date: 2017-12-15T15:28:14+09:00
URL: https://mackerel.io/docs/entry/faq/spec/connection-with-agent
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812326830924
---

* mackerel -agent communicates with the Mackerel server via its HTTP REST API.
  * No direct communication is done from the Mackerel server side to the host that contains mackerel-agent.
* Encrypted with SSL/TLS correspondence.

---

[< Return to FAQ](https://mackerel.io/docs/entry/faq)
