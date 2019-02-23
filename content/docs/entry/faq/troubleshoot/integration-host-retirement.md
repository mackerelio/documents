---
Title: FAQ・I’m using integration, but the host remains without retiring
Date: 2018-04-24T17:03:33+09:00
URL: https://mackerel.io/docs/entry/faq/troubleshoot/integration-host-retirement
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/17391345971638043762
---

Using [AWS Integration](https://mackerel.io/docs/entry/integrations/aws) or [Azure Integration](https://mackerel.io/docs/entry/integrations/azure), you can monitor with Mackerel without having to install the agent software (mackerel-agent).

However, Mackerel's integration feature can be used for “host registration (integration)”, but integration rescission ([host retirement](https://mackerel.io/docs/entry/howto/host-retirement)) is not done automatically. Even if the virtual server is deleted, host information already integrated with Mackerel will remain registered. Additionally, host information will remain registered even if integration configurations have been deleted.

If the host information isn’t necessary, [remove the registered host from Mackerel’s managed objects](https://mackerel.io/docs/entry/howto/host-retirement).

---

[< Return to FAQ](https://mackerel.io/docs/entry/faq)
