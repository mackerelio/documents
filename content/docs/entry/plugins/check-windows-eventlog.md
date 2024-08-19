---
Title: Check plugins - check-windows-eventlog
Date: 2022-12-15T17:43:14+09:00
URL: https://mackerel.io/docs/entry/plugins/check-windows-eventlog
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/4207112889945338246
---

check-windows-eventlog is a plugin that monitors Windows event logs. It raises an alert when it detects an event matching a specified pattern.

[:contents]

<h2 id="options">Configurable options</h2>

| Option | Abbreviation | Description | Default |
| --- | --- | --- | --- |
| --log | | Specify the type of event log you want to detect.<br>See the [Types of event logs that can be monitored](#log-type). | Application |
| --type | | Specify the event type you want to detect.<br>See the [Event type to be alerted](#event-type). |  |
| --source-pattern | | Specify the event source you want to detect. |  |
| --source-exclude | | Specify the event sources you want to exclude. |  |
| --message-pattern | | Specify the string pattern you want to detect with a regular expression. (AND conditions are not supported.) *1 |  |
| --message-exclude | | Specify the string pattern you want to exclude with a regular expression. (AND conditions are not supported.) *1 |  |
| --event-id-pattern | | Specify event IDs to be detected, Multiple specifications can be specified by separating them with commas, and ranges can be specified with hyphens. |  |
| --event-id-exclude | | Specify event IDs to exclude, Multiple specifications can be specified by separating them with commas, and ranges can be specified with hyphens. |  |
| --warning-over | -w | Warning alert occurs if the number of lines matching the detection pattern exceeds the specified value. | 0 |
| --critical-over | -c | Critical alert occurs if the number of lines matching the detection pattern exceeds the specified value. | 0 |
| --return | -r | Alerts you to log lines that match the pattern, Up to 1024 characters. |  |
| --state-dir | -s | Specify the directory path where the State file is to be saved. | See [About State File](#state-file). |
| --no-state | | Target all logs without use State file. |  |
| --fail-first | | Alerts on first check immediately after setting up the plugin. |  |
| --verbose | | Display execution results in detail. Used for debugging. |  |
| --help | -h | Show Help. |  |

- *1 If multiple options are specified, only the last option is valid.

<h3 id="log-type">Types of event logs that can be monitored</h3>

- Application
- Security
- System

<h3 id="event-type">Event type to be alerted</h3>

| Event Type | Alert Level |
|---|---|
| Error | Critical |
| Audit Failure | Critical |
| Warning | Warning |

Event types other than those listed above are not supported.

<h3 id="state-file">About State File</h3>

Since check-windows-eventlog monitors for differences in event log output, it records the last read EventRecordID in the State file.

If the `--state-dir` option is not specified, the State file will be stored in the following folder in the format `{monitored event log type}-<hash string>`.

- When executed via agent.
  - `C:\Windows\SystemTemp\check-windows-eventlog`
  - mackerel-agent v0.80.0 or earlier
    - `C:\Windows\Temp\check-windows-eventlog`
- When executed manually.
  - `check-windows-eventlog` folder under `Temp` folder of logged-in user. Check the Windows environment variable `TEMP` for the location of the `Temp` folder.

<h2 id="config">Example configurations</h2>

Target Error events in the Application log that contain the string foo and do not contain bar.

```
[plugin.checks.check-windows-eventlog-sample]
command = ["check-windows-eventlog", "--log", "Application", "--type", "Error", "--message-pattern", "foo, "--message-exclude", "bar"]
```

Target the Event IDs 900 and 901 in the Error event in the Application log.

```
[plugin.checks.check-windows-eventlog-sample]
command = ["check-windows-eventlog", "--log", "Application", "--type", "Error", "--event-id-pattern", "900,901"]
```

Among the Error events in the Application log, event IDs 900 through 1200 are targeted, and only 1101 is excluded.

```
[plugin.checks.check-windows-eventlog-sample]
command = ["check-windows-eventlog", "--log", "Application", "--type", "Error", "--event-id-pattern", "900-1200", "--event-id-exclude", "1101"]
```

<h2 id="troubleshoot">Troubleshooting</h2>

### "Perflib:Because the message resource could not be found, the event log message could not be obtained. Please access the target server and check the event log directly." event occurs.

This event occurs when the `--return` option is given and the contents of the event matching the pattern cannot be retrieved from the EventMessageFile. The cause may be that the log of the event source is not output in a format that meets the specifications of the event viewer. Please identify the relevant event in the Event Viewer based on the time when the alert occurred, and check the output format of the Event Source log.

### Event Log UNKNOWN: strconv.ParseInt: parsing "\x00\x00\x00\x00\x00\x00\x00": invalid syntax alert occurs.

The State file may be corrupt. Deleting the State file corresponding to the monitored event log will close the alert (The State file will be re-created at the next monitoring run). Please refer to [About State File](#state-file) for the location of the State file.

<h2 id="repository">Repository</h2>

https://github.com/mackerelio/go-check-plugins/tree/master/check-windows-eventlog
