---
Title: Monitoring TCP servers
Date: 2015-12-02T14:56:04+09:00
URL: https://mackerel.io/docs/entry/howto/check/tcp
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6653586347147184061
---

With Mackerel's official check plugins, you can monitor TCP servers.

This page covers everything from installing the check plugins to setting up basic monitoring. For detailed configuration options, please refer to the help page for the plugin you are using.

[:contents]

## Getting started with TCP server monitoring

### 1. Install the official check plugin pack

Refer to [Using the official check plugin pack for check monitoring](https://mackerel.io/docs/entry/howto/mackerel-check-plugins) to install the official check plugin pack.

### 2. Add check monitoring settings

Add check monitoring settings for the TCP server you want to monitor to the end of `mackerel-agent.conf` on the target server.
The configuration format is as follows:

```toml
[plugin.checks.<monitoring rule name>]
command = ["command to execute", "argument", "argument"...]
```

After adding the settings, restart mackerel-agent (on Linux, a reload is also acceptable) to start monitoring.
If the settings are applied correctly, the monitoring rule name specified in the settings will appear under "Monitors" on the target host.

Below are configuration examples for check-tcp. For available options, refer to the [check-tcp help](https://mackerel.io/docs/entry/plugins/check-tcp).

#### Using with a specified service

For several services, specifying the `--service` option allows you to run standard checks without complex configurations. For example, FTP would be configured as shown below.

```toml
[plugin.checks.ftp]
command = ["check-tcp", "--service", "ftp", "-H", "localhost"]
```

This is equivalent to the configuration below.

```toml
[plugin.checks.ftp]
command = ["check-tcp", "-H", "localhost", "--port", "21", "--expect-pattern", "^200", "--quit", "QUIT"]
```

For a list of service names that can be specified with the `--service` option, refer to the [list of supported services](https://mackerel.io/docs/entry/plugins/check-tcp#services).

#### Checking HTTP servers

Below is a configuration for an HTTP application server that sends the request `GET / HTTP/1.0\r\n\r\n` and checks whether the response contains `OK Farm`. By specifying the `--escape` option, characters such as `"\r"` and `"\n"` contained in the `--send` argument will be treated as line breaks.

You can also set thresholds for the connection time (in seconds). In the example below, a Warning alert will be triggered if the connection takes 3 seconds or more, and a Critical alert if it takes 10 seconds or more.

```toml
[plugin.checks.tcp_app]
command = ["check-tcp", "--hostname", "localhost", "--port", "5000", "--send", "GET / HTTP/1.0\r\n\r\n", "--escape", "--expect-pattern", "OK Farm", "--warning", "3", "--critical", "10"]
```

### 3. Change check monitoring behavior

If needed, you can configure settings such as the check monitoring execution interval and how many consecutive anomalies must be detected before an alert is triggered. The following example changes the default 1-minute check interval to 10 minutes and triggers an alert only when all 3 consecutive check results are abnormal.

```toml
[plugin.checks.ftp]
command = ["check-tcp", "--service", "ftp", "-H", "localhost"]
check_interval = 10m
max_check_attempts = 3
```

For other configurable check monitoring settings, refer to [Adding check monitoring items - Mackerel Help](https://mackerel.io/docs/entry/custom-checks#items).
