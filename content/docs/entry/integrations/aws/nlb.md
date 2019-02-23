---
Title: AWS Integration - NLB
Date: 2017-10-23T18:07:40+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/nlb
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812310631927
---

Mackerel supports obtaining and monitoring each Load Balancer metric of <a href="https://aws.amazon.com/elasticloadbalancing/" target="_blank">Elastic Load Balancing</a> in AWS Integration. This page contains details about the Network Load Balancer (NLB). When integrating with AWS Integration, billable targets are determined using the conversion 1 NLB = 1 Host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services. <br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

In addition, refer to our other help pages regarding Elastic Load Balancing; [ALB](https://mackerel.io/docs/entry/integrations/aws/alb) and [ELB (CLB)](https://mackerel.io/docs/entry/integrations/aws/elb).

## Obtaining metrics
The metrics obtainable with AWS Integration’s NLB support are as follows. For `Metric` explanations, refer to the <a href="https://docs.aws.amazon.com/elasticloadbalancing/latest/network/load-balancer-cloudwatch-metrics.html" target="_blank">AWS help page</a>.

### Graph per Load Balancer
|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|Processed Bytes|ProcessedBytes|nlb.bytes.processed|bytes|Sum|
|TCP Flow|ActiveFlowCount<br>NewFlowCount|nlb.flowcount.active<br>nlb.flowcount.new|integer|Average<br>Sum|
|TCP Reset Packets|TCP_Client_Reset_Count<br>TCP_Target_Reset_Count<br>TCP_ELB_Reset_Count|nlb.tcp_reset.client_count<br>nlb.tcp_reset.target_count<br>nlb.tcp_reset.elb_count|integer|Sum|

### Graph per Target Group
There can be multiple target groups per load balancer in Application Load Balancer and Network Load Balancer. The following metrics can be obtained for each target group in NLB. The metric name’s `TARGET_GROUP_NAME ` will contain the name of the target group.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|Host Count|HealthyHostCount<br>UnHealthyHostCount|nlb.host_count.TARGET_GROUP_NAME.healthy<br>nlb.host_count.TARGET_GROUP_NAME.unhealthy|integer|Average|
