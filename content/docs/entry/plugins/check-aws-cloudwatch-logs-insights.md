---
Title: Check plugins - check-aws-cloudwatch-logs-insights
Date: 2023-04-25T18:23:06+09:00
URL: https://mackerel.io/docs/entry/plugins/check-aws-cloudwatch-logs-insights
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/4207112889984434834
---

check-aws-cloudwatch-logs-insights is a plugin that monitors log group of CloudWatch Logs. To run this plugin, you must be authorized to run the CloudWatch Logs Insights API. For more information, see [Authentication and Required Policies](#policy). See [difference from check-aws-cloudwatch-logs](#difference) for the difference from check-aws-cloudwatch-logs.

[:contents]

<h2 id="installation">How to Install</h2>

check-aws-cloudwatch-logs-insights is not included in the [official check plugin pack](https://mackerel.io/docs/entry/howto/mackerel-check-plugins) and requires additional installation.


### When installing using the mkr command

Set up mkr so that you can install plugins with the mkr command. Please refer to the following help page for installation instructions.

[https://mackerel.io/docs/entry/advanced/cli:embed:cite]

Execute the following mkr command.

```
sudo mkr plugin install check-aws-cloudwatch-logs-insights
```

### To download and use without installation

The plugin executable is available at [GitHub Releases](https://github.com/mackerelio/check-aws-cloudwatch-logs-insights/releases). Please download the appropriate file for your environment.


<h2 id="specification">Monitoring Specifications</h2>

The first time check-aws-cloudwatch-logs-insights is run, it monitors logs from the time 5 minutes before the current time up to an additional minute before.

- ex. 1st run at 12:00
  - Target is 11:54 (startTime) to 11:55 (endTime)

The endTime is recorded in [State file](#state-file) each time it is executed, and from the second time onward, the log is monitored from that time until 5 minutes before the current time.

- ex. 1st run 12:00, 2nd run 12:05
  - Target is 11:55 (startTime) to 12:00 (endTime)

If more than 90 minutes has passed since the last execution, it monitors logs from the time 5 minutes before the current time up to an additional 1 minute before as in the case of the first execution.

<h2 id="options">Configurable options</h2>

| Option            | Short | Description                                                                 | Default  |
| --------------------- | ------ | -------------------------------------------------------------------- | -------- |
| --log-group-name |  | Specify target log group |  |
| --filter | -f | Set search strings for monitoring in CloudWatch Logs Insights query syntax |  |
| --warning-over | -w | Warning alert is issued when the number of lines matching the detection pattern exceeds the specified value | 0 |
| --critical-over | -c | Critical alert is issued when the number of lines matching the detection pattern exceeds the specified value | 0 |
| --state-dir | -s | Specify the directory path where the State file is saved | See [About State file](#state-file) |
| --return | -r | Log lines matching the pattern will be noted in the alert notification (Up to 1024 characters) |  |
| --help | -h | Show help |  |

<h3 id="filter">How to write --pattern option</h3>

--pattern is written in CloudWatch Logs Insights query syntax. Please refer to the AWS documentation for details on how to specify.

[https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax.html:embed:cite]


<h3 id="state-file">About State file</h3>

If the `--state-dir` option is not specified, the State file will be saved in the following directory in the format `<hash string>.json`.

- When run via mackerel-agent
  - `/var/tmp/mackerel-agent/check-aws-cloudwatch-logs-insights`
- When run manually
  - `/tmp/check-aws-cloudwatch-logs-insights`


<h2 id="config">Example configurations</h2>

In the following configuration, a Warning alert is detected when one or more logs containing the word `ERROR` are output to the log group `/aws/lambda/some-lambda-function` in CloudWatch Logs, and a Critical alert is detected when 10 or more logs containing the word `ERROR` are output.

```toml
[plugin.checks.aws-cloudwatch-logs-insights-sample]
command = ["check-aws-cloudwatch-logs-insights", "--log-group-name", "/aws/lambda/some-lambda-function", "--filter", "filter @message like /ERROR/", "--warning-over", "1", "--critical-over", "10"]
env = { AWS_REGION = "ap-northeast-1" }
```

<h3 id="region">How to specify a region</h3>

The region is specified by the check monitoring environment variable, not by a plugin option. Named profiles by the AWS_PROFILE environment variable are also supported.

```
env = { AWS_REGION = "ap-northeast-1" }
```

<h3 id="policy">Authentication and required policies</h3>

check-aws-cloudwatch-logs-insights uses the API of CloudWatch Logs Insights. Please check that the credentials of the IAM user/role are available to perform the following actions on the monitored log groups.

- `logs:GetQueryResults`
- `logs:StartQuery`
- `logs:StopQuery`

The following methods are supported for setting credentials.

- Use of instance profiles (when monitoring from EC2 instances)
- Use named profiles in the AWS_PROFILE environment variable
- Specify the environment variable `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` directly with `env = {}` in mackerel-agent.conf.


<h2 id="difference">Difference from check-aws-cloudwatch-logs</h2>

There is also a plugin [check-aws-cloudwatch-logs](https://mackerel.io/docs/entry/plugins/check-aws-cloudwatch-logs) that monitors CloudWatch Logs logs. The differences from check-aws-cloudwatch-logs are as follows

### Can monitor log groups with high volume

check-aws-cloudwatch-logs may time out if the log volume is too large. check-aws-cloudwatch-logs-insights uses the 'CloudWatch Logs Insights API' and can successfully monitor a large number of logs.

### To filter the characters to monitor, use a different syntax

- check-aws-cloudwatch-logs
  - Use the [filter and pattern syntax](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/FilterAndPatternSyntax.html)
- check-aws-cloudwatch-logs-insights
  - Use the [CloudWatch Logs Insights query syntax](CloudWatch Logs Insights query syntax)

### Monitor logs up to 5 minutes before the current time

As described in [Monitoring Specifications](#specification), this plugin monitors logs up to 5 minutes before the current time, so the monitoring is less real-time than check-aws-cloudwatch-logs.

### CloudWatch Logs Insights API usage fee will be charged

The FilterLogEvents API used by check-aws-cloudwatch-logs is not charged for API requests. On the other hand, the CloudWatch Logs Insights API used by check-aws-cloudwatch-logs-insights charges a fee based on the amount of log data scanned. For CloudWatch Logs Insights API pricing, please see the AWS pricing page.

[https://aws.amazon.com/cloudwatch/pricing/:embed:cite]

<h2 id="troubleshoot">Troubleshooting</h2>

### UNKNOWN: context canceled occurred

- Possible Causes
  - This error occurs when plugin execution is interrupted. This plugin uses the CloudWatch Logs Insights API provided by AWS to retrieve log data on CloudWatch. It is possible that the plugin execution timed out because it took a long time to send the API request or receive a response (We are not able to investigate what kind of issue was occurring on the AWS-provided API side).
- Affect
  - Fails to check logs for the period that was monitored when the error occurred. Please check the [Monitoring Specifications](#specification) for the period to be monitored.
- How to deal with it
  - If the timeout mentioned above is the actual cause of the error, you may be able to avoid the error by extending the time between plugin execution timeouts with timeout_seconds. For more information on timeout_seconds, see the [Configuration items](https://mackerel.io/docs/entry/custom-checks#items).

<h2 id="repository">Repository</h2>

https://github.com/mackerelio/check-aws-cloudwatch-logs-insights
