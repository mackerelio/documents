---
Title: AWSインテグレーション - CloudFront
Date: 2018-10-04T18:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/cloudfront
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10257846132646137877
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/cloudfront/" target="_blank">Amazon CloudFront</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1ディストリビューション = 1マイクロホストと換算します。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック

AWSインテグレーションのCloudFront対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関してはAWSのヘルプをご確認ください。<br>
<a href="https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/monitoring-using-cloudwatch.html" target="_blank">Amazon CloudWatch による CloudFront のモニタリング</a><br>
<a href="https://docs.amazonaws.cn/en_us/AmazonCloudFront/latest/DeveloperGuide/monitoring-functions.html" target="_blank">Monitoring CloudFront Functions</a>

最大で `14 + 6 × (Distributionに設定されているFunction数)` 個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|Requests|Requests|cloudfront.requests.request|integer|Sum|
|Transfer|BytesDownloaded<br>BytesUploaded|cloudfront.transfer.download<br>cloudfront.transfer.upload|bytes|Sum|
|ErrorRate|TotalErrorRate<br>4xxErrorRate<br>5xxErrorRate<br>401ErrorRate [*](#additional-metric-notes)<br>403ErrorRate [*](#additional-metric-notes)<br>404ErrorRate [*](#additional-metric-notes)<br>502ErrorRate [*](#additional-metric-notes)<br>503ErrorRate [*](#additional-metric-notes)<br>504ErrorRate [*](#additional-metric-notes)|cloudfront.error_rate.total_error_rate<br>cloudfront.error_rate.4xx_error_rate<br>cloudfront.error_rate.5xx_error_rate<br>cloudfront.error_rate.401_error_rate<br>cloudfront.error_rate.403_error_rate<br>cloudfront.error_rate.404_error_rate<br>cloudfront.error_rate.502_error_rate<br>cloudfront.error_rate.503_error_rate<br>cloudfront.error_rate.504_error_rate|float|Average|
|CacheHitRate|CacheHitRate [*](#additional-metric-notes)|cloudfront.cache_hit_rate|float|Average|
|OriginLatency|OriginLatency [*](#additional-metric-notes)|cloudfront.origin_latency|float|Minimum<br>Average<br>Maximum|

<h4 id="additional-metric-notes">* 追加のメトリック</h4>
これらのメトリックを取得するには、<a href="https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/viewing-cloudfront-metrics.html#monitoring-console.distributions-additional" target="_blank">追加のメトリックを有効にする</a> 必要があります。

### CloudFront Functionsメトリック
|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|Function Invocations|FunctionInvocations|cloudfront.function_invocations.FUNCTION_NAME|integer|Sum|
|Function Validation Errors|FunctionValidationErrors|cloudfront.function_validation_errors.FUNCTION_NAME|integer|Sum|
|Function Execution Errors|FunctionExecutionErrors|cloudfront.function_execution_errors.FUNCTION_NAME|integer|Sum|
|Function Compute Utilization|FunctionComputeUtilization|cloudfront.function_compute_utilization.FUNCTION_NAME.minimum<br>cloudfront.function_compute_utilization.FUNCTION_NAME.average<br>cloudfront.function_compute_utilization.FUNCTION_NAME.maximum|percentage|Minimum<br>Average<br>Maximum|

- "Mackerel上のメトリック名"の以下の項目は次の通り設定されます。
    - `FUNCTION_NAME`：CloudFront Functionsの関数名

<h2 id="notes">注意事項</h2>

CloudFrontはグローバルなサービスである為、どのリージョンを選択していてもCloudFrontと連携できます。
