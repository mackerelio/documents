---
Title: Monitoring Amazon CloudWatch Logs
Date: 2018-09-25T18:37:35+09:00
URL: https://mackerel.io/docs/entry/howto/check/aws-cloudwatch-logs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10257846132639277272
---

With `check-aws-cloudwatch-logs` included in the official check plugin pack, you can monitor log data from [Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html). For information on how to install the official check plugin pack, refer to [Using the official check plugin pack for check monitoring](https://mackerel.io/docs/entry/howto/mackerel-check-plugins).

## How to use `check-aws-cloudwatch-logs`

To monitor CloudWatch Logs with `check-aws-cloudwatch-logs`, write the following for example in mackerel-agent.conf and restart mackerel-agent.

```toml
[plugin.checks.aws-cloudwatch-logs-sample]
command = ["check-aws-cloudwatch-logs", "--log-group-name", "/aws/lambda/sample_log_group", "--pattern", "Error", "--critical-over", "5", "--warning-over", "3"]
env = { AWS_REGION = "ap-northeast-1" }
```

The following options can be specified.

- `--log-group-name` (Required): Specify the name of the log group.
- `--log-stream-name-prefix`: Filter the stream name by prefix.
- `--pattern` (Required): The search string for monitoring. See [Filter and Pattern Syntax](https://docs.aws.amazon.com/en_us/AmazonCloudWatch/latest/logs/FilterAndPatternSyntax.html).
- `--warning-over`: Set to warning when more messages are detected than the set value.
- `--critical-over`: Set to critical when more messages are detected than the set value.
- `--return`: Send the detected messages to Mackerel. Be careful not to include confidential information. If the amount of messages is large, the display may be cut off.

For other options, check `check-aws-cloudwatch-logs --help`.

## Authentication and required policies
When monitoring from an EC2 instance, the instance profile is used for authentication. In order to run the plugin, the `logs: FilterLogEvents` action is required, so the policy needs to be attached.
Instead of using an instance profile, you can also configure an `AWS_PROFILE` or the `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY`. Specify this in `env` in the plugin settings.

## The `--pattern` configuration value
To filter log messages with the `--pattern` option, you can specify the syntax described in the Amazon CloudWatch Logs [Filter and Pattern Syntax](https://docs.aws.amazon.com/en_us/AmazonCloudWatch/latest/logs/FilterAndPatternSyntax.html). Note that this is not a regular expression and more than one can not be specified.

## Unknown: command timed out
The plugin may time out for log streams with high flow rates or log groups with numerous streams.

You can extend the timeout by specifying `timeout_seconds` in the plugin settings. However, it can not be specified to exceed the execution interval (default is 60 seconds). For more details, see [Adding monitors for script checks](https://mackerel.io/docs/entry/custom-checks).

If the log quantity is large, it may be inevitable that periodically running plugins cannot collect logs. However, there are countermeasures for this, such as reducing the quantity of logs and sampling.

## Source code

The source code for `check-aws-cloudwatch-logs` is published in the following link. 
 
<https://github.com/mackerelio/go-check-plugins/tree/master/check-aws-cloudwatch-logs>
