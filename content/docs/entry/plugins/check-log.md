---
Title: Check plugins - check-log
Date: 2023-01-27T09:08:26+09:00
URL: https://mackerel.io/docs/entry/plugins/check-log
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/4207112889957873684
---

check-log is a plug-in that monitors log files on the server, including applications and middleware.

Pattern matching using regular expressions is performed on the output differences of the monitored log files, and alerts are notified.

[:contents]

<h2 id="options">Configurable options</h2>

| Option                | Short | Description                                                                                                          |  Default |
| --------------------- | ----- | -------------------------------------------------------------------------------------------------------------------- |  ------- |
| --file                | -f    | Specify the path of the file to be monitored (can be specified in glob format)                                       |          |
| --pattern             | -p    | Specify the pattern of error wording to be detected with a regular expression *1                                     |          |
| --exclude             | -E    | Specify patterns to exclude from detection using regular expressions *1                                              |          |
| --warning-over        | -w    | Raise a Warning alert if the number of rows matching the detection pattern exceeds the specified value               | 0        |
| --critical-over       | -c    | Raise a Critical alert if the number of rows matching the detection pattern exceeds the specified value              | 0        |
| --warning-level       |       | Raise a Warning alert if the value extracted by the detection pattern exceeds the specified value                    |          |
| --critical-level      |       | Raise a Critical alert if the value extracted by the detection pattern exceeds the specified value                   |          |
| --return              | -r    | Notify of log lines that match the pattern with an alert. *1                                                         |          |
| --search-in-directory |       | Specify the directory path where the monitored files are located (in Windows environment, use with `--file-pattern`) |          |
| --file-pattern        | -F    | Specify files to be monitored by regular expression                                                                  |          |
| --icase               | -i    | Case-insensitive matching                                                                                            |          |
| --state-dir           | -s    | Specify the directory path where the State file is to be saved                                                       |          |
| --no-state            |       | All logs are checked without State file                                                                              |          |
| --encoding            |       | Specify character encoding of monitored files                                                                        |          |
| --missing             |       | Specify alert level if monitored files are missing                                                                   |  UNKNOWN |
| --check-first         |       | Check all contents at the time of the first check of the file                                                        |          |
| --suppress-pattern    |       | Suppress the display of detection patterns on the host detail screen                                                 |          |

- *1 If multiple conditions are specified, they become AND conditions. (`"--pattern", "A", "--pattern", "B"`)
- *2 Content exceeding 1024 characters will be truncated by mackerel-agent.

<h3 id="state-file">About state file</h3>

The check-log plugin monitors for differences in log file output, so the number of bytes read for each file and the inode number (Linux only) are recorded in the State file.

If the `--state-dir` option is not specified, a file in the format of `{monitored file}-<hash string>.json` is created in the same directory hierarchy as the monitored log file under the prescribed destination for each OS.

- Linux
  - `/var/tmp/mackerel-agent/check-log` or `/tmp/check-log`
- Windows
  - `C:\Windows\Temp\check-log`

<h3 id="rotation-method">Supported rotation methods of logfile</h3>

The check-log plugin supports the create (create a new file with the same name after moving a file) method as a rotation method for logs to be monitored.  
In the case of the copytruncate method (deletes the contents of the source file after copying the file), it does not track the copied file during rotation, but only checks the source file from the beginning where the contents have been deleted.

<h2 id="config">Example configurations</h2>

<h3 id="config-linux">for Linux</h3>

The configuration for detecting `ERROR` output to /var/log/access.log is as follows

```
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "ERROR"]
```

Specify the `--return` option to be notified with an alert of detected log lines.

```
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "ERROR", "--return"]
```

When a monitored file is checked for the first time, no content check (matching detection patterns) is performed. If you want to check the contents of the file for the first time, use the `--check-first` option.

```
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "ERROR", "--return", "--check-first"]
```

To check the above settings manually from a terminal or other device, execute as follows

```
check-log --file /var/log/access.log --pattern "ERROR" --return --check-first
```

<h3 id="config-windows">for Windows</h3>

The configuration for detecting `ERROR` output to C:\log\access.log is as follows

```
[plugin.checks.access_log]
command = ["check-log", "--file", "C:\\log\\access.log", "--pattern", "ERROR"]
```

If you use regular expressions to specify files with `--file-pattern` on Windows, please specify the directory path in the `--search-in-directory` option to avoid conflicts between the directory delimiter `\` and the regular expression escaping process.

If you want to specify a file of the form `C:\log\access.log.{yyyyy}-{mm}-{dd}` with a regular expression, it will look like this.

```
[plugin.checks.access_log]
command = ["check-log", "--search-in-directory", "C:\\log\\", "--file-pattern", "access.log.\\d{4}-\\d{2}-\\d{2}", "--pattern", "ERROR"]
```

Directory paths cannot be specified using regular expressions. Please configure monitoring rules for each directory.

<h2 id="tips">Tips</h2>

### Alerts when multiple patterns are matched by AND conditions

Multiple `--pattern` options will trigger an alert when log lines containing all keywords are detected.

The following is a condition that must contain `PRODUCTION` and `ERROR`.

```
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "PRODUCTION", "--pattern", "ERROR"]
```

### Alerts when any of the OR conditions are matched

The condition specified in the `--pattern` option is evaluated as a regular expression.

For example, if you want to detect log lines containing `FATAL` or `ERROR`, specify them separated by pipe `|` as follows

```
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "FATAL|ERROR"]
```

### If you only want to generate Warning alerts

By default, check-log sets `--warning-over` and `--critical-over` to 0. Also, beause the alert level for Critical is higher than for Warning,, if these options are not specified, a Critical alert will be triggered if even a single log matching the conditions is detected.

If you want all alerts to be generated as Warnings, you can do this by specifying a value that will never exceed `--critical-over`.

```
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "ERROR", "--critical-over", "9999", "--return"]
```

<h2 id="troubleshoot">Troubleshooting</h2>

### Log lines matching the condition are output but not detected

Please review your settings, etc., as the following cases are often seen.

- Make sure the `--check-first` option is specified.
  - If not specified, the contents of the monitored file will not be checked the first time it is checked.
  - The same applies to the initial check when a file is switched due to log rotation, etc.
- Make sure that the detection pattern specified in the `--pattern` option is correct.
  - Since the detection pattern is evaluated as a regular expression, escape it if it contains characters that are interpreted as regular expression meta characters, such as half-width parentheses.
- Make sure that the create method is used as the rotation method.
  - If logs are rotated using the copytruncate method, it is not possible to check for logs added from the plugin is run before rotation to rotation is run.

Also, if the file size when check-log is executed after rotation is larger than the file size when check-log is executed before rotation, rotation cannot be detected, and detection cannot be performed for logs written up to the size checked by check-log before rotation.

### "LOG UNKNOWN: unexpected end of JSON input" alert is issued

The State file in which the check-log plugin records the monitoring status may be corrupted.

Deleting or renaming the State file may solve the problem. Please refer to [About the State file](#state-file) for details.
Also, since the monitoring state is reset after this action, the behavior will be the same as when it is executed for the first time.

<h2 id="repository">Repository</h2>

https://github.com/mackerelio/go-check-plugins/tree/master/check-log
