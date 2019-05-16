---
Title: AWS Integration - API Gateway
Date: 2018-10-18T18:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/apigateway
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10257846132664956018
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/api-gateway/" target="_blank">Amazon API Gateway</a> metrics in AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 API = 1 Micro Host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services.<br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

## Obtaining metrics
The metrics obtainable with AWS Integration's API Gateway support are as follows. For `Metric` explanations, refer to the <a href="https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-metrics-and-dimensions.html" target="_blank">AWS help page</a>.

The maximum number of metrics obtainable is 11.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:--|:--|:--|:--|:--|
|Requests|Count|apigateway.requests.count|integer|Sum|
|Errors|4XXError<br>5XXError|apigateway.errors.4xx_error<br>apigateway.errors.5xx_error|integer|Sum|
|Cache|CacheHitCount<br>CacheMissCount|apigateway.cache.cache_hit_count<br>apigateway.cache.cache_miss_count|integer|Sum|
|Latency|Latency<br>IntegrationLatency|apigateway.latency.#.minimum<br>apigateway.latency.#.average<br>apigateway.latency.#.maximum|float|Minimum<br>Average<br>Maximum|

- Either "Latency" or "IntegrationLatency" goes in for the # of "Metric name in Mackerel".

<h2 id="notes">Precautions</h2>

When filtering with tags, APIs that contain the stage attached by the specified tag will be the target.
