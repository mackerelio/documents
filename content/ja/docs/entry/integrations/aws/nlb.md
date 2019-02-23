---
Title: AWSインテグレーション - NLB
Date: 2017-10-13T18:10:21+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/nlb
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8599973812307563059
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/elasticloadbalancing/" target="_blank">Elastic Load Balancing</a>の各ロードバランサのメトリック取得や監視に対応しています。このページではそのうちNetwork Load Balancer(以下NLB)についての詳細です。
AWSインテグレーションで連携をおこなった場合、課金対象として 1NLB = 1ホスト と換算します。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

また、Elastic Load Balancingの他のロードバランサについては、[ALB](https://mackerel.io/ja/docs/entry/integrations/aws/alb) と [ELB (CLB)](https://mackerel.io/ja/docs/entry/integrations/aws/elb)のそれぞれのページをご確認下さい。

## 取得メトリック
AWSインテグレーションのNLB対応で取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/network/load-balancer-cloudwatch-metrics.html" target="_blank">AWSのヘルプ</a>をご確認ください。

### ロードバランサーごとのグラフ
|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:---|:---|:---|:---|:---|
|Processed Bytes|ProcessedBytes|nlb.bytes.processed|bytes|Sum|
|TCP Flow|ActiveFlowCount<br>NewFlowCount|nlb.flowcount.active<br>nlb.flowcount.new|integer|Average<br>Sum|
|TCP Reset Packets|TCP_Client_Reset_Count<br>TCP_Target_Reset_Count<br>TCP_ELB_Reset_Count|nlb.tcp_reset.client_count<br>nlb.tcp_reset.target_count<br>nlb.tcp_reset.elb_count|integer|Sum|

### ターゲットグループごとのグラフ
Application Load BalancerとNetwork Load Balancerではロードバランサーごとに複数のターゲットグループを持つことができます。NLBではそのターゲットグループごとに以下のメトリックを取得することができます。メトリック名の `TARGET_GROUP_NAME` にターゲットグループの名前が入ります。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:---|:---|:---|:---|:---|
|Host Count|HealthyHostCount<br>UnHealthyHostCount|nlb.host_count.TARGET_GROUP_NAME.healthy<br>nlb.host_count.TARGET_GROUP_NAME.unhealthy|integer|Average|
