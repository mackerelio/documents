---
Title: Using the official plugin pack to visualize middleware metrics
Date: 2015-02-16T18:16:25+09:00
URL: https://mackerel.io/docs/entry/howto/mackerel-agent-plugins
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450083908463
---

By using the official plugin, which supports many different kinds of middleware, it’s easy to gather middleware metrics. Supported environments will be the same as `mackerel-agent`, so for more information about that please refer to our [outline of supported environments](https://mackerel.io/docs/entry/overview#support-environments).

In order to monitor metrics for AWS services like ELB or RDS, consider using [AWS Integration](https://mackerel.io/docs/entry/integrations/aws). As for the posting of service metrics, please use [fluentd](http://www.fluentd.org/). For more information please refer to [Posting metrics related to ELB from AWS CloudWatch to service metrics.](https://mackerel.io/docs/entry/advanced/fluentd#example-elb)

See below for plugins included in the official plugin pack.

[https://mackerel.io/docs/entry/plugins/metric-plugins-list:embed:cite]

[:contents]

## Installing the official plugin pack

### Linux OS

We recommend using yum or apt repositories for installation, depending on your environment. The repository is set up when you do [Register a new Host](https://mackerel.io/my/instruction-agent); if you want to get the rpm or deb files directly, please refer to [GitHub Releases](https://github.com/mackerelio/mackerel-agent-plugins/releases).


#### rpm package

```
sudo yum install mackerel-agent-plugins
```

Updates can be performed using the following command.

```
sudo yum update mackerel-agent-plugins
```

#### deb package

```
sudo apt-get install mackerel-agent-plugins
```

Updates can be performed using the following command.

```
sudo apt-get update
sudo apt-get install mackerel-agent-plugins
```

### Windows Server

Official plugins are bundled with the agent. See [List of Plugins](https://mackerel.io/docs/entry/plugins/metric-plugins-list) for a list of included plugins. For information on how to install the agent, see [Registering a New Host](https://mackerel.io/my/instruction-agent).

Updating the agent will also update the included plugins. See [Installing mackerel-agent on Windows](https://mackerel.io/docs/entry/howto/install-agent/msi) for instructions on how to update.

Plugins that are not included are not officially supported and must be built by the user.

## Using the official plugin pack

### Linux OS

Each plugin has been installed in `/usr/bin` ,so please configure the mackerel-agent settings file to meet with the plugin which you will be using as shown below.

if using plugin for Apache2
```
[plugin.metrics.apache2]
command = ["mackerel-plugin-apache2"]
```

For more on how to use each plugin, refer below to the README of each plugin in GitHub.

### Windows Server

Each plugin is stored in the agent's installation folder and has its own path. add the following settings to mackerel-agent's configuration file according to the plugin you are using. Restarting mackerel-agent is required for the settings to take effect.

When using the "mackerel-plugin-mssql" plugin, the description is as follows.

```
[plugin.metrics.mssql]
command = ["mackerel-plugin-mssql"]
```

For more on how to use each plugin, refer below to the README of each plugin in GitHub.

## Source code for the official plugin pack

The official plugin pack is open to the public and can be found here [GitHub](https://github.com/mackerelio/mackerel-agent-plugins).
We are always happy to receive Pull Requests, comments, etc. regarding plugins that support new middleware.

For creating plugins please refer to [Posting custom metrics](https://mackerel.io/docs/entry/advanced/custom-metrics).
