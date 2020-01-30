---
Title: Monitoring Logs
Date: 2015-11-06T16:28:32+09:00
URL: https://mackerel.io/docs/entry/howto/check/log
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6653458415127142958
---

Using `check-log` from the official check plugin pack, you can monitor logs in Mackerel. For information on installing the official check plugin pack, you can refer to [Using the official check plugin pack for check monitoring](https://mackerel.io/docs/entry/howto/mackerel-check-plugins).

## Using `check-log`

To use `check-log` to do log monitoring, you can, for example, append the following item in mackerel-agent.conf and then restart the mackerel-agent.

```config
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "FATAL"]
```

Assign the target file to the `--file` option, and assign the error message pattern that you want to detect as a regular expression in the `--pattern` option. In the example above, if the string “FATAL” appears in the targeted log file, an alert will be triggered.

The initial check is done only for the diff output after adding settings. If you want all of the contents of an already existing file to be checked at the time of the initial check, specify the `--check-first` option.

```config
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "FATAL", "--check-first"]
```

Multiple `--pattern` options can be specified, in which case, they are treated with the AND operator (only log output lines that satisfy all specified patterns are detected). The following is a configuration example to have alerts occur when a log output line that includes the two strings "PRODUCTION" and "FATAL" appears.

```config
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/access.log", "--pattern", "PRODUCTION", "--pattern", "FATAL"]
```

Multibyte characters can be specified in `--pattern`, but in such cases, the conf file character code (encoding) must be `UTF-8`.

With the `--file` option, check targets can be specified in glob format.

```config
[plugin.checks.access_log]
command = ["check-log", "--file", "/var/log/*.log", "--pattern", "FATAL"]
```

Additionally, you can specify the filename of the monitoring target with regular expressions using the `--file-pattern` option.

```config
[plugin.checks.access_log]
command = ["check-log", "--file-pattern", "/var/log/access.log.\\d{4}-\\d{2}-\\d{2}", "--pattern", "FATAL"]
```

The log file will be checked periodically and any lines that have already been checked will be skipped. The execution interval is specified by `check_interval` ([reference](https://mackerel.io/docs/entry/custom-checks)) and the default is 1 minute.


If log rotation occurs, the log is read over from the beginning. To be exact, if the file size has become smaller since the previous check, it is considered a log rotation and a read-over from the beginning occurs.  

## Checking detected log output in Mackerel

By adding the `--return` option to the command, you can output the contents of detected log output in stdout which will be sent to Mackerel and visualized in the host's details and the Alerts screen. When using this option, please use caution so that sensitive information does not get transmitted unintentionally.

Furthermore, if the size of the transmitted contents is too large, the display may be trimmed down. For more details regarding specifications, refer to the [Check plugin specs](https://mackerel.io/docs/entry/custom-checks#plugin).

## Assigning a threshold and exclusion pattern for event frequency

The following option is useful in situations where if an error message appears just once it’s ok, but when frequent appearances occur you want an alert to be raised.

- `-w`, `--warning-over`
  - a warning alert will be created if the number of error appearances exceeds the configured value
- `-c`, `--critical-over`
  - a critical alert will be created if the number of error appearances exceeds the configured value
  
Additionally, by designating the `--exclude` option, it's possible to assign an exclusion pattern.

The following configuration, for example, will monitor the Nginx access log, and check the occurrence of 4xx and 5xx errors.

With the `--exclude` option we have excluded access to "robots.txt". Also, with `--warning-over` and `--critical-over` we've set it up so that if there are 3 or more occurrences within 1 minute a Warning alert will be raised, and 10 or more occurrences will result in a Critical alert.

```config
[plugin.checks.access_status]
command = ["check-log", "--file", "/var/log/nginx/access.log", "--pattern", "HTTP/1\.[01]\" [45][0-9][0-9] ", "--exclude", "GET .*?robots\.txt HTTP/1\.[01]", "--warning-over", "3", "--critical-over", "10", "--return"]
```

For more information regarding other options, please refer to `check-log --help` and the [README](https://github.com/mackerelio/go-check-plugins/blob/master/check-log/README.md)

## Source code

The source code for `check-log` is publicly available via the URL below.

<https://github.com/mackerelio/go-check-plugins/tree/master/check-log>
