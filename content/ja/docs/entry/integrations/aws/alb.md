---
Title: AWSインテグレーション - ALB
Date: 2016-12-28T12:06:02+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/alb
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10328749687201704692
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/elasticloadbalancing/" target="_blank">Elastic Load Balancing</a>の各ロードバランサのメトリック取得や監視に対応しています。このページではそのうちApplication Load Balancer(以下ALB)についての詳細です。
AWSインテグレーションで連携をおこなった場合、課金対象として 1ALB = 1マイクロホスト と換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

また、Elastic Load Balancingの他のロードバランサについては、[NLB](https://mackerel.io/ja/docs/entry/integrations/aws/nlb) と [ELB (CLB)](https://mackerel.io/ja/docs/entry/integrations/aws/elb)のそれぞれのページをご確認下さい。

## 取得メトリック
AWSインテグレーションのALB対応で取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/application/load-balancer-cloudwatch-metrics.html" target="_blank">AWSのヘルプ</a>をご確認ください。

最大で `18 + 13 × (ターゲットグループ数)` 個のメトリックが取得されます。

### ロードバランサーごとのグラフ
|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:---|:---|:---|:---|:---|
|Request Count|RequestCount|alb.request.count|integer|Sum|
|Processed Bytes|ProcessedBytes|alb.bytes.processed|bytes|Sum|
|HTTP Code Count|HTTPCode_Target_2XX_Count<br>HTTPCode_Target_3XX_Count<br>HTTPCode_Target_4XX_Count<br>HTTPCode_Target_5XX_Count<br>HTTPCode_ELB_4XX_Count<br>HTTPCode_ELB_5XX_Count|alb.httpcode_count.target_2xx<br>alb.httpcode_count.target_3xx<br>alb.httpcode_count.target_4xx<br>alb.httpcode_count.target_5xx<br>alb.httpcode_count.alb_4xx<br>alb.httpcode_count.alb_5xx|integer|Sum|
|Concurrent Connection Count|ActiveConnectionCount|alb.concurrent_connection_count.active|integer|Sum|
|Connection Count|NewConnectionCount<br>RejectedConnectionCount|alb.connection_count.new<br>alb.connection_count.rejected|Integer|Sum|
|Target Response Time|TargetResponseTime|alb.response.time<br>alb.response.time_p90<br>alb.response.time_p95<br>alb.response.time_p99|float|Average<br>p90<br>p95<br>p99|
|TLS Negotiation Error Count|ClientTLSNegotiationErrorCount<br>TargetTLSNegotiationErrorCount|alb.tls_negotiation_error_count.client<br>alb.tls_negotiation_error_count.target|integer|Sum|
|Target Connection Error Count|TargetConnectionErrorCount|alb.connection_error_count.target|integer|Sum|

### ターゲットグループごとのグラフ
Application Load Balancerではロードバランサーごとに複数のターゲットグループを持つことができるので、それぞれのメトリックは以下のようにグルーピングされます。メトリック名の `TARGET_GROUP_NAME` にターゲットグループの名前が入ります。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:---|:---|:---|:---|:---|
|Request Count per Target Group|RequestCount|alb.request_per_group.TARGET_GROUP_NAME.count|integer|Sum|
|Request Average per Target|RequestCountPerTarget|alb.request_average_per_target.TARGET_GROUP_NAME.average|float|Sum|
|HTTP Code Count per Target Group|HTTPCode_Target_2XX_Count<br>HTTPCode_Target_3XX_Count<br>HTTPCode_Target_4XX_Count<br>HTTPCode_Target_5XX_Count|alb.httpcode_count_per_group.TARGET_GROUP_NAME.target_2xx<br>alb.httpcode_count_per_group.TARGET_GROUP_NAME.target_3xx<br>alb.httpcode_count_per_group.TARGET_GROUP_NAME.target_4xx<br>alb.httpcode_count_per_group.TARGET_GROUP_NAME.target_5xx|integer|Sum|
|Target Response Time per Target Group|TargetResponseTime|alb.response_per_group.TARGET_GROUP_NAME.time<br>alb.response_per_group.TARGET_GROUP_NAME.time_p90<br>alb.response_per_group.TARGET_GROUP_NAME.time_p95<br>alb.response_per_group.TARGET_GROUP_NAME.time_p99|float|Average<br>p90<br>p95<br>p99|
|TLS Negotiation Error Count per Target Group|TargetTLSNegotiationErrorCount|alb.tls_negotiation_error_count_per_group.TARGET_GROUP_NAME.count|integer|Sum|
|Host Count|HealthyHostCount<br>UnHealthyHostCount|alb.host_count.TARGET_GROUP_NAME.healthy<br>alb.host_count.TARGET_GROUP_NAME.unhealthy|float|Average|
