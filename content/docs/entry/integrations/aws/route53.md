---
Title: AWS Integration - Route 53
Date: 2021-01-25T10:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/route53
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613686235326
CustomPath: integrations/aws/route53
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/route53/" target="_blank">Amazon Route 53</a> metrics in AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 account = 1 Micro Host. In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services.<br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

## Obtaining metrics
The metrics obtainable with AWS Integration's Route 53 support are as follows. For `Metric` explanations, refer to the following AWS help pages.

<a href="https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/monitoring-hosted-zones-with-cloudwatch.html" target="_blank">Monitoring hosted zones using Amazon CloudWatch</a><br>
<a href="https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/monitoring-cloudwatch.html" target="_blank">Monitoring your resources with Amazon Route 53 health checks and Amazon CloudWatch</a>

The maximum number of metrics obtainable can be calculated using the formula `1 × (number of hosted zones) + 6 × (number of health checks)`.

`HOSTED_ZONE_ID` / `HEALTH_CHECK_ID` in the metric name are placeholders for the hosted zone ID / health check ID.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:--|:--|:--|:--|:--|
|DNS Queries|DNSQueries|route53.dns_queries.HOSTED_ZONE_ID|integer|Sum|
|Health Check Percentage Healthy|HealthCheckPercentageHealthy|route53.health_check_percentage_healthy.HEALTH_CHECK_ID|float|Average|
|Health Check Status|HealthCheckStatus|route53.health_check_status.HEALTH_CHECK_ID|integer|Minimum|
|SSL Handshake Time|SSLHandshakeTime|route53.ssl_handshake_time.HEALTH_CHECK_ID|float|Average|
|Time To First Byte|TimeToFirstByte|route53.time_to_first_byte.HEALTH_CHECK_ID|float|Average|
|Child Health Check Healthy Count|ChildHealthCheckHealthyCount|route53.child_health_check_healthy_count.HEALTH_CHECK_ID|float|Average|
|Connection Time|ConnectionTime|route53.connection_time.HEALTH_CHECK_ID|float|Average|
