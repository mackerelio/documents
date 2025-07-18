---
Title: AWSインテグレーション - CLB (ELB)
Date: 2016-12-28T11:38:03+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/elb
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10328749687201699111
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/elasticloadbalancing/" target="_blank">Elastic Load Balancing</a>の各ロードバランサのメトリック取得や監視に対応しています。このページではそのうちClassic Load Balancer(以下CLB。旧名はElastic Load Balancer(ELB))についての詳細です。
AWSインテグレーションで連携をおこなった場合、課金対象として 1ELB(CLB) = 1マイクロホスト と換算します。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

また、Elastic Load Balancingの他のロードバランサについては、[ALB](https://mackerel.io/ja/docs/entry/integrations/aws/alb) と [NLB](https://mackerel.io/ja/docs/entry/integrations/aws/nlb)のそれぞれのページをご確認下さい。

## 取得メトリック
AWSインテグレーションのELB(CLB)対応で取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/classic/elb-cloudwatch-metrics.html" target="_blank">AWSのヘルプ</a>をご確認ください。

最大で13個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:---|:---|:---|:---|
|Request Count|RequestCount|elb.count.request_count|integer|Sum|
|Host Count|HealthyHostCount<br>UnHealthyHostCount|elb.host_count.healthy<br>elb.host_count.unhealthy|float|Average|
|Latency|Latency|elb.latency.latency|float|Average|
|HTTP Code ELB Count|HTTPCode_ELB_4XX<br>HTTPCode_ELB_5XX|elb.httpcode_elb.4xx<br>elb.httpcode_elb.5xx|integer|Sum|
|HTTP Code Backend Count|HTTPCode_Backend_2XX<br>HTTPCode_Backend_3XX<br>HTTPCode_Backend_4XX<br>HTTPCode_Backend_5XX|elb.httpcode_backend.2xx<br>elb.httpcode_backend.3xx<br>elb.httpcode_backend.4xx<br>elb.httpcode_backend.5xx|integer|Sum|
|Backend Connection Errors|BackendConnectionErrors|elb.errors.backend_connection_errors|integer|Sum|
|Surge Queue Length|SurgeQueueLength<br>SpilloverCount|elb.surge_queue.length<br>elb.surge_queue.spillover|integer|Maximum<br>Sum|
