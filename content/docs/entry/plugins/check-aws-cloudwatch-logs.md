---
Title: Check plugins - check-aws-cloudwatch-logs
Date: 2023-04-19T12:10:37+09:00
URL: https://mackerel.io/docs/entry/plugins/check-aws-cloudwatch-logs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/4207112889982640479
CustomPath: plugins/check-aws-cloudwatch-logs
---

check-aws-cloudwatch-logs is a plugin that monitors log group of CloudWatch Logs. This plugin will generate an alert when it detects logs that match the conditions described in the CloudWatch Logs [filter and pattern syntax](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/FilterAndPatternSyntax.html). This plugin requires configuration to run FilterLogEvents API of CloudWatch Logs. See [Authentication and required policies](#policy) for more information.

[:contents]

<h2 id="specification">Monitoring Specifications</h2>

check-aws-cloudwatch-logs monitors logs up to 1 minute before the current time when first run.

- ex. 1st run at 12:00
  - Target is 11:59 (startTime) to 12:00 (endTime)

The endTime is recorded in [State file](#state-file) each time it is executed, and from the second time onward, the log is monitored from the current time to that time.

- ex. 1st run 12:00, 2nd run 12:05
  - Target is 12:00 (startTime) to 12:05 (endTime)

If more than one hour has passed since the last execution, logs up to 1 minute before the current time will be monitored as in the case of the first execution.


<h2 id="options">Configurable options</h2>

| Option                 | Short | Description                                                                                                  | Default                             |
| ---------------------- | ----- | ------------------------------------------------------------------------------------------------------------ | ----------------------------------- |
| --log-group-name         |       | Specify target log group                                                                                            |                                     |
| --log-stream-name-prefix |       | Prefix to filter log stream                                                                                  |                                     |
| --pattern                | -p    | Set search strings for monitoring in CloudWatch Logs filter and pattern syntax              |                                     |
| --warning-over           | -w    | Warning alert is issued when the number of lines matching the detection pattern exceeds the specified value  | 0                                   |
| --critical-over          | -c    | Critical alert is issued when the number of lines matching the detection pattern exceeds the specified value | 0                                   |
| --state-dir              | -s    | Specify the directory path where the State file is saved                                               | See [About State file](#state-file) |
| --return                 | -r    | Log lines matching the pattern will be noted in the alert notification (Up to 1024 characters)               |                                     |
| --max-retries            | -t    | Specify the maximum number of query attempts to CloudWatch Logs                                              | 3                                   |
| --help                   | -h    | Show help                                                                                                    |                                     |

<h3 id="pattern">How to write --pattern option</h3>

--pattern is written in CloudWatch Logs filter and pattern syntax. Please refer to the AWS documentation for details on how to specify.

[https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/FilterAndPatternSyntax.html:embed:cite]

<h3 id="state-file">About State file</h3>

If the `--state-dir` option is not specified, the State file will be saved in the following directory in the format `{monitoring log group name}-<hash string>.json`.

- When run via mackerel-agent
  - `/var/tmp/mackerel-agent/check-cloudwatch-logs`
- When run manually
  - `/tmp/check-cloudwatch-logs`


<h2 id="config">Example configurations</h2>

The following is a configuration for detecting logs containing the word "Error" in the CloudWatch Logs log group `/aws/lambda/sample_log_group`.

```
[plugin.checks.aws-cloudwatch-logs-sample]
command = ["check-aws-cloudwatch-logs", "--log-group-name", "/aws/lambda/sample_log_group", "--pattern", "Error"]
env = { AWS_REGION = "ap-northeast-1" }
```

<h3 id="region">How to specify a region</h3>

The region is specified by the check monitoring environment variable, not by a plugin option. Named profiles by the AWS_PROFILE environment variable are also supported.

```
env = { AWS_REGION = "ap-northeast-1" }
```

<h3 id="policy">Authentication and required policies</h3>

check-aws-cloudwatch-logs uses the FilterLogEvents API of CloudWatch Logs. Please check that the credentials of the IAM user/role are available to perform the following actions on the monitored log groups.

- `logs:FilterLogEvents`

The following methods are supported for setting credentials.

- Use of instance profiles (when monitoring from EC2 instances)
- Use named profiles in the AWS_PROFILE environment variable
- Specify the environment variable `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` directly with `env = {}` in mackerel-agent.conf.


<h2 id="troubleshoot">Troubleshooting</h2>

### `Unknown: command timed out' occurred

A log stream with a high flow rate or a log group with a large number of streams may cause the plugin execution to time out. You can extend the timeout by specifying `timeout_seconds` in the plugin configuration. However, it must be specified so that it does not exceed `check_interval`. For more information, see [Adding monitors for script checks](https://mackerel.io/docs/entry/custom-checks).

If the log volume is large, a timeout may occur. If timeouts occur frequently, reduce the amount of log volume or sample the logs. If this is difficult, consider using [check-aws-cloudwatch-logs-insights](https://mackerel.io/docs/entry/plugins/check-aws-cloudwatch-logs-insights), which also supports monitoring of large log groups.

<h2 id="repository">Repository</h2>

https://github.com/mackerelio/go-check-plugins/tree/master/check-aws-cloudwatch-logs
