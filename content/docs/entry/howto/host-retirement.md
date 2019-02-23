---
Title: Removing registered hosts from Mackerel’s managed targets
Date: 2017-04-12T15:09:31+09:00
URL: https://mackerel.io/docs/entry/howto/host-retirement
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10328749687236434695
---

By [installing the agent](https://mackerel.io/docs/entry/howto/install-agent) on the server you wish to be monitored or by configuring the [AWS Integration](https://mackerel.io/docs/entry/integrations/aws), that server will be registered as Mackerel's managed host.

In Mackerel, removing a registered host from Mackerel’s managed object is referred to as “retiring”. This document will explain how to retire a host.

In the event that managing a host becomes no longer necessary, please follow the instructions provided below.


<h2 id="retire">How to retire a host</h2>

Open the host details screen of the host you want to remove from Mackerel's managed targets and click the gear icon settings link that is located to the right of the host name.

[f:id:mackerelio:20190222141046p:plain]

This will open the host’s settings screen shown below. Located in the lower right corner of this screen, there is a button labeled “Retire This Host”. Click this to retire the host.

[f:id:mackerelio:20190222141102p:plain]

If the agent is installed to that server, be sure to uninstall it as well. For details on how to uninstall the agent, refer to [https://mackerel.io/docs/entry/howto/install-agent:title].

Additionally, retiring hosts can be done with the API, the command line tool mkr, or the mackerel-agent `retire` subcommand.

[https://mackerel.io/api-docs/entry/hosts#retire:embed:cite]

[https://mackerel.io/docs/entry/advanced/cli:embed:cite]

[https://mackerel.io/docs/entry/spec/agent:embed:cite]


<h2 id="faq">FAQ</h2>

### How do I register/retire hosts in Mackerel in combination with automatic expansion/degradation (auto-scaling)?

Reference the following page.

[https://mackerel.io/docs/entry/howto/auto-scaling:embed:cite]

### When using AWS Integration, how do I remove AWS resources from Mackerel without removing them from AWS?

If instances etc. are left over in AWS and only retired in Mackerel, they will be re-registered as a Mackerel host due to a feature of AWS Integration.

Using the AWS tag, configure the instances so that they are excluded from the integration target. For more information, refer to the following help page.

[https://mackerel.io/docs/entry/integrations/aws#tag:embed:cite]


### How do I register a retired host again in Mackerel?

**Once a host has been retired, original host information can not be restored.** Please be careful and only retire a host when necessary.

If you want to re-register a retired server as a new host in Mackerel, follow the procedure below (**host information prior to retirement cannot be obtained**). 

1. Check to see if there is host ID file named `id` in `/var/lib/mackerel-agent` (This is for default settings. `/var/lib/mackerel-agent-kcps` for the KCPS version agent, inside the install folder for Windows Server). If it exists, delete it.
2. Start the agent (restart if already running). If the agent is not installed, re-install it.
3. The server will be registered in Mackerel as a new host.

[https://mackerel.io/docs/entry/howto/install-agent:embed:cite]
