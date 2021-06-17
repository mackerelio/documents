---
Title: AWS Integration - CloudFront
Date: 2018-10-04T18:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/cloudfront
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10257846132650121985
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/jp/cloudfront/" target="_blank">Amazon CloudFront</a> metrics in AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 Distribution = 1 Micro Host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services. <br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

## Obtaining metrics

The metrics obtainable with AWS Integration's CloudFront support are as follows. For `Metric` explanations, refer to the AWS help page.<br>
<a href="https://docs.aws.amazon.com/en_us/AmazonCloudFront/latest/DeveloperGuide/monitoring-using-cloudwatch.html" target="_blank">Monitoring CloudFront with Amazon CloudWatch</a><br>
<a href="https://docs.amazonaws.cn/en_us/AmazonCloudFront/latest/DeveloperGuide/monitoring-functions.html" target="_blank">Monitoring CloudFront Functions</a>

The maximum number of metrics obtainable is `14 + 6 × (number of functions associated with distributions)`.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:--|:--|:--|:--|:--|
|Requests|Requests|cloudfront.requests.request|integer|Sum|
|Transfer|BytesDownloaded<br>BytesUploaded|cloudfront.transfer.download<br>cloudfront.transfer.upload|bytes|Sum|
|ErrorRate|TotalErrorRate<br>4xxErrorRate<br>5xxErrorRate<br>401ErrorRate [*](#additional-metric-notes)<br>403ErrorRate [*](#additional-metric-notes)<br>404ErrorRate [*](#additional-metric-notes)<br>502ErrorRate [*](#additional-metric-notes)<br>503ErrorRate [*](#additional-metric-notes)<br>504ErrorRate [*](#additional-metric-notes)|cloudfront.error_rate.total_error_rate<br>cloudfront.error_rate.4xx_error_rate<br>cloudfront.error_rate.5xx_error_rate<br>cloudfront.error_rate.401_error_rate<br>cloudfront.error_rate.403_error_rate<br>cloudfront.error_rate.404_error_rate<br>cloudfront.error_rate.502_error_rate<br>cloudfront.error_rate.503_error_rate<br>cloudfront.error_rate.504_error_rate|float|Average|
|CacheHitRate|CacheHitRate [*](#additional-metric-notes)|cloudfront.cache_hit_rate|float|Average|
|OriginLatency|OriginLatency [*](#additional-metric-notes)|cloudfront.origin_latency|float|Minimum<br>Average<br>Maximum|

<h4 id="additional-metric-notes">* Additional metrics</h4>
In order to obtain these metrics, <a href="https://docs.aws.amazon.com/en_us/AmazonCloudFront/latest/DeveloperGuide/viewing-cloudfront-metrics.html#monitoring-console.distributions-additional" target="_blank">additional metrics</a> must be enabled.

### CloudFront Functions metrics
|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:--|:--|:--|:--|:--|
|Function Invocations|FunctionInvocations|cloudfront.function_invocations.FUNCTION_NAME|integer|Sum|
|Function Validation Errors|FunctionValidationErrors|cloudfront.function_validation_errors.FUNCTION_NAME|integer|Sum|
|Function Execution Errors|FunctionExecutionErrors|cloudfront.function_execution_errors.FUNCTION_NAME|integer|Sum|
|Function Compute Utilization|FunctionComputeUtilization|cloudfront.function_compute_utilization.FUNCTION_NAME.minimum<br>cloudfront.function_compute_utilization.FUNCTION_NAME.average<br>cloudfront.function_compute_utilization.FUNCTION_NAME.maximum|percentage|Minimum<br>Average<br>Maximum|

- The following items in "Metric name in Mackerel" are set as follows.
    - `FUNCTION_NAME`: The function name of CloudFront Functions

<h2 id="notes">Precautions</h2>
Since CloudFront is a global service, integration with CloudFront is possible regardless of the region selected.
