---
Title: Installing mackerel-agent in binary
Date: 2015-02-16T18:29:00+09:00
URL: https://mackerel.io/docs/entry/howto/install-agent/binary
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450083912494
---

<p><a href="#kcps">KCPS edition Mackerel users, click here.</a></p>

<h2 id="download">Download the archive</h2>

Download [mackerel-agent-latest.tar.gz](https://mackerel.io/file/agent/tgz/mackerel-agent-latest.tar.gz).

```
curl -LO https://mackerel.io/file/agent/tgz/mackerel-agent-latest.tar.gz
tar xvzf mackerel-agent-latest.tar.gz
cd mackerel-agent
```

In the archive, `mackerel-agent` is the binary that will execute in Linux.

<h2 id="config">Describe the settings file</h2>

Edit `mackerel-agent.conf` to configure the API key.

```
apikey = "<YOUR_API_KEY>"
```

You can see your API key in your [organization’s page](https://mackerel.io/my). This API key is how your organization is identified, so please be careful not to share this information with anyone outside of your organization.

For more information please refer to [mackerel-agent specifications](https://mackerel.io/docs/entry/spec/agent).

Using the settings file the following things can be done:

- [Configure services and roles](https://mackerel.io/docs/entry/spec/agent#setting-services-and-roles)
- [Post custom metrics](https://mackerel.io/docs/entry/advanced/custom-metrics)

<h2 id="start">Launching the agent</h2>

Designate the settings file you have edited and launch the `mackerel-agent`:

```
./mackerel-agent --conf=./mackerel-agent.conf
```

The agent runs in the foreground so please adjust services to conform with the environment you are using.

<h2 id="uninstall">Uninstall the agent</h2>

To uninstall mackerel-agent, delete the binary file that you downloaded.

Afterwards, the file which recorded the host ID will remain in `/var/lib/mackerel-agent/id` by default, so please delete this file as well.

<h1 id="kcps">Installing the KCPS edition of mackerel-agent in binary</h1>

Deb packages are not supported in KCPS editions of Mackerel.
