---
Title: AWS Integration - Lambda
Date: 2017-03-03T11:09:29+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/lambda
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10328749687222912159
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/lambda/" target="_blank">AWS Lambda</a> metrics in AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 function = 1 Micro Host.
In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services. <br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

## Notes on metric retrieval

* The timestamp of Lambda metrics is the time when the function was invoked, so metrics are recorded in Mackerel graphs at the function invocation time.
  * Reference: [Viewing metrics for Lambda functions - AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/monitoring-metrics-view.html)
* AWS Integration can only retrieve metrics with timestamps within the past 10 minutes, so if a function's execution time exceeds 10 minutes, that function's metrics will not be retrieved by Mackerel.

## Obtaining metrics

The metrics obtainable with AWS Integration’s Lambda support are as follows. For `Metric` explanations, refer to the <a href="https://docs.aws.amazon.com/lambda/latest/dg/monitoring-functions-metrics.html" target="_blank">AWS help page</a>.

The maximum number of metrics obtainable is `10 + 10 × (number of Aliases) + 10 × (number of Versions)`.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|Count|Invocations<br>Errors<br>DeadLetterErrors<br>Throttles|lambda.count.invocations<br>lambda.count.errors<br>lambda.count.dead_letter_errors<br>lambda.count.throttles|integer|Sum|
|Duration [ms]|Duration|lambda.duration.average<br>lambda.duration.max<br>lambda.duration.min|float|Average<br>Maximum<br>Minimum|
|Iterator Age [ms]|IteratorAge|lambda.iterator_age.average<br>lambda.iterator_age.max<br>lambda.iterator_age.min|float|Average<br>Maximum<br>Minimum|

## Metric per alias
The metric name’s `ALIAS` will contain the name of the ALIAS. Metrics of `LATEST` version are classified as `unqualified` alias metrics.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|Count per alias|Invocations<br>Errors<br>DeadLetterErrors<br>Throttles|lambda.count_per_alias.ALIAS.invocations<br>lambda.count_per_alias.ALIAS.errors<br>lambda.count_per_alias.ALIAS.dead_letter_errors<br>lambda.count_per_alias.ALIAS.throttles|integer|Sum|
|Duration [ms] per alias|Duration|lambda.duration_per_alias.ALIAS.average<br>lambda.duration_per_alias.ALIAS.max<br>lambda.duration_per_alias.ALIAS.min|float|Average<br>Maximum<br>Minimum|
|Iterator Age [ms] per alias|IteratorAge|lambda.iterator_age_per_alias.ALIAS.average<br>lambda.iterator_age_per_alias.ALIAS.max<br>lambda.iterator_age_per_alias.ALIAS.min|float|Average<br>Maximum<br>Minimum|

## Metric per version
The metric name’s `VERSION` will contain the name of the version.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|Count per version|Invocations<br>Errors<br>DeadLetterErrors<br>Throttles|lambda.count_per_version.VERSION.invocations<br>lambda.count_per_version.VERSION.errors<br>lambda.count_per_version.VERSION.dead_letter_errors<br>lambda.count_per_version.VERSION.throttles|integer|Sum|
|Duration [ms] per version|Duration|lambda.duration_per_version.VERSION.average<br>lambda.duration_per_version.VERSION.max<br>lambda.duration_per_version.VERSION.min|float|Average<br>Maximum<br>Minimum|
|Iterator Age [ms] per version|IteratorAge|lambda.iterator_age_per_version.VERSION.average<br>lambda.iterator_age_per_version.VERSION.max<br>lambda.iterator_age_per_version.VERSION.min|float|Average<br>Maximum<br>Minimum|
