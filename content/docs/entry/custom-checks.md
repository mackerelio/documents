---
Title: Adding monitors for script checks
Date: 2015-05-15T18:16:34+09:00
URL: https://mackerel.io/docs/entry/custom-checks
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450094528333
---

Check monitoring is a feature to monitor the execution results of a check plugin. The agent will execute the check plugin periodically and send the results to Mackerel. Each check monitor item counts as one host metric. Please refer to [Differences between metric monitoring and check monitoring](https://mackerel.io/docs/entry/custom-checks#difference) for more information on the differences between check monitoring and the monitoring of host metrics and service metrics.

[:contents]

<h2 id="check-plugin">About check plugins</h2>

A program that performs the desired monitoring process is required for check monitoring. We have released an official check plugin pack that can be used for this purpose. Please refer to [Using the official check plugin pack for check monitoring](https://mackerel.io/docs/entry/howto/mackerel-check-plugins) for more information.

Users may also use programs they have created as check plugins. Such programs must perform the desired monitoring process and return an exit status based on the monitoring results. Please refer to [Check plugin specs](https://mackerel.io/docs/entry/custom-checks#specification) for more information.

<h2 id="setting">Sample agent configuration</h2>

Check monitoring settings are added to the [Agent configuration file](https://mackerel.io/docs/entry/spec/agent#config-file). The following is a sample monitor configuration using a selfmade program named "check-ssh.rb". Please refer to [Configuration items](https://mackerel.io/docs/entry/custom-checks#items) for a description of each item. (Set non-required fields as needed.)

```config
[plugin.checks.ssh]
command = ["ruby", "/path/to/check-ssh.rb"]
check_interval = 5
timeout_seconds = 45
max_check_attempts = 3
prevent_alert_auto_close = true
notification_interval = 60
custom_identifier = "SOME_IDENTIFIER"
env = { HOST = "hostname", PORT = "port" }
memo = "This check monitor is ..."
```

<h3 id="items">Configuration items</h3>

| Configuration item | Required | Description | Default value |
|---|---|---|---|
| [plugin.checks.XXX] | ◯ | This is the key used by the configuration file. The "plugin.checks." part is fixed, and the string following the second dot (XXX) will be used as the name of the monitoring rule. The "XXX" part can not contain dots. | None |
| command | ◯ | This is executed by the agent periodically, and its exit status and standard output are used as the monitoring results. Users may also execute arbitrary commands and programs they have created, in addition to the official check plugin. | None |
| check_interval |  | This specifies the interval (in minutes) between successive executions of the check monitor. If using an agent whose version is v0.67.0 or later, you may enter expressions such as "10m" or "1h". Intervals allowed range from 1 minute to 60 minutes. Specified intervals under 1 minute will default to 1 minute, and intervals over 60 minutes will default to 60 minutes. | 1 (minute) |
| timeout_seconds |  | This specifies the timeout period (in seconds) for the processing performed by a plugin. Since the launch of multiple instances of each plugin is not controlled, it is recommended that the value set for this item does not exceed the plugin execution interval (check_interval). | 30 (seconds) |
| max_check_attempts |  |This specifies the maximum number of attempts. An alert will be generated in the event that a result besides OK is obtained for more times than the value specified here in succession. <br>**This defaults to "1" regardless of the value specified when used in conjunction with "prevent_alert_auto_close".** | 1 |
| prevent_alert_auto_close |  | This prevents the alerts generated through monitoring with this check plugin from being closed automatically. <br>**"max_check_attempts" will always default to "1" when this is used in conjunction with "max_check_attempts".** | false |
| notification_interval |  | This specifies the interval (in minutes) between successive resends of alert notifications. If using an agent whose version is v0.67.0 or later, you may enter expressions such as "10m" or "1h". Alert notifications will not be resent if no value is specified. Intervals under 10 minutes are not allowed. Specified intervals under 10 minutes will default to 10 minutes. | Not specified |
| custom_identifier |  | This allows the monitor's execution results to be handled as monitoring results for the host whose identifier is specified here instead of the host on which mackerel-agent is running. If the check result is not OK, an alert will be generated for the host specified here. <br>This is useful for adding check monitoring to hosts where the agent cannot be installed, such as hosts that are integrated via AWS, Azure, or Google Cloud integration. Please refer to [AWS integration documentation](https://mackerel.io/docs/entry/integrations/aws#plugin-custom-identifier) for more information. <br>This value may be found at [Get Host Information](https://mackerel.io/api-docs/entry/hosts#get) for the API. | Not specified |
| env |  | This allows environment variables to be set. It is only valid for plugins that have been configured. | Not specified |
| action |  | The action entered in this item will be executed after each execution of the command set in "command". Please refer to [How to write an action](https://mackerel.io/docs/entry/custom-checks#action-setting) for more information. | Not specified |
| memo |  | This allows a note to be set for the check monitor. You will see the string specified here in alert notifications as well as on the Alert Details screen and the Host Details screen. | Not specified |

<h3 id="action-setting">How to write an action</h3>

You can write an action by entering the following items in the syntax "action = {}". Multiple items can be entered at the same time using a comma-delimited list.

| Setting | Description |
|---|---|
| command | This will be executed immediately after each execution of the command set in "command". This is used to perform any further processing that is required depending on the execution results of the command. [Environment variables](https://mackerel.io/docs/entry/custom-checks#action-env) described below may also be used. (This will be referred to as "action.command" in this section to differentiate it from the "command" of the plugin.) |
| env | This can be used to specify environment variables that are passed to "action.command". The variables are specified as a [Table][] or [Inline Table][] in TOML format. |
| user | This specifies the user by whom "action.command" is executed. This is not supported on Windows. |
| timeout_seconds | This specifies the timeout period (in seconds) for processing "action.command". (Default: 30 seconds) |

The following is a sample configuration for executing "action.command" in the event that the execution result of "command" (MACKEREL_STATUS) is not OK.

```
action = { command = "bash -c '[ \"$MACKEREL_STATUS\" != \"OK\" ]' && ruby /path/to/notify_something.rb", env = { NOTIFY_API_KEY = "API_KEY" }, user = "someone", timeout_seconds = 45 }
```

<h3 id="action-env">Environment variables available with action</h3>

|Environment variable|Description|
|:-----------|:------------|
| MACKEREL_STATUS | This is the execution result for the latest "command". Possible values are "OK", "WARNING", "CRITICAL", and "UNKNOWN". ("max_check_attempts" is not taken into account.) |
| MACKEREL_PREVIOUS_STATUS | This is the execution result for the previous "command". It will be an empty string for the first execution after the agent is launched. Possible values are "OK", "WARNING", "CRITICAL", and "UNKNOWN" from the second execution onwards. ("max_check_attempts" is not taken into account.) |
| MACKEREL_CHECK_MESSAGE | This is the standard output result for the latest "command". It may not be possible to obtain a result on Windows. |

<h2 id="specification">Check plugin specs</h2>

Alert levels are returned as follows based on the exit status of "command".

| Exit status | Alert level |
|-----------|------------|
| 0 | OK |
| 1 | WARNING |
| 2 | CRITICAL |
| Any value besides 0, 1, or 2 | UNKNOWN |

A supplementary message may also be added to the standard output. The length of this message may not exceed 1024 characters. The output will be sent to Mackerel and displayed on the Host Details screen and the Alert Details screen. Therefore, please make sure that no confidential information, such as passwords, is sent.

For more information on how to develop plugins using [github.com/mackerelio/checkers](https://github.com/mackerelio/checkers), the utility library used for the official plugin, please refer to [Creating a check plugin using checkers](https://mackerel.io/docs/entry/advanced/checkers).

<h2 id="notification">Check monitoring notifications</h2>

Notifications are sent when an alert is generated and when the status of an alert changes after it is generated. This includes cases in which the status becomes OK.

The following is an illustration of changes to an alert status and whether a notification will be sent for each status change.

| Check monitor execution attempt | 1st | 2nd | 3rd | 4th | 5th | 6th |
|---|---|---|---|---|---|---|
| Status | CRITICAL | WARNING | WARNING | CRITICAL | CRITICAL | OK |
| Alert notification | Yes | Yes | No | Yes | No | Yes |

In the event of changes to an alert status or the content of a message, this information will also be reflected on the Alert Details screen. No notification will be sent if only the content of a message has changed.

<h2 id="difference">Differences between metric monitoring and check monitoring</h2>

Metric monitoring, such as the monitoring of host metrics and service metrics, differs from check monitoring in the following ways.

- Metric monitoring
  - The host posts metric values to Mackerel, which compares them to the threshold values before generating alert notifications accordingly.
  - Metrics are plotted as graphs.
  - Monitoring rules may be configured via the Web Console or Web API.
  - There is no charge for adding monitoring rules. (Charges apply only to the metrics.)
- Check monitoring
  - The host on which the plugin is executed will determine if the status is OK or NG ("CRITICAL", "WARNING", or "UNKNOWN") and send this result to Mackerel, which will generate an alert notification based on the result received.
  - No metrics are posts, so no graphs are plotted.
  - Monitoring rules are configured within the mackerel-agent configuration file, and no configuration may be added or modified via the Web Console.
  -  Each check monitor item counts as one host metric. Please refer to [Pricing](https://mackerel.io/pricing) for more information on the maximum number of metrics allowed per host. Also, please refer to [Host conversion when plan limit is exceeded](https://support.mackerel.io/hc/en-us/articles/360040109431-%E3%83%97%E3%83%A9%E3%83%B3%E4%B8%8A%E9%99%90%E8%B6%85%E9%81%8E%E6%99%82%E3%81%AE%E3%83%9B%E3%82%B9%E3%83%88%E5%8F%B0%E6%95%B0%E6%8F%9B%E7%AE%97%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6) for more information on the specifications when the limit is exceeded.
- Illustrations of monitoring
  -[Left] Metric monitoring / [Right] Check monitoring
  - ![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200108/20200108154244.png)

<h3 id="example-ruby">An example in Ruby</h3>

This is a plugin that takes the values of a six-sided die as messages, 4 and 5 being a WARNING and 6 being a CRITICAL, and posts them to Mackerel.


```ruby
#!/usr/bin/env ruby
dice = rand(6)+1
puts "value is #{dice}"
exit (dice >= 6 ? 2 : dice >= 4 ? 1 : 0)
```

By executing the agent configured with a check plugin, an item showing that monitoring is active will be displayed in the host details page as shown below.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/S/Songmu/20150514/20150514114502.png)

If an alert is raised it will be displayed as shown below and can be confirmed in the alert details page.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/S/Songmu/20150514/20150514115053.png)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/S/Songmu/20150514/20150514115052.png)
