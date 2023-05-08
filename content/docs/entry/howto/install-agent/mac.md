---
Title: Installing mackerel-agent on macOS
Date: 2017-11-29T15:44:59+09:00
URL: https://mackerel.io/docs/entry/howto/install-agent/mac
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812322127868
---

Mackerel offers mackerel-agent’s [Homebrew](https://brew.sh/) Tap for macOS. Monitoring of a macOS machine can be done by following the procedure below.

However, please note that macOS is not officially supported for mackerel-agent. For a list of supported OS, check [here](https://mackerel.io/docs/entry/overview).

<h2 id="install-command">Installing mackerel-agent </h2>

With Homebrew's preinstalled environment, the agent can be installed by executing the following command.

```
brew install mackerelio/mackerel-agent/mackerel-agent
```


<h2 id="config">Edit the configuration file</h2>

The agent configuration file installed with Homebrew is placed in `$(brew --prefix)/etc/mackerel-agent.conf`.
Before starting-up the agent, you need to configure an API key in the configuration file. The API key can be configured with the following command.

```
mackerel-agent init -apikey="<YOUR_API_KEY>" -conf "$(brew --prefix)/etc/mackerel-agent.conf"
```

You can check API keys from the [Organization](https://mackerel.io/my) page. Please be careful to not allow this API key to be leaked to the outside as it is used to identify the organization.

For details, refer to the [mackerel-agent specifications](https://mackerel.io/docs/entry/spec/agent) page.

The following can be achieved using the configuration file:

- [Configure services, roles](https://mackerel.io/docs/entry/spec/agent#setting-services-and-roles)
- [Post custom metrics](https://mackerel.io/docs/entry/advanced/custom-metrics)

<h2 id="start-agent">Startup/stop the agent</h2>

To startup and stop the agent, execute the following commands respectively.

```
brew services start mackerel-agent
```
```
brew services stop mackerel-agent
```

The agent log is output to `$(brew --prefix)/var/log/mackerel-agent.log`

When the agent starts to run correctly, it will be registered as a host in Mackerel. This can be confirmed from the [Dashboard](https://mackerel.io/my/dashboard) etc.
