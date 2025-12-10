---
Title: Monitoring Logs
Date: 2015-11-06T16:28:32+09:00
URL: https://mackerel.io/docs/entry/howto/check/log
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6653458415127142958
---

With Mackerel, you can monitor logs such as the following using official check plugins.

- Log files
- Windows Event Log
- Amazon CloudWatch Logs

This page guides you through installing check plugins and setting up simple monitoring configurations based on log types. For detailed settings, please refer to the help documentation for the specific plugins you use.
  
[:contents]

## Getting Started with Log Monitoring

### 1. Install the Official Check Plugin Pack

Refer to [Using the official check plugin pack for check monitoring](https://mackerel.io/docs/entry/howto/mackerel-check-plugins) to install the official check plugin pack.

### 2. Add Check Monitoring Configuration

Add the check monitoring configuration for the logs you want to monitor to the end of the `mackerel-agent.conf` file on the target server.

The format for the settings to be added is as follows:

```toml
[plugin.checks.<monitoring_rule_name>]
command = ["command_to_execute", "argument", "argument", ...]
```

After appending the configuration, restart the mackerel-agent (or use `reload` on Linux) to start monitoring.  
The specified monitor name will appear under Monitors for the target host if the configuration is successfully applied.

<figure class="figure-image figure-image-fotolife" title="The host details screen showing log monitoring configurations named log-sample and aws-cloudwatch-logs-sample displayed under Monitors">[f:id:mackerelio:20251024231326p:plain]<figcaption>Example of log monitoring configurations named "log-sample" or "aws-cloudwatch-logs-sample" displayed in the Monitors section of the host details screen</figcaption></figure>

After configuration, verify functionality by appending an error string to the monitored log to confirm alerts are triggered.

Below are configuration examples for different logs you may want to monitor.

#### Monitoring Log File (check-log)

The following example uses the monitor name `log-sample` to trigger a CRITICAL alert when `ERROR` is output to `/var/log/messages`.

```toml
[plugin.checks.log-sample]
command = ["check-log", "--file", "/var/log/messages", "--pattern", "ERROR"]
```

<figure class="figure-image figure-image-fotolife" title="Example of an alert triggered by the above configuration">[f:id:mackerelio:20251024230354p:plain]<figcaption>Example of an alert triggered by the above configuration</figcaption></figure>

Additionally, depending on your monitoring objectives, you can configure settings such as using the number of logs matching a pattern as an alert condition or monitoring multiple files.
For details, please refer to [Check plugins - check-log - Mackerel Docs](https://mackerel.io/docs/entry/plugins/check-log).

#### Monitoring Windows Event Log (check-windows-eventlog)

Windows Event Log can be monitored using the `check-windows-eventlog` plugin.  

Below is an example using the monitor name `windows-eventlog-sample`. It triggers an alert if an event in the `Application` log has an event level of `Error` and the event message contains the string `ERROR`.

```toml
[plugin.checks.windows-eventlog-sample]
command = ["check-windows-eventlog", "--log", "Application", "--type", "Error", "--message-pattern", "ERROR"]
```

When an alert is triggered with the above configuration, a log like `Event Log CRITICAL: 0 warnings, 1 criticals.` will appear in the Mackerel alert details screen.

Additionally, you can monitor the System log or target specific event IDs and event sources. For details, refer to the [Check plugins - check-windows-eventlog - Mackerel Docs](https://mackerel.io/docs/entry/plugins/check-windows-eventlog).

#### Monitoring Amazon CloudWatch Logs (check-aws-cloudwatch-log)

Amazon CloudWatch Logs can be monitored using the `check-aws-cloudwatch-logs` plugin. Use Amazon CloudWatch Logs filter patterns to define the string conditions you want to detect.

Below is an example with the monitor name `aws-cloudwatch-logs-sample`. It triggers a CRITICAL alert if a string containing `Error` is output to the log group `/aws/lambda/sample_log_group`.

```toml
[plugin.checks.aws-cloudwatch-logs-sample]
command = ["check-aws-cloudwatch-logs", "--log-group-name", "/aws/lambda/sample_log_group", "--pattern", "Error"]
```

When an alert is triggered with the above configuration, a log like `CloudWatch Logs CRITICAL: 1 > 0 messages for pattern /Error/` will appear on the alert details screen.

Additionally, you can narrow down the target log stream using a prefix or set the number of logs matching the pattern as an alert condition.
For details, please refer to [Check plugins - check-aws-cloudwatch-logs - Mackerel Docs](https://mackerel.io/docs/entry/plugins/check-aws-cloudwatch-logs).

Besides, by installing the separate check-aws-cloudwatch-logs-insights plugin, you can enable monitoring using CloudWatch Logs Insights syntax.  
For differences from the check-aws-cloudwatch-logs plugin and installation instructions, see [Check plugins - check-aws-cloudwatch-logs-insights - Mackerel Docs](https://mackerel.io/docs/entry/plugins/check-aws-cloudwatch-logs-insights).

### 3. Modifying Check Monitoring Behavior

You can configure settings such as the check monitoring execution interval and the number of consecutive abnormal detections required to trigger an alert, as needed.
The following example changes the default 1-minute execution interval to 10 minutes and triggers an alert only if all results from three consecutive executions are abnormal.

```toml
[plugin.checks.messages_error]
command = ["check-log", "--file", "/var/log/messages", "--pattern", "ERROR"]
check_interval = 10m
max_check_attempts = 3
```

For other configurable items for check monitoring, refer to [Adding monitors for script checks - Mackerel Docs](https://mackerel.io/docs/entry/custom-checks#items).

## Monitoring Using Experimental Plugins

While not included in the official check plugin pack, Mackerel offers experimental plugins for other log monitoring purposes via [Mackerel Labs](https://github.com/mackerelio-labs).

The `cloudwatch-logs-aggregator` plugin aggregates logs from Amazon CloudWatch Logs and posts them as metrics.

- [Aggregate Amazon CloudWatch Logs and Post Metrics to Mackerel - Mackerel Help](https://mackerel.io/docs/entry/advanced/cloudwatch-logs-aggregator)
- [Practical Implementation of Metricization for Amazon CloudWatch Logs Using cloudwatch-logs-aggregator](https://mackerel.io/blog/entry/cloudwatch-logs-aggregator)

mackerel-sql-metric-collector allows you to post query results from various databases, including MySQL and Athena, as metrics.

- [mackerel-sql-metric-collector - README](https://github.com/mackerelio-labs/mackerel-sql-metric-collector/blob/main/README_ja.md) (in Japanese)
- [Introducing "mackerel-sql-metric-collector": A tool to freely post query results from various databases to Mackerel](https://mackerel.io/ja/blog/entry/meetup14-7) (in Japanese)

check-systemd-journal enables monitoring of journald logs.

- [mackerelio-labs/check-systemd-journal](https://github.com/mackerelio-labs/check-systemd-journal)

Software released by Mackerel Labs is experimental and therefore not covered by our official support. Please understand that we generally cannot respond to inquiries regarding these tools.
