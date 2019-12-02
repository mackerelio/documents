---
Title: Adding monitors for script checks
Date: 2015-05-15T18:16:34+09:00
URL: https://mackerel.io/docs/entry/custom-checks
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450094528333
---

Check monitoring is a feature that monitors the check plugin execution results similarly to Nagios. The agent periodically runs the check plugin and sends the results to Mackerel. 

As for **metric monitoring**, which monitors the thresholds of metric values ​​sent to Mackerel, they differ in the following ways.

- With metric monitoring, the host posts metric values and Mackerel compares/judges those values against thresholds.
  - Metrics are displayed as graphs, and monitors can be configured from the web console or API
- With check monitoring, plugins are used to make OK / NG (`CRITICAL` or `WARNING` or `UNKNOWN`) judgments within the host and post the results to Mackerel.
    - Graphs are not displayed because metrics are not posted. Monitors can not be configured from the web console, but configurations can be added to mackerel-agent installed on the host.

[f:id:mackerelio:20191126110656p:plain]

In order to use check monitoring with mackerel-agent, a program is required that performs the target monitoring process and returns the exit status according to the results. For this reason, an official check plugin pack is available. For more information please refer to [Using the official check plugin pack for check monitoring](https://mackerel.io/docs/entry/howto/mackerel-check-plugins).

Check monitors will be counted as 1 host metric each. See [here](https://mackerel.io/pricing) for the limits of each plan, and [here](https://mackerel.io/docs/entry/faq/contracts/limit-exceeded-conversion) for metric limits per host and specifications when limits are exceeded.

<h2 id="setting">Configuration</h2>

In the [agent settings file](https://mackerel.io/docs/entry/spec/agent#config-file), add an item as shown here:

```config
[plugin.checks.ssh]
command = ["ruby", "/path/to/check-ssh.rb"]
custom_identifier = "SOME_IDENTIFIER" # optional
notification_interval = 60
max_check_attempts = 1
check_interval = 5
timeout_seconds = 45
prevent_alert_auto_close = true
env = { HOST = "hostname", PORT = "port" }
action = { command = "bash -c '[ \"$MACKEREL_STATUS\" != \"OK\" ]' && ruby /path/to/notify_something.rb", env = { NOTIFY_API_KEY = "API_KEY" }, user = "someone", timeout_seconds = 45 }
memo = "This check monitor is ..."
```

- Item name: With the key for the settings file, the item name must begin with "plugin.checks." and contain exactly two periods. Anything after the second dot will be used as the monitor settings name.
- command: This command will have the agent temporarily execute, and use it’s exit status/standard output as the monitoring result.
- custom_identifier: Monitoring results are sent as a monitor of the host of the specified identifier, not the host on which the agent is running.
    - If the check result is not OK, it will be notified as an alert for the host that is specified here.    
    - This can be useful for adding monitors to hosts integrated with AWS / Azure Integration. For more details, refer to the [AWS Integration Document](https://mackerel.io/docs/entry/integrations/aws#plugin-custom-identifier).
- notification_interval: The notification re-sending interval will be designated in minutes. If the notification is abbreviated, it will not be re-sent. An interval of less than 10 minutes can not be designated. If an interval of less than 10 min is designated, the notification will be re-sent at 10 minutes. 
- max_check_attempts: An alert will be sent for any result other than “okay” in the designated number sequence. For example, if set at 3 and the latest monitoring result for all three is not ok, then a notification will be sent. **When used with  `prevent_alert_auto_close`, the value of `max_check_attempts` will be treated as `1` regardless of the specified value.**
- check_interval: Designate the check monitoring execution interval in minutes. The default value is 1 minute. The configurable range is 1 to 60 minutes. If a value of less than 1 minute is designated, monitoring will be run at 1 minute intervals. If a value of more than 60 minutes is designated, monitoring will be run at 60 minute intervals.
- timeout_seconds: Specify the plugin timeout in seconds. The default value is 30 seconds. Since simultaneous activation for each plugin is not controlled, we recommend that the plugin execution interval not be exceeded.
- prevent_alert_auto_close: With this value set to true, alerts opened for this check plugin will not be automatically closed. **When used with `max_check_attempts`, `max_check_attempts` will always be treated as `1`.**
- env: Environment variables can be specified to pass to command. Specify with TOML [Table](https://github.com/toml-lang/toml#table) or [Inline Table](https://github.com/toml-lang/toml#inline-table).
- action.command: An action executed following the execution of the command configured in `command`. This is used when there is a process to be performed depending on the command result. The result of the previous/current command etc. is passed as an environment variable. The execution result is ignored.
- action.env: Environment variables can be specified to pass to action.command. Specify with TOML [Table](https://github.com/toml-lang/toml#table) or [Inline Table](https://github.com/toml-lang/toml#inline-table).
- action.timeout_seconds : Specify the timeout for `action.command` in seconds. The default value is 30 seconds.
- action.user: Execute `action.command` as the user specified for this option.
- memo: Configure notes for check monitoring. The character string specified here can be checked in alert notifications / the alert details screen / the host details page.

<h2 id="plugin">Check plugin specs</h2>

The specs for the [Nagios][] plugin and the [Sensu][] check script are mostly the same.
In the settings file, the assign command’s exit status will be treated as shown below.

|exit status|meaning|
|:-----------|------------:|:------------:|
| 0 | OK |
| 1 | WARNING |
| 2 | CRITICAL |
| other than 0,1, or 2 | UNKNOWN |

It's also possible to add an auxiliary message to the standard output. The maximum character limit for messages is 1024. This output is sent to Mackerel and visualized in the host's details and Alerts page. For this reason, please be careful not to unintentionally send confidential information such as passwords.


About to develop a plugin using [github.com/mackerelio/checkers](https://github.com/mackerelio/checkers) (a helper library that is used in our official plugins), please refer to [Creating check plugins using checkers](https://mackerel.io/docs/entry/advanced/checkers).

<h2 id="notification">Check monitoring notifications</h2>

An alert notification will be sent when an alert has occurred and when the condition has been changed after an alert has occurred. Two cases for “when the alert condition has been changed” follow below. 

- When the status has changed 
    - ex. CRITICAL -> WARNING, WARNING -> CRITICAL, CRITICAL -> OK
    - Including when the status is “OK”

When the condition of an alert or the message content has changed, that information will also be available in the alert details screen. A notification will not occur if just the message content changes. 

<h2 id="action-env">Environment variables available with action</h2>

|Environment variable|Description|
|:-----------|:------------|
| MACKEREL_STATUS | The result of the previous command (`max_check_attempts` not taken into account).Either `OK`, `WARNING`, `CRITICAL`, or `UNKNOWN`. |
| MACKEREL_PREVIOUS_STATUS | The result of the command before the previous command (`max_check_attempts` not taken into account). The initial result is an empty string after starting-up the agent. Either an empty string, `OK`, `WARNING`, `CRITICAL`, or `UNKNOWN`.|
| MACKEREL_CHECK_MESSAGE | The result message of the previous command ( `command` stdout). |

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

[Nagios]: http://www.nagios.org/
[Sensu]: https://sensuapp.org/
