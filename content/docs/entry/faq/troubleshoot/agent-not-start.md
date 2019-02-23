---
Title: FAQ・The agent won’t startup.
Date: 2017-12-15T15:40:23+09:00
URL: https://mackerel.io/docs/entry/faq/troubleshoot/agent-not-start
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812326834124
---

* It's a good idea to first check and see if the package has been properly installed or not.
* Please check whether OS to be installed is officially supported.
    * [Supported Environments - Mackerel Docs](https://mackerel.io/docs/entry/overview#support-environments)
* If antivirus software is installed on the server where the agent is installed, please make sure that mackerel-agent signal is not blocked.
* Check the contents of the log by mackerel-agent and check whether an error is output.
* Please check the command `mackerel-agent configtest` to see if the configuration file `mackerel-agent.conf` is written correctly.
* If you have included `/var/lib/mackerel-agent/id` into an OS image such as Docker Image or AMI, it will be necessary to delete this file.
  * When the mackerel-agent starts up for the first time, the ID issued by the server will be saved to `/var/lib/mackerel-agent/id`. The host will be identified by this ID from then on. For that reason, if `/var/lib/mackerel-agent/id` is copied, multiple hosts will be recognized by Mackerel as the same host, resulting in metric posting and monitoring not functioning properly.

---

[< Return to FAQ](https://mackerel.io/docs/entry/faq)
