---
Title: AWSインテグレーション - API Gateway
Date: 2018-10-18T18:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/apigateway
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10257846132664955420
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/api-gateway/" target="_blank">Amazon API Gateway</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1API = 1マイクロホストと換算します。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのAPI Gateway対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関してはAWSのヘルプをご確認ください。

- <a href="https://docs.aws.amazon.com/ja_jp/apigateway/latest/developerguide/api-gateway-metrics-and-dimensions.html" target="_blank">Amazon API Gatewayのディメンションおよびメトリクス (REST)</a>
- <a href="https://docs.aws.amazon.com/ja_jp/apigateway/latest/developerguide/apigateway-websocket-api-logging.html" target="_blank">CloudWatchを使用したWebSocket API実行のモニタリング</a>
- <a href="https://docs.aws.amazon.com/ja_jp/apigateway/latest/developerguide/http-api-metrics.html" target="_blank">HTTP APIのメトリクスの使用</a>

### REST

最大で11個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|Requests|Count|apigateway.requests.count|integer|Sum|
|Errors|4XXError<br>5XXError|apigateway.errors.4xx_error<br>apigateway.errors.5xx_error|integer|Sum|
|Cache|CacheHitCount<br>CacheMissCount|apigateway.cache.cache_hit_count<br>apigateway.cache.cache_miss_count|integer|Sum|
|Latency|Latency<br>IntegrationLatency|apigateway.latency.#.minimum<br>apigateway.latency.#.average<br>apigateway.latency.#.maximum|float|Minimum<br>Average<br>Maximum|

- "Mackerel上のメトリック名"の#には、"Latency"、"IntegrationLatency"のいずれかが入ります。

### WebSocket

最大で8個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|WebSocket Connect|ConnectCount|apigateway.websocket_connect.count|integer|Sum|
|WebSocket Message|MessageCount|apigateway.websocket_message.count|integer|Sum|
|WebSocket Errors|IntegrationError<br>ClientError<br>ExecutionError|apigateway.websocket_errors.integration<br>apigateway.websocket_errors.client<br>apigateway.websocket_errors.execution|integer|Sum|
|WebSocket Latency|IntegrationLatency|apigateway.websocket_latency.minimum<br>apigateway.websocket_latency.average<br>apigateway.websocket_latency.maximum|float|Minimum<br>Average<br>Maximum|

### HTTP

最大で10個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|Requests|Count|apigateway.requests.count|integer|Sum|
|Data Processed|DataProcessed|apigateway.data_processed|bytes|Sum|
|Errors|4xx<br>5xx|apigateway.http_errors.4xx_error<br>apigateway.http_errors.5xx_error|integer|Sum|
|Latency|Latency<br>IntegrationLatency|apigateway.latency.#.minimum<br>apigateway.latency.#.average<br>apigateway.latency.#.maximum|float|Minimum<br>Average<br>Maximum|

- "Mackerel上のメトリック名"の#には、"Latency"、"IntegrationLatency"のいずれかが入ります。
