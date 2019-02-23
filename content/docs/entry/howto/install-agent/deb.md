---
Title: Installing mackerel-agent in Ubuntu / Debian
Date: 2014-11-11T16:52:36+09:00
URL: https://mackerel.io/docs/entry/howto/install-agent/deb
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450073208234
---

<p><a href="#kcps">KCPS edition Mackerel users, click here.</a></p>

Contents described on this page are also available from the [New host registration screen in Mackerel](https://mackerel.io/my/instruction-agent).

<h2 id="v2">Using Ubuntu 16.04 or later / Debian 8 or later</h2>

Execute the following command:

```
wget -q -O - https://mackerel.io/file/script/setup-all-apt-v2.sh | MACKEREL_APIKEY='<YOUR_API_KEY>' sh
```

You can obtain the API key from the [API key tab on your organization's page](https://mackerel.io/my?tab=apikeys). Keep in mind this key is used to identify your organization, so we strongly advise not sharing it with others.

<h2 id="v1">Using Ubuntu 14.04</h2>

Execute the following command:

```
wget -q -O - https://mackerel.io/file/script/setup-all-apt.sh | MACKEREL_APIKEY='<YOUR_API_KEY>' sh
```

You can obtain the API key from the [API key tab on your organization's page](https://mackerel.io/my?tab=apikeys). Keep in mind this key is used to identify your organization, so we strongly advise not sharing it with others.

<h2 id="dpkg">Using <code>dpkg</code></h2>

Download the deb file:

```
curl -LO https://mackerel.io/file/agent/deb/mackerel-agent_latest.all.deb
```

Use `dpkg` to install:

```
sudo dpkg -i mackerel-agent_latest.all.deb
```

<h2 id="config">Editing configuration files</h2>

Edit the file `/etc/mackerel-agent/mackerel-agent.conf` and set the API key.

```
apikey = "<YOUR_API_KEY>"
```

You can obtain the API key from the [API key tab on your organization's page](https://mackerel.io/my?tab=apikeys). Keep in mind this key is used to identify your organization, so we strongly advise not sharing it with others.

For more information, please visit [mackerel-agent specifications](https://mackerel.io/docs/entry/spec/agent).

Using configuration files the following can be realized:

- [Setting services and roles](https://mackerel.io/docs/entry/spec/agent#setting-services-and-roles)
- [Posting custom metrics](https://mackerel.io/docs/entry/advanced/custom-metrics)
- [Adding monitors for script checks](https://mackerel.io/docs/entry/custom-checks)

<h2 id="start-agent">Launching mackerel-agent</h2>
<h3>Using Ubuntu 16.04 or later / Debian 8 or later</h3>

Executing the following command starts the agent.

```
sudo systemctl start mackerel-agent
```

The log is output to the Journal. You can check the mackerel-agent log with the following command.

```
sudo journalctl -u mackerel-agent.service
```

Once the agent starts functioning properly, the host will be registered with Mackerel, which you can confirm by checking the [Dashboard](https://mackerel.io/my/dashboard).


<h3>When using Ubuntu 14.04</h3>


Executing the following command starts the agent.

```
sudo /etc/init.d/mackerel-agent start
```

You can find the log file here: `/var/log/mackerel-agent.log`.

Once the agent starts functioning properly, the host will be registered with Mackerel, which you can confirm by checking the [Dashboard](https://mackerel.io/my/dashboard).

<h2 id="uninstall">Uninstalling mackerel-agent</h2>

To uninstall Mackerel-agent implement one of the following commands:

If the agent was installed with `apt-get`:

```
sudo apt-get remove mackerel-agent
```

If the agent was installed with `dpkg`:

```
sudo dpkg -r mackerel-agent
```

Then, do not forget to delete the file `/var/lib/mackerel-agent/id` (by default) which contains the host ID.

<h1 id="kcps">Installing the KCPS version of mackerel-agent with a deb package</h1>

Deb packages are not supported in KCPS editions of Mackerel.
