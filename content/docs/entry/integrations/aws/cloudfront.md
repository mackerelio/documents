---
Title: AWS Integration - CloudFront
Date: 2018-10-04T18:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/cloudfront
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10257846132650121985
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/jp/cloudfront/" target="_blank">Amazon CloudFront</a> metrics in AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 Distribution = 1 Host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services. <br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

## Obtaining metrics
The metrics obtainable with AWS Integration's CloudFront support are as follows. For `Metric` explanations, refer to the <a href="https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/monitoring-using-cloudwatch.html" target="_blank">AWS help page</a>.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:--|:--|:--|:--|:--|
|Requests|Requests|cloudfront.requests.request|integer|Sum|
|Transfer|BytesDownloaded<br>BytesUploaded|cloudfront.transfer.download<br>cloudfront.transfer.upload|bytes|Sum|
|ErrorRate|4xxErrorRate<br>5xxErrorRate|cloudfront.error_rate.4xx_error_rate<br>cloudfront.error_rate.5xx_error_rate|float|Average|

<h2 id="notes">Precautions</h2>
Since CloudFront is a global service, integration with CloudFront is possible regardless of the region selected.
