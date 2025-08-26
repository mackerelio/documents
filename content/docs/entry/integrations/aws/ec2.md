---
Title: AWS Integration - EC2
Date: 2017-01-11T16:54:02+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/ec2
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10328749687205738372
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/ec2/" target="_blank">Amazon Elastic Compute Cloud (Amazon EC2)</a> metrics in AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 Instance = 1 Standard Host. AWS integration and mackerel-agent can also be used together. In such a case, system metrics and custom metrics will be integrated as one host (no duplicate charges will be made).

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services.  <br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

## Obtaining metrics

The metrics obtainable with AWS Integration’s EC2 support are as follows. For `Metric` explanations, refer to the <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/viewing_metrics_with_cloudwatch.html" target="_blank">AWS help page</a>.

The maximum number of metrics obtainable is 21.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|CPU|CPUUtilization|ec2.cpu.used|percentage|Average|
|CPU Credit|CPUCreditUsage<br>CPUCreditBalance<br>CPUSurplusCreditBalance<br>CPUSurplusCreditsCharged|ec2.cpu_credit.used<br>ec2.cpu_credit.balance<br>ec2.cpu_credit.surplus_balance<br>ec2.cpu_credit.surplus_charged|float|Average|
|Disk OPS|DiskReadOps<br>DiskWriteOps|ec2.disk_ops.read<br>ec2.disk_ops.write|float|Average|
|Disk IO|DiskReadBytes<br>DiskWriteBytes|ec2.disk_io.read<br>ec2.disk_io.write|bytes|Average|
|Network Traffic|NetworkIn<br>NetworkOut|ec2.network.in<br>ec2.network.out|bytes|Average|
|Network Packets|NetworkPacketsIn<br>NetworkPacketsOut|ec2.network_packets.in<br>ec2.network_packets.out|float|Average|
|Status Check Failed|StatusCheckFailed_Instance<br>StatusCheckFailed_System<br>StatusCheckFailed|ec2.status_check_failed.instance<br>ec2.status_check_failed.system<br>ec2.status_check_failed.total|float|Average|
|EBS Operation|EBSReadOps [*1](#ec2-nitro)<br>EBSWriteOps [*1](#ec2-nitro)|ec2.ebs_operation.read<br>ec2.ebs_operation.write|integer|Sum|
|EBS Bytes Used|EBSReadBytes [*1](#ec2-nitro)<br>EBSWriteBytes [*1](#ec2-nitro)|ec2.ebs_bytes_used.read<br>ec2.ebs_bytes_used.write|bytes|Sum|
|EBS Burst Bucket Balance|EBSIOBalance% [*1](#ec2-nitro)<br>EBSByteBalance% [*1](#ec2-nitro)|ec2.ebs_burst_bucket_balance.io<br>ec2.ebs_burst_bucket_balance.throughput|percentage|Average|

ediv id="ec2-nitro">*1 Occurs with Nitro-based instances. Click <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances">here</a> for a list of instances.

<h3 id="notes">Precautions</h2>

Among the graphs/metrics obtainable with AWS Integration, metrics included in the following graph are usually obtained in 5 minute intervals.

* CPU
* CPU Credit
* Disk OPS
* Disk IO
* Network Traffic
* Network Packets
* EBS Operation
* EBS Bytes Used
* EBS Burst Bucket Balance

By enabling <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/manage-detailed-monitoring.html">detailed monitoring</a>, the metrics in the graph below will be obtained in 1-minute intervals.

* CPU
* Disk OPS
* Disk IO
* Network Traffic
* EBS Operation
* EBS Bytes Used

This is specified by the AWS CloudWatch API.

## Using with mackerel-agent 

If mackerel-agent has already been installed on the target EC2 instance, host information from Mackerel will automatically be integrated and registered as one host. It will not be counted twice as a billable host.

If you prefer a more detailed style of monitoring with AWS Integration, we recommend installing mackerel-agent for EC2.

