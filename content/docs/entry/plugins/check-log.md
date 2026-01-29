---
Title: Check plugins - check-log
Date: 2023-01-27T09:08:26+09:00
URL: https://mackerel.io/docs/entry/plugins/check-log
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/4207112889957873684
---

check-log is a plug-in that monitors log files on the server, including applications and middleware.

Pattern matching using regular expressions is performed on the output differences of the monitored log files, and alerts are notified.

[:contents]

<h2 id="options">Monitoring Specifications</h2>

<h3 id="">Logs subject to monitoring</h3>

The check-log plugin records the number of bytes read from the monitored file and the inode number (Linux only) in the [State file](#state-file) each time it runs. On subsequent runs, it monitors only the differences since the previous run.

<h3 id="rotation-method">Regarding the corresponding rotation system</h3>

The check-log plugin supports the create method (creating a new file with the same name after moving the file) as the rotation method for monitored logs. For the copytruncate method (deleting the contents of the source file after copying), it does not track the copied file during rotation and only checks the contents of the original file from the beginning after its contents have been deleted.

<h2 id="options">Configurable options</h2>

| Option                | Short | Description                                                                                                          |  Default |
| --------------------- | ----- | -------------------------------------------------------------------------------------------------------------------- |  ------- |
| --file                | -f    | Specify the path of the file to be monitored (can be specified in glob format) [*1](#annotation1)                                      |          |
| --pattern             | -p    | Specify the pattern of error wording to be detected with a regular expression [*2](#annotation2)                                   |          |
| --exclude             | -E    | Specify patterns to exclude from detection using regular expressions [*2](#annotation2)                                               |          |
| --warning-over        | -w    | Raise a Warning alert if the number of rows matching the detection pattern exceeds the specified value               | 0        |
| --critical-over       | -c    | Raise a Critical alert if the number of rows matching the detection pattern exceeds the specified value              | 0        |
| --warning-level       |       | Raise a Warning alert if the value extracted by the detection pattern exceeds the specified value                    |          |
| --critical-level      |       | Raise a Critical alert if the value extracted by the detection pattern exceeds the specified value                   |          |
| --return              | -r    | Notify of log lines that match the pattern with an alert [*3](#annotation3)                                                         |          |
| --search-in-directory |       | Specify the directory path where the files to be monitored are located. When using this option, use `--file-pattern` instead of `--file` to specify the monitored files |          |
| --file-pattern        | -F    | Specify monitored files using a regular expression [*4](#annotation4). On Windows, it is recommended to use this in conjunction with `--search-in-directory` ([Example](#windows-file-pattern)) |          |
| --icase               | -i    | Case-insensitive matching                                                                                            |          |
| --state-dir           | -s    | Specify the directory path where the State file is to be saved                                                       |          |
| --no-state            |       | All logs are checked without State file                                                                              |          |
| --encoding            |       | Specify character encoding of monitored files                                                                        |          |
| --missing             |       | Specify alert level if monitored files are missing [*5](#annotation5)                                                                   |  UNKNOWN |
| --check-first         |       | Check all contents at the time of the first check of the file [*6](#annotation6)                                                     |          |
| --suppress-pattern    |       | Suppress the display of detection patterns on the host detail screen                                                 |          |

- <span id="annotation1">*1</span>
  - When executing via the command line or using string format for command specification in mackerel-agent.conf, glob patterns like `--file "/var/log/*.log"` must be enclosed in single quotes.
- <span id="annotation2">*2</span>
  - Multiple specifications form an AND condition. See also [Alerts when multiple patterns are matched by AND conditions](#multiple).
  - When treating metacharacters in regular expressions as literal strings, they must be escaped. For details, see [Treating Regular Expression Metacharacters as Plain Text](#metacharacters).
  - You can also specify Japanese (multibyte characters) as patterns, but in that case, the character encoding of the conf file must be UTF-8.
- <span id="annotation3">*3</span>
  - To include detected log lines in alerts, add the `--return` option.
  - Content exceeding 1024 characters will be truncated by mackerel-agent.
- <span id="annotation4">*4</span>
  - Directory paths cannot be specified using regular expressions.
- <span id="annotation5">*5</span>
  - This is ignored if `--search-in-directory` and `--file-pattern` are used to specify the monitoring target.
- <span id="annotation6">*6</span>
  - If this option is not set, during the initial check of a monitored file (e.g., immediately after log rotation), only the file size is recorded, and the content is not checked.
  - See also [Log lines matching the condition are output but not detected](#not_detected).

<h3 id="state-file">About state file</h3>

If the `--state-dir` option is not specified, the agent creates a directory hierarchy matching the monitored log file's directory structure under the following default locations for each OS, and creates files in the format `{monitored file}-<hash string>.json`. For example, if logs under `/var/log` are being monitored, they will be saved under the directory `/var/tmp/mackerel-agent/check-log/var/log`.

- For Linux
  - When executed via mackerel-agent
    - `/var/tmp/mackerel-agent/check-log`
  - When executed manually
    - `/tmp/check-log`
- For Windows
  - When executed via mackerel-agent
    - `C:\Windows\Temp\check-log`
  - When executed manually
    - The check-log folder under the logged-in user's Temp folder (Check the location of the Temp folder via the Windows environment variable TEMP)

<h2 id="config">Example configurations</h2>

<h3 id="config-linux">for Linux</h3>

The configuration for detecting `ERROR` output to `/var/log/access.log` is as follows

```toml
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "ERROR", "--return"]
```

To verify the above settings by executing them directly from the terminal or similar, run the following command:

```
check-log --file /var/log/access.log --pattern "ERROR" --return --no-state
```

<h3 id="config-windows">for Windows</h3>

The configuration to detect when a log containing the string "ERROR" is output to `C:\log\access.log` is as follows. Although the path separator character `\` in Windows environments is normally a single character, you must specify two characters when configuring it in mackerel-agent.conf.

```toml
[plugin.checks.access_log]
command = ["check-log", "--file", "C:\\log\\access.log", "--pattern", "ERROR", "--return"]
```

To verify the above settings by executing them directly from the Command Prompt or similar, run the following:

```
check-log --file "C:\log\access.log" --pattern "ERROR" --return --no-state
```

<h2 id="tips">Tips</h2>

### Specifying thresholds and exclusion patterns for occurrence frequency

Using `--warning-over` or `--critical-over` allows alerts to be triggered only when the number of logs output since the previous run exceeds the specified number of lines.
The following example triggers a WARNING if `ERROR` appears in `/var/log/nginx/error.log` three or more times, and a CRITICAL if it appears ten or more times.

```toml
[plugin.checks.access_status]
command = ["check-log", "--file", "/var/log/nginx/error.log", "--pattern", "ERROR","--warning-over", "3", "--critical-over", "10", "--return"]
```

You can also specify exclusion patterns using the `--exclude` option.
For example, the following configuration monitors Nginx access logs to check for 4xx and 5xx errors.
Specifying `--exclude` excludes access to "robots.txt".

```toml
[plugin.checks.access_status]
command = ["check-log", "--file", "/var/log/nginx/access.log", "--pattern", "HTTP/1\.[01]\" [45][0-9][0-9] ", "--exclude", "GET .*?robots\.txt HTTP/1\.[01]", "--return"]
```

<h3 id="multiple">Alerts when multiple patterns are matched by AND conditions</h3>

Specifying multiple `--pattern` or `--exclude` options creates an AND condition, allowing you to target logs matching all specified conditions for alerts.

The following example triggers an alert when logs containing the strings "PRODUCTION" and "ERROR" are detected.

```toml
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "PRODUCTION", "--pattern", "ERROR", "--return"]
```

### Alerts when any of the OR conditions are matched

When specifying multiple conditions separated by pipes `|` with `--pattern` or `--exclude`, they are treated as OR conditions, allowing you to target logs matching any specified condition for alerts.

The following example triggers an alert when logs containing the strings "FATAL" or "ERROR" are detected.

```toml
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "FATAL|ERROR", "--return"]
```

<h3 id="metacharacters">Treating Regular Expression Metacharacters as Literals</h2> 

When using `--pattern` or `--exclude`, if you want to treat regular expression metacharacters (characters with special meanings like `[]`) as literal characters, you must escape them by placing the escape character `\` before the metacharacter.

Below is a configuration example for detecting logs containing the string [ERROR].

When the `--pattern` argument is enclosed in single quotes `'`, a single escape character (`\`) is required before each metacharacter.

```
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "\[ERROR\]", "--return"]
```

If the `--pattern` argument is enclosed in double quotation marks `"`, two escape characters (`\\`) are required before each metacharacter.

```
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "\\[ERROR\\]", "--return"]
```

Additionaly if the pattern itself contains a double quotation mark `"`, and the `--pattern` argument is enclosed in double quotation marks `"`, three escape characters (`\\\`) are required before each double quotation mark contained `--pattern`.   
The following example detects logs containing the string "ERROR".

```
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "\\\"ERROR\\\"", "--return"]
```

### Targeting multiple log files

<h4 id="linux-file-pattern">for Linux</h4>

When targeting multiple files instead of a single file, the `--file` option supports glob patterns, while the `--file-pattern` option supports regular expressions.
The following example targets log files matching `/var/log/*.log`.

```toml
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/*.log", "--pattern", "FATAL"]
```

Additionally, you can specify the conditions for filenames to monitor using regular expressions with the `--file-pattern` option. Note that you cannot specify directory paths with regular expressions. Set monitors for each directory.
  
The following example targets files matching the regular expression `/var/log/access.log.\\d{4}-\\d{2}-\\d{2}` (such as `/var/log/access.log.2014-09-17`).

```toml
[plugin.checks.access_log]
command = ["check-log", "--file-pattern", "/var/log/access.log.\\d{4}-\\d{2}-\\d{2}", "--pattern", "FATAL"]
```

<h4 id="windows-file-pattern">for Windows</h4>

When using regular expressions with the `--file-pattern` option to specify files to monitor in a Windows environment, use the `--search-in-directory` option to avoid conflicts between the directory separator `\` and regular expression escaping.

```toml
[plugin.checks.access_log]
command = ["check-log", "--search-in-directory", "C:\\log\\", "--file-pattern", "access.log.\\d{4}-\\d{2}-\\d{2}", "--pattern", "ERROR", "--return"]
```

### To trigger only warning alerts

By default, `check-log` sets `--warning-over` and `--critical-over` to 0. Since the Critical alert level takes precedence, if these options are not specified, a Critical alert will be triggered upon detecting even a single log matching the conditions.

To ensure all alerts are triggered at the Warning level, specify a value for `--critical-over` that is absolutely not exceeded.

```
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "ERROR", "--critical-over", "9999", "--return"]
```

<h2 id="troubleshoot">Troubleshooting</h2>

<h3 id="not_detected">Log lines matching the condition are output but not detected</h3>

Please review your settings, etc., as the following cases are often seen.

- Make sure the `--check-first` option is specified.
  - If not specified, the contents of the monitored file will not be checked the first time it is checked.
  - The same applies to the initial check when a file is switched due to log rotation, etc.
- Make sure that the detection pattern specified in the `--pattern` option is correct.
  - Since the detection pattern is evaluated as a regular expression, escape it if it contains characters that are interpreted as regular expression meta characters, such as half-width parentheses.
  - See also [Treating Regular Expression Metacharacters as Literals](#metacharacters).
- Make sure that the create method is used as the rotation method.
  - If logs are rotated using the copytruncate method, it is not possible to check for logs added from the plugin is run before rotation to rotation is run.
- Please verify that the relevant log lines contain line breaks.
  - Logs without a line break code at the end of the line cannot be detected.

Also, if the file size when check-log is executed after rotation is larger than the file size when check-log is executed before rotation, rotation cannot be detected, and detection cannot be performed for logs written up to the size checked by check-log before rotation.

### "LOG UNKNOWN: unexpected end of JSON input" alert is issued

The State file in which the check-log plugin records the monitoring status may be corrupted.

Deleting or renaming the State file may solve the problem. Please refer to [About the State file](#state-file) for details.
Also, since the monitoring state is reset after this action, the behavior will be the same as when it is executed for the first time.

<h2 id="repository">Repository</h2>

https://github.com/mackerelio/go-check-plugins/tree/master/check-log
