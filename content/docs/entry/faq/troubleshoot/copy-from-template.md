---
Title: FAQ・Mackerel doesn't recognize duplicate servers from agent installed templates
  ／ machine images etc.
Date: 2018-04-19T17:54:45+09:00
URL: https://mackerel.io/docs/entry/faq/troubleshoot/copy-from-template
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/17391345971636492083
---

In Mackerel, each host is identified by a unique host ID, and that host ID is saved in the host file on which mackerel-agent is running (`/Var/lib / mackerel-agent / id` for Linux OS hosts, the installation folder `id` file for Windows Servers. Refer to the host section of the glossary for more details).

When mackerel-agent launches on another host containing a file with the same host ID recorded, it is recognized as the same host by Mackerel even if multiple hosts exist. If this is the case, monitoring and metric posting will not function properly. Also, refer here. [FAQ・How do I have a re-created instance be recognized as the same host as before?](https://mackerel.io/docs/entry/faq/spec/host-inheriting)

Therefore, when creating a template / machine image installed with the agent, make sure that the host ID file has been deleted before doing so. 

For a host that has already been launched containing an ID file that overlaps with another host, it is possible to append it as a separate host by following the procedure below.

1. Delete the host ID file in the target host

2. Reboot the agent of target host

---

[< Return to FAQ](https://mackerel.io/docs/entry/faq)
