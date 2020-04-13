---
Title: Using the official check plugin pack for check monitoring
Date: 2015-11-06T16:21:20+09:00
URL: https://mackerel.io/docs/entry/howto/mackerel-check-plugins
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6653458415127142258
---

Using the official plugin pack for check monitoring, you can easily monitor processes, logs, and other types of checks in Mackerel.

[:contents]

## Installing the official check plugin pack(for Linux OS)

To install, we recommend using either the yum repository or the apt repository in accordance with the environment you are using. For how to configure the repository, please refer to the [mackerel-agent installation page][]. If you would like to directly obtain the rpm and deb file, please refer to [GitHub Releases][].

[mackerel-agent installation page]: https://mackerel.io/my/instruction-agent
[GitHub Releases]: https://github.com/mackerelio/go-check-plugins/releases


### If installing with the rpm package

```
yum install mackerel-check-plugins
```

### If installing with the deb package

```
apt-get install mackerel-check-plugins
```

## Installing the official plugin pack(for Windows Server)

When using the official plugin pack on a Windows Server, we recommend using a plugin that comes bundled with the agent. (For details on how to install the agent, refer to [Installing Mackerel Agent][].)

For a list of the bundled plugins, refer to the [GitHub Repository Bundled Plugin List][]. Plugins that are not included in this list are not officially supported and must be built independently.

[Installing Mackerel Agent]: https://mackerel.io/my/instruction-agent
[GitHub Repository Bundled Plugin List]: https://github.com/mackerelio/mackerel-agent/blob/master/wix/pluginlist.txt

## Using the official plugin pack(for Linux OS)

Each plugin will be installed in `/usr/bin`, so you will need to add the item shown below to the mackerel-agent settings file for the plugins you will be using. To apply the settings, you will need to restart the mackerel-agent.

If you wanted to, for example, use the process monitoring plugin to monitor crond, you would add the following item.

```config
[plugin.checks.check_cron]
command = ["check-procs", "-p", "crond"]
```

## Using the official plugin pack(for Windows Server)

As mentioned above, when using the official plugin pack on a Windows Server, we recommend using a plugin that comes bundled with the agent. When using a bundled plugin, each is stored in the agent's installation folder and run through pass. Add the following configuration according to the plugin of use in the mackerel-agent configuration file. Then restart mackerel-agent to apply the configuration.

To monitor the process "foobar" using the process monitoring plugin, use the following description.

```config
[plugin.checks.check_foobar]
command = ["check-procs", "-p", "foobar"]
```

## Usage example

- [Monitoring Logs](https://mackerel.io/docs/entry/howto/check/log)
- [Monitoring Processes](https://mackerel.io/docs/entry/howto/check/process)
- [Running checks on TCP servers](https://mackerel.io/docs/entry/howto/check/tcp)

## The plugin pack source code

The source code for the official plugin pack is publicly available here: [https://github.com/mackerelio/go-check-plugins:title]. We are always happy to receive your pull requests for things like plugins to support new middleware.

For information about making plugins, please refer to [Adding monitors for script checks](https://mackerel.io/docs/entry/custom-checks).
