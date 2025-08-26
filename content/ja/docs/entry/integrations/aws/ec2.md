---
Title: AWSインテグレーション - EC2
Date: 2016-12-28T10:54:48+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/ec2
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10328749687201689861
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/ec2/" target="_blank">Amazon Elastic Compute Cloud (Amazon EC2)</a>のメトリック取得や監視に対応しています。
AWSインテグレーションで連携をおこなった場合、課金対象として 1インスタンス = 1スタンダードホスト と換算します。
AWSインテグレーションとmackerel-agentの併用も可能です。その場合、システムメトリックとカスタムメトリックはひとつのホストに統合されます（重複課金はされません）。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのEC2対応で取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/viewing_metrics_with_cloudwatch.html" target="_blank">AWSのヘルプ</a>をご確認ください。

最大で21個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
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

<div id="ec2-nitro">*1 Nitroベースのインスタンスで発生します。対象は<a href="https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances">こちら</a>をご確認ください。

<h3 id="notes">注意事項</h2>

AWSインテグレーションにより取得可能な上記のグラフ・メトリックのうち、下記のグラフに含まれるメトリックについては、通常、5分間隔粒度でのメトリックが取得されます。

* CPU
* CPU Credit
* Disk OPS
* Disk IO
* Network Traffic
* Network Packets
* EBS Operation
* EBS Bytes Used
* EBS Burst Bucket Balance

<a href="https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/manage-detailed-monitoring.html">詳細モニタリング</a>を有効化することで、下記のグラフに含まれるメトリックについては1分間隔粒度でのメトリックが取得されます。

* CPU
* Disk OPS
* Disk IO
* Network Traffic
* EBS Operation
* EBS Bytes Used

これは、AWS CloudWatch API の仕様です。

## mackerel-agent との併用について

インテグレーションの対象となっているEC2インスタンスにmackerel-agentが導入されている場合、Mackerel上でホスト情報は自動的に統合され、ひとつのホストとして登録されます。課金対象ホストとして重複カウントされることはありません。

EC2の場合、AWSインテグレーションで簡易的な監視をおこない、より詳細な監視をおこないたい場合に、mackerel-agentを導入するのがおすすめです。
