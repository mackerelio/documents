---
Title: Installing mackerel-agent in Amazon Linux
Date: 2016-05-23T15:21:56+09:00
URL: https://mackerel.io/docs/entry/howto/install-agent/amazon-linux
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6653812171397671378
---

The content described on this page is also available from the [New Host registration screen in Mackerel](https://mackerel.io/my/instruction-agent).

<h2>Install the package</h2>
Execute the following command:

```
curl -fsSL https://mackerel.io/file/script/amznlinux/setup-all-yum-v2.sh | MACKEREL_APIKEY='<YOUR_API_KEY>' sh
```

You can check the API key from the [Organization page’s API Keys tab](https://mackerel.io/my?tab=apikeys). Keep in mind that this key is used to identify your organization, so we strongly advise not sharing it with others.

Updates can be performed using the `yum` command.

```
sudo yum update mackerel-agent
```

Installation can also be done directly using the `rpm` command.

<h3 id="rpm-v2">Using the rpm command</h2>

To install mackerel-agent with `rpm` run the following command:

```
sudo rpm -ivh https://mackerel.io/file/agent/rpm/mackerel-agent-latest.amzn2.x86_64.rpm
```

To update Mackerel-agent with `rpm` run the following command:

```
sudo rpm -Uvh https://mackerel.io/file/agent/rpm/mackerel-agent-latest.amzn2.x86_64.rpm
```

<h2 id="config">Edit the configuration file</h2>

Edit the `/etc/mackerel-agent/mackerel-agent.conf` file to configure the agent.

By using the configuration file, the following can be implemented:

- [Service and role configuration](https://mackerel.io/docs/entry/spec/agent#setting-services-and-roles)
- [Posting user-defined custom metrics](https://mackerel.io/docs/entry/advanced/custom-metrics)
- [Adding monitors for script checks](https://mackerel.io/docs/entry/custom-checks)

For more details, check out the [mackerel-agent specifications](https://mackerel.io/docs/entry/spec/agent) help page.

Please be sure to configure the API key if installed from rpm. This is done automatically if set up with the setup script.

```
apikey = "<YOUR_API_KEY>"
```

You can check the API key from the [Organization page’s API Keys tab](https://mackerel.io/my?tab=apikeys). Keep in mind that this key is used to identify your organization, so we strongly advise not sharing it with others.

<h2 id="start-agent">Starting the agent</h2>
The agent will start by running the following command:

```
sudo systemctl start mackerel-agent
```

The log is output to Journal. You can check the mackerel-agent log with the following command.

```
sudo journalctl -u mackerel-agent.service
```

When the agent begins to run properly, it will be registered as a host in Mackerel. Please confirm this in [dashboards](https://mackerel.io/my/dashboard) etc.

<h2 id="uninstall">Uninstall the agent</h2>

To uninstall mackerel-agent, run the following command.


```
sudo yum erase mackerel-agent
```

By doing so, a file with the host ID documented will remain (at `/var/lib/mackerel-agent/id` under default settings), so please make sure to delete it.
