---
Title: AWS Integration - ELB (CLB)
Date: 2017-01-11T18:20:48+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/elb
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10328749687205759344
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/elasticloadbalancing/" target="_blank">Classic Load Balancing</a> metrics in AWS Integration. This page contains details regarding the Classic Load Balancer (CLB, formerly Elastic Load Balancer (ELB)). When integrating with AWS Integration, billable targets are determined using the conversion 1 ELB(CLB) = 1 Micro Host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services.  <br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

In addition, you can take a look at the help pages for other supported Load Balancers; [ALB](https://mackerel.io/docs/entry/integrations/aws/alb) and [NLB](https://mackerel.io/docs/entry/integrations/aws/nlb).

## Obtaining metrics

The metrics obtainable with AWS Integration’s ELB(CLB) support are as follows. For `Metric` explanations, refer to the <a href="https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/elb-cloudwatch-metrics.html" target="_blank">AWS help page</a>.

The maximum number of metrics obtainable is 13.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|
|Request Count|RequestCount|elb.count.request_count|integer|Sum|
|Host Count|HealthyHostCount<br>UnHealthyHostCount|elb.host_count.healthy<br>elb.host_count.unhealthy|integer|Average|
|Latency|Latency|elb.latency.latency|float|Average|
|HTTP Code ELB Count|HTTPCode_ELB_4XX<br>HTTPCode_ELB_5XX|elb.httpcode_elb.4xx<br>elb.httpcode_elb.5xx|integer|Sum|
|HTTP Code Backend Count|HTTPCode_Backend_2XX<br>HTTPCode_Backend_3XX<br>HTTPCode_Backend_4XX<br>HTTPCode_Backend_5XX|elb.httpcode_backend.2xx<br>elb.httpcode_backend.3xx<br>elb.httpcode_backend.4xx<br>elb.httpcode_backend.5xx|integer|Sum|
|Backend Connection Errors|BackendConnectionErrors|elb.errors.backend_connection_errors|integer|Sum|
|Surge Queue Length|SurgeQueueLength<br>SpilloverCount|elb.surge_queue.length<br>elb.surge_queue.spillover|integer|Maximum<br>Sum|
