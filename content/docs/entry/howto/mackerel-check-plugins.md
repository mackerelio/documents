---
Title: Using the official check plugin pack for check monitoring
Date: 2015-11-06T16:21:20+09:00
URL: https://mackerel.io/docs/entry/howto/mackerel-check-plugins
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6653458415127142258
---

Using the official check plugin pack for check monitoring, you can easily monitor processes, logs, and other types of checks in Mackerel.

See below for plugins included in the official check plugin pack.

[https://mackerel.io/docs/entry/plugins/check-plugins-list:embed:cite]

[:contents]

## Installing the official check plugin pack

### Linux OS

We recommend using yum or apt repositories for installation, depending on your environment. The repository is set up when you do [Register a new Host](https://mackerel.io/my/instruction-agent); if you want to get the rpm or deb files directly, please refer to [GitHub Releases](https://github.com/mackerelio/mackerel-agent-plugins/releases).

#### rpm package

```
sudo yum install mackerel-check-plugins
```

Updates can be performed using the following command.

```
sudo yum update mackerel-check-plugins
```

#### deb package

```
sudo apt-get install mackerel-check-plugins
```

Updates can be performed using the following command.。

```
sudo apt-get update
sudo apt-get install mackerel-check-plugins
```

### Windows Server

Official check plugins are bundled with the agent. See [List of Plugins](https://mackerel.io/docs/entry/plugins/check-plugins-list) for a list of included plugins. For information on how to install the agent, see [Registering a New Host](https://mackerel.io/my/instruction-agent).

Updating the agent will also update the included plugins. See [Installing mackerel-agent on Windows](https://mackerel.io/docs/entry/howto/install-agent/msi) for instructions on how to update.

Plugins that are not included are not officially supported and must be built by the user.

## Using the official check plugin pack

### Linux OS

Each plugin will be installed in `/usr/bin`, so you will need to add the item shown below to the mackerel-agent settings file for the plugins you will be using. To apply the settings, you will need to restart the mackerel-agent.

If you wanted to, for example, use the process monitoring plugin to monitor crond, you would add the following item.

```toml
[plugin.checks.check_cron]
command = ["check-procs", "-p", "crond"]
```

### Windows Server

Each plugin is stored in the agent's installation folder and has its own path. add the following settings to mackerel-agent's configuration file according to the plugin you are using. Restarting mackerel-agent is required for the settings to take effect.

To monitor the process "foobar" using the process monitoring plugin, use the following description.

```toml
[plugin.checks.check_foobar]
command = ["check-procs", "-p", "foobar"]
```

## Usage example

- [Monitoring Logs](https://mackerel.io/docs/entry/howto/check/log)
- [Monitoring Processes](https://mackerel.io/docs/entry/howto/check/process)
- [Running checks on TCP servers](https://mackerel.io/docs/entry/howto/check/tcp)

## Source code for the official check plugin pack

The source code for the official check plugin pack is publicly available here: [GitHub](https://github.com/mackerelio/go-check-plugins). We are always happy to receive your pull requests for things like plugins to support new middleware.

For information about making plugins, please refer to [Adding monitors for script checks](https://mackerel.io/docs/entry/custom-checks).
