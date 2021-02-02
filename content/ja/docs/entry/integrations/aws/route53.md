---
Title: AWSインテグレーション - Route 53
Date: 2021-01-25T10:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/route53
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613686235471
CustomPath: integrations/aws/route53
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/route53/" target="_blank">Amazon Route 53</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1アカウント = 1マイクロホストと換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのRoute 53対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関してはAWSのヘルプをご確認ください。

<a href="https://docs.aws.amazon.com/ja_jp/Route53/latest/DeveloperGuide/monitoring-hosted-zones-with-cloudwatch.html" target="_blank">Amazon CloudWatchを使用したホストゾーンのモニタリング</a><br>
<a href="https://docs.aws.amazon.com/ja_jp/Route53/latest/DeveloperGuide/monitoring-cloudwatch.html" target="_blank">Amazon Route 53 のヘルスチェックと Amazon CloudWatch によるリソースのモニタリング</a>

最大で `1 × (ホストゾーン数) + 6 × (ヘルスチェック数)` のメトリックが取得されます。

メトリック名の`HOSTED_ZONE_ID`にはホストゾーンのIDが、`HEALTH_CHECK_ID`にはヘルスチェックのIDが入ります。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|DNS Queries|DNSQueries|route53.dns_queries.HOSTED_ZONE_ID|integer|Sum|
|Health Check Percentage Healthy|HealthCheckPercentageHealthy|route53.health_check_percentage_healthy.HEALTH_CHECK_ID|float|Average|
|Health Check Status|HealthCheckStatus|route53.health_check_status.HEALTH_CHECK_ID|integer|Minimum|
|SSL Handshake Time|SSLHandshakeTime|route53.ssl_handshake_time.HEALTH_CHECK_ID|float|Average|
|Time To First Byte|TimeToFirstByte|route53.time_to_first_byte.HEALTH_CHECK_ID|float|Average|
|Child Health Check Healthy Count|ChildHealthCheckHealthyCount|route53.child_health_check_healthy_count.HEALTH_CHECK_ID|float|Average|
|Connection Time|ConnectionTime|route53.connection_time.HEALTH_CHECK_ID|float|Average|
