---
Title: FAQ・How do I have a re-created instance be recognized as the same host as before?
Date: 2017-12-15T15:22:26+09:00
URL: https://mackerel.io/docs/entry/faq/spec/host-inheriting
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812326829060
---

## For Amazon EC2 or Azure Virtual Machines
Instances that differ from the those that were previously monitored can not be recognized (carry over information) as the same host in Mackerel.
Please [retire](https://mackerel.io/docs/entry/howto/host-retirement) the exiting host and start monitoring as a new host. 


## For other virtual hosts / VMs
When you recreate an instance and still want it be recognized as the same host as before, first backup the file containing the host ID (saved by default in `/var/lib/mackerel-agent/id` for Linux OS and in the `id` file inside the install folder for Windows Server; for more info see the [Glossary](https://mackerel.io/docs/entry/glossary#host)) and insert that file in the new instance. After starting the agent, it will now be recognized as the same host by Mackerel.

However, please be aware that if the agent is running on multiple hosts with the same host ID file, those hosts will be recognized as the same host and monitoring/metrics posting will not be able to function properly.

---

[< Return to FAQ](https://mackerel.io/docs/entry/faq)
