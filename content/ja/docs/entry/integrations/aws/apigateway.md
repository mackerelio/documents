---
Title: AWSインテグレーション - API Gateway
Date: 2018-10-18T18:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/apigateway
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10257846132664955420
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/api-gateway/" target="_blank">Amazon API Gateway</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1API = 1ホストと換算します。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのAPI Gateway対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/apigateway/latest/developerguide/api-gateway-metrics-and-dimensions.html" target="_blank">AWSのヘルプ</a>をご確認ください。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|Requests|Count|apigateway.requests.count|integer|Sum|
|Errors|4XXError<br>5XXError|apigateway.errors.4xx_error<br>apigateway.errors.5xx_error|integer|Sum|
|Cache|CacheHitCount<br>CacheMissCount|apigateway.cache.cache_hit_count<br>apigateway.cache.cache_miss_count|integer|Sum|
|Latency|Latency<br>IntegrationLatency|apigateway.latency.#.minimum<br>apigateway.latency.#.average<br>apigateway.latency.#.maximum|float|Minimum<br>Average<br>Maximum|

- "Mackerel上のメトリック名"の#には、"Latency"、"IntegrationLatency"のいずれかが入ります。

<h2 id="notes">注意事項</h2>

タグで絞り込みを行う場合、指定したタグが付与されたステージを含むAPIが対象となります。
