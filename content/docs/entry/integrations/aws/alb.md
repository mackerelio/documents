---
Title: AWS Integration - ALB
Date: 2017-01-11T16:00:50+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/alb
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10328749687205720315
---

Mackerel supports obtaining and monitoring each Load Balancer metric of <a href="https://aws.amazon.com/elasticloadbalancing/" target="_blank">Elastic Load Balancing</a> in AWS Integration. This page contains details regarding the Application Load Balancer (ALB). When integrating with AWS Integration, billable targets are determined using the conversion 1 ALB = 1 Micro Host.
In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services. <br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

In addition, you can take a look at the help pages for other supported Load Balancers; [NLB](https://mackerel.io/docs/entry/integrations/aws/nlb) and [ELB (CLB)](https://mackerel.io/docs/entry/integrations/aws/elb).

## Obtaining metrics

The metrics obtainable with AWS Integration’s ALB support are as follows. For `Metric` explanations, refer to the <a href="https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-cloudwatch-metrics.html" target="_blank">AWS help page</a>.

The maximum number of metrics obtainable is `18 + 13 × (number of target groups)`.

### Graph per Load Balancer
|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|Request Count|RequestCount|alb.request.count|integer|Sum|
|Processed Bytes|ProcessedBytes|alb.bytes.processed|bytes|Sum|
|HTTP Code Count|HTTPCode_Target_2XX_Count<br>HTTPCode_Target_3XX_Count<br>HTTPCode_Target_4XX_Count<br>HTTPCode_Target_5XX_Count<br>HTTPCode_ELB_4XX_Count<br>HTTPCode_ELB_5XX_Count|alb.httpcode_count.target_2xx<br>alb.httpcode_count.target_3xx<br>alb.httpcode_count.target_4xx<br>alb.httpcode_count.target_5xx<br>alb.httpcode_count.alb_4xx<br>alb.httpcode_count.alb_5xx|integer|Sum|
|Concurrent Connection Count|ActiveConnectionCount|alb.concurrent_connection_count.active|integer|Sum|
|Connection Count|NewConnectionCount<br>RejectedConnectionCount|alb.connection_count.new<br>alb.connection_count.rejected|Integer|Sum|
|Target Response Time|TargetResponseTime|alb.response.time<br>alb.response.time_p90<br>alb.response.time_p95<br>alb.response.time_p99|float|Average<br>p90<br>p95<br>p99|
|TLS Negotiation Error Count|ClientTLSNegotiationErrorCount<br>TargetTLSNegotiationErrorCount|alb.tls_negotiation_error_count.client<br>alb.tls_negotiation_error_count.target|integer|Sum|
|Target Connection Error Count|TargetConnectionErrorCount|alb.connection_error_count.target|integer|Sum|

### Graph per Target Group

Since there can be multiple target groups per load balancer in Application Load Balancer, each metric is grouped as follows. The metric name’s `TARGET_GROUP_NAME` will contain the name of the target group.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|Request Count per Target Group|RequestCount|alb.request_per_group.TARGET_GROUP_NAME.count|integer|Sum|
|Request Average per Target|RequestCountPerTarget|alb.request_average_per_target.TARGET_GROUP_NAME.average|float|Sum|
|HTTP Code Count per Target Group|HTTPCode_Target_2XX_Count<br>HTTPCode_Target_3XX_Count<br>HTTPCode_Target_4XX_Count<br>HTTPCode_Target_5XX_Count|alb.httpcode_count_per_group.TARGET_GROUP_NAME.target_2xx<br>alb.httpcode_count_per_group.TARGET_GROUP_NAME.target_3xx<br>alb.httpcode_count_per_group.TARGET_GROUP_NAME.target_4xx<br>alb.httpcode_count_per_group.TARGET_GROUP_NAME.target_5xx|integer|Sum|
|Target Response Time per Target Group|TargetResponseTime|alb.response_per_group.TARGET_GROUP_NAME.time<br>alb.response_per_group.TARGET_GROUP_NAME.time_p90<br>alb.response_per_group.TARGET_GROUP_NAME.time_p95<br>alb.response_per_group.TARGET_GROUP_NAME.time_p99|float|Average<br>p90<br>p95<br>p99|
|TLS Negotiation Error Count per Target Group|TargetTLSNegotiationErrorCount|alb.tls_negotiation_error_count_per_group.TARGET_GROUP_NAME.count|integer|Sum|
|Host Count|HealthyHostCount<br>UnHealthyHostCount|alb.host_count.TARGET_GROUP_NAME.healthy<br>alb.host_count.TARGET_GROUP_NAME.unhealthy|integer|Average|
