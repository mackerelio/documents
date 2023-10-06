---
Title: Installing mackerel-agent in Red Hat Enterprise Linux derivatives
Date: 2014-11-10T16:45:49+09:00
URL: https://mackerel.io/docs/entry/howto/install-agent/rpm
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450073117128
---

- <p><a href="./amazon-linux">Click here for Amazon Linux users</a><p>
- <p><a href="#kcps">Click here for KCPS edition Mackerel users</a></p>

Contents described on this page are also available from the [New host registration screen in Mackerel](https://mackerel.io/my/instruction-agent).

<h2 id="v2">When using RHEL 8 (or later) based Linux Distributions</h2>

Execute the following command:

```
curl -fsSL https://mackerel.io/file/script/setup-all-yum-v2.sh | MACKEREL_APIKEY='<YOUR_API_KEY>' sh
```

You can obtain the API key from the [API tab on your organization's page](https://mackerel.io/my?tab=apikeys). Keep in mind this key is used to identify your organization, so we strongly advise not sharing it with others.

Updates can be performed using the `yum` command.

```
sudo yum update mackerel-agent
```

<h2 id="rpm">Using <code>rpm</code></h2>

To install mackerel-agent with `rpm` run the following command:

```
sudo rpm -ivh https://mackerel.io/file/agent/rpm/mackerel-agent-latest.noarch.rpm
```

To update Mackerel-agent with `rpm` run the following command:

```
sudo rpm -Uvh https://mackerel.io/file/agent/rpm/mackerel-agent-latest.noarch.rpm
```

<h2 id="config">Editing configuration files</h2>

Edit the file `/etc/mackerel-agent/mackerel-agent.conf` and set the API key.

```
apikey = "<YOUR_API_KEY>"
```

You can obtain the API key from the [API tab on your organization's page](https://mackerel.io/my?tab=apikeys). Keep in mind this key is used to identify your organization, so we strongly advise not sharing it with others.

For more information, please refer to [mackerel-agent specifications](https://mackerel.io/docs/entry/spec/agent).

Using configuration files the following can be done:

- [Setting services and roles](https://mackerel.io/docs/entry/spec/agent#setting-services-and-roles)
- [Posting custom metrics](https://mackerel.io/docs/entry/advanced/custom-metrics)
- [Adding monitors for script checks](https://mackerel.io/docs/entry/custom-checks)

<h2 id="start-agent">Launching mackerel-agent</h2>
<h3>When using RHEL 8 (or later) based Linux Distributions</h3>

To launch Mackerel-agent run the following command:

```
sudo systemctl start mackerel-agent
```

You can find the log file here: `/var/log/mackerel-agent.log`.

```
sudo journalctl -u mackerel-agent.service
```

Once the agent starts functioning properly, the host will be registered with Mackerel, which you can confirm by checking the [Dashboard](https://mackerel.io/my/dashboard).

<h2 id="uninstall">Uninstalling mackerel-agent</h2>

To uninstall Mackerel-agent run one of the following commands:

If the agent was installed with `yum`:

```
sudo yum erase mackerel-agent
```

If the agent was installed with `rpm`:

```
sudo rpm -e mackerel-agent
```

Lastly, please do not forget to delete the file `/var/lib/mackerel-agent/id` which by default contains the host ID.


<h1 id="kcps">Installing the KCPS edition of mackerel-agent as an RPM package</h1>


<h2 id="yum">Using yum</h2>

First things first, we need to register the Mackerel yum repository:

```
curl -fsSL http://198.18.0.16/file/setup-yum-kcps.sh | sh
```

Once that’s done, we can use `yum` to install:

```
sudo yum install mackerel-agent-kcps
```

You can also use `yum` to update Mackerel-agent:

```
sudo yum update mackerel-agent-kcps
```

<h2 id="rpm">Using rpm</h2>

To install mackerel-agent with `rpm` run the following command:

```
sudo rpm -ivh http://198.18.0.16/file/rpm/mackerel-agent-kcps-latest.noarch.rpm
```

To update Mackerel-agent with rpm run the following command:

```
sudo rpm -Uvh http://198.18.0.16/file/rpm/mackerel-agent-kcps-latest.noarch.rpm
```

<h2 id="config">Editing configuration files</h2>

Edit the file `/etc/mackerel-agent-kcps/mackerel-agent-kcps.conf` and set the API key.

```
apikey = "<YOUR_API_KEY>"
```

You can obtain the API key from your [organization's page](https://kcps-mackerel.io/my). Keep in mind this key is used to identify your organization, so we strongly advise not sharing it with others.

For more information, please refer to [mackerel-agent specifications](https://mackerel.io/docs/entry/spec/agent).

Using configuration files the following can be done:

- [Setting services and roles](https://mackerel.io/docs/entry/spec/agent#setting-services-and-roles)
- [Posting custom metrics](https://mackerel.io/docs/entry/advanced/custom-metrics)
- [Adding monitors for script checks](https://mackerel.io/docs/entry/custom-checks)

<h2 id="start-agent">Launching mackerel-agent</h2>

To launch Mackerel-agent run the following command:

```
sudo /etc/init.d/mackerel-agent-kcps start
```

You can find the log file here: `/var/log/mackerel-agent-kcps.log`

Once the agent starts functioning properly, the host will be registered with Mackerel, which you can confirm by checking the [Dashboard](https://kcps-mackerel.io/my/dashboard).

<h2 id="uninstall">Uninstalling mackerel-agent</h2>

To uninstall Mackerel-agent run one of the following commands:

If the agent was installed with `yum`:

```
sudo yum erase mackerel-agent-kcps
```

If the agent was installed with `rpm`:

```
sudo rpm -e mackerel-agent-kcps
```

Lastly, please do not forget to delete the file `/var/lib/mackerel-agent-kcps/id` which by default contains the host ID.

-

If you have any questions please contact our support team at support@mackerel.io
