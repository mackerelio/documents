---
Title: Adding monitors for script checks
Date: 2015-05-15T18:16:34+09:00
URL: https://mackerel.io/docs/entry/custom-checks
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450094528333
---

Check monitoring is a feature to monitor the execution results of a check plugin. The agent will execute the check plugin periodically and send the results to Mackerel. Each check monitor item counts as one host metric. Please refer to [Differences between metric monitoring and check monitoring](#difference) for more information on the differences between check monitoring and the monitoring of host metrics and service metrics.

[:contents]

<h2 id="check-plugin">About check plugins</h2>

A program that performs the desired monitoring process is required for check monitoring. We have released an official check plugin pack that can be used for this purpose. Please refer to [Using the official check plugin pack for check monitoring](https://mackerel.io/docs/entry/howto/mackerel-check-plugins) for more information.

Users may also use programs they have created as check plugins. Such programs must perform the desired monitoring process and return an exit status based on the monitoring results. Please refer to [Check plugin specs](#specification) for more information.

<h2 id="setting">Sample agent configuration</h2>

Check monitoring settings are added to the [Agent configuration file](https://mackerel.io/docs/entry/spec/agent#config-file). The following is a sample monitor configuration using a selfmade program named `check-ssh.rb`. Please refer to [Configuration items](#items) for a description of each item. Please set the non-required fields as needed.

```config
[plugin.checks.ssh]
command = ["ruby", "/path/to/check-ssh.rb"]
user = "SOME_USER_NAME"
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

As shown in the sample configuration for the agent, it should be followed by the key `[plugin.checks.XXX]` of the monitoring rule. Each configuration item must be configured for each monitoring rule.

| Configuration item | Required | Description | Default value |
|---|---|---|---|
| [plugin.checks.XXX] | ✓ | Define the key values of the monitoring rules in the configuration file in three levels separated by dots . The second level, `plugin.checks.`, is fixed, and the third level, `XXX`, is used as the name of the monitoring rule. Dots are not allowed in `XXX`. The third level, `XXX`, will be used as the monitoring rule name. |  |
| command | ✓ | This is executed by the agent periodically, and its exit status and standard output are used as the monitoring results. Users may also execute arbitrary commands and programs they have created, in addition to the official check plugin. |  |
| user |  | The user to run command can be specified with `command`. If a user is not specified, the executing user of mackerel-agent will also be the executing user of command. Not yet supported for Windows environments. |  |
| check_interval |  | Specifies the interval in minutes between check monitoring runs. If using an agent whose version is v0.67.0 or later, you may enter expressions such as `10m` or `1h`. Intervals allowed range from 1 minute to 60 minutes. Specified intervals under 1 minute will default to 1 minute, and intervals over 60 minutes will default to 60 minutes. | 1 minute |
| timeout_seconds |  | The timeout period in seconds for the processing of the program specified by `command`. Set the value so that it does not exceed `check_interval`. | 30 seconds |
| max_check_attempts |  | This specifies the maximum number of attempts. An alert will be generated in the event that a result besides OK is obtained for more times than the value specified here in succession. <br>**This defaults to `1` regardless of the value specified when used in conjunction with `prevent_alert_auto_close`.** | 1 |
| prevent_alert_auto_close |  | Normally, if the result of monitoring after an alert occurs is OK, the alert is automatically closed, but if this is true, it remains opend. <br>**`max_check_attempts` will always default to `1` when this is used in conjunction with `max_check_attempts`.** | false |
| notification_interval |  | Specifies the alert notification retransmission interval in minutes. If the agent version is v0.67.0 or later, it can be expressed as `10m` or `1h`. If less than 10 minutes is specified, it is treated as 10 minutes. If not set, the notification will not be resent. | null |
| custom_identifier |  | This monitoring rule is treated as monitoring the host specified in `custom_identifier`, not the host running mackerel-agent. If the result of the monitoring is not OK, an alert will be issued for the host specified here. The `custom_identifier` can be found in the API [Get Host Information](https://mackerel.io/api-docs/entry/hosts#get). <br>This is useful when performing check monitoring on hosts where agents cannot be installed, such as hosts linked with AWS / Azure / Google Cloud integration. See [AWS Integration Documentation](https://mackerel.io/docs/entry/integrations/aws#plugin-custom-identifier) for more information. | null |
| env |  | You can set environment variables. This is only valid for the `command` of the monitoring rule you set. | null |
| action |  | The action entered in this item will be executed after each execution of the command set in `command`. Please refer to [How to write an action](#action-setting) for more information. | null |
| memo |  | This allows a note to be set for the check monitor. You will see the string specified here in alert notifications as well as on the Alert Details screen and the Host Details screen. Up to 250 characters. | null |

<h3 id="action-setting">How to write an action</h3>

You can write an action by entering the following items in the syntax "action = {}". Multiple items can be entered at the same time using a comma-delimited list.

| Setting | Description | Default value |
| --- | --- | --- |
| command | This will be executed immediately after each execution of the command set in `command`. This is used to perform any further processing that is required depending on the execution results of the command. [Environment variables](#action-env) described below may also be used. To distinguish it from the `command` in the monitoring rules, it is treated as `action.command` in this page. |  |
| env | This can be used to specify environment variables that are passed to `action.command`. The variables are specified as a [Table][] or [Inline Table][] in TOML format. | null |
| user | This specifies the user by whom `action.command` is executed. This is not supported on Windows. | root |
| timeout_seconds | The timeout period in seconds for processing `action.command`. | 30 seconds |

Example of action

```
action = { command = "bash -c '[ \"$MACKEREL_STATUS\" != \"OK\" ]' && ruby /path/to/notify_something.rb", env = { NOTIFY_API_KEY = "API_KEY" }, user = "someone", timeout_seconds = 45 }
```

<h3 id="action-env">Environment variables available with action</h3>

|Environment variable|Description|
|:-----------|:------------|
| MACKEREL_STATUS | This is the execution result for the latest `command`. Possible values are `OK`, `WARNING`, `CRITICAL`, and `UNKNOWN`. `max_check_attempts` is not taken into account. |
| MACKEREL_PREVIOUS_STATUS | This is the execution result for the previous `command`. It will be an empty string for the first execution after the agent is launched. Possible values are `OK`, `WARNING`, `CRITICAL`, and `UNKNOWN` from the second execution onwards. `max_check_attempts` is not taken into account. |
| MACKEREL_CHECK_MESSAGE | This is the standard output result for the latest `command`. It may not be possible to obtain a result on Windows. |

<h4 id="example-action-env">Examples of environment variable usage</h4>

The following is a sample action configuration that executes a service startup or restart command with `action.command` when `MACKEREL_STATUS` is not OK.

Linux

```
action = { command = "bash -c '[ \"$MACKEREL_STATUS\" != \"OK\" ]' && systemctl restart SERVICE" }
```

Windows

```
action = { command = "if not %MACKEREL_STATUS% == OK ( net start SERVICE )" }
```

If the service name contains spaces, an error will occur with the net command if it is written as is, so use `action.env` and specify it in an environment variable.

```
action = { command = "if not %MACKEREL_STATUS% == OK ( net start %SERVICE% )", env = { SERVICE = "Sample Service" } }
```

<h2 id="specification">Check plugin specs</h2>

Alert levels are returned as follows based on the exit status of `command`.

| Exit status | Alert level |
|-----------|------------|
| 0 | OK |
| 1 | WARNING |
| 2 | CRITICAL |
| Any value besides 0, 1, or 2 | UNKNOWN |

A supplementary message may also be added to the standard output. The length of this message may not exceed 1024 characters. The output will be sent to Mackerel and displayed on the Host Details screen and the Alert Details screen. Therefore, please make sure that no confidential information, such as passwords, is sent.

For more information on how to develop check plugins using [github.com/mackerelio/checkers](https://github.com/mackerelio/checkers), the utility library used for the official check plugin, please refer to [Creating a check plugin using checkers](https://mackerel.io/docs/entry/advanced/checkers).

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
  - The host where the check plugin is run will make an OK or CRITICAL, WARNING, UNKNOWN decision and send the result to Mackerel. Mackerel will alert you according to the results it receives.
  - No metrics are posts, so no graphs are plotted.
  - Monitoring rules are configured within the mackerel-agent configuration file, and no configuration may be added or modified via the Web Console.
  -  Each check monitor item counts as one host metric. Please refer to [Pricing](https://mackerel.io/pricing) for more information on the maximum number of metrics allowed per host. Also, please refer to [How usage fees are calculated](https://support.mackerel.io/hc/en-us/articles/31304727432729) for more information on the specifications when the limit is exceeded.
- Illustrations of monitoring
  -[Left] Metric monitoring / [Right] Check monitoring
  - ![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200108/20200108154244.png)

<h3 id="example-ruby">An example in Ruby</h3>

This is a check plugin that takes the values of a six-sided die as messages, 4 and 5 being a WARNING and 6 being a CRITICAL, and posts them to Mackerel.


```ruby
#!/usr/bin/env ruby
dice = rand(6)+1
puts "value is #{dice}"
exit (dice >= 6 ? 2 : dice >= 4 ? 1 : 0)
```

By executing the agent configured with a check plugin, an item showing that monitoring is active will be displayed in the host details page as shown below.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230125/20230125174429.png)

If an alert is raised it will be displayed as shown below and can be confirmed in the alert details page.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230125/20230125174450.png)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230125/20230125174337.png)
