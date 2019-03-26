---
Title: AWSインテグレーション - CloudFront
Date: 2018-10-04T18:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/cloudfront
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10257846132646137877
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/cloudfront/" target="_blank">Amazon CloudFront</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1ディストリビューション = 1ホストと換算します。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのCloudFront対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/monitoring-using-cloudwatch.html" target="_blank">AWSのヘルプ</a>をご確認ください。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|Requests|Requests|cloudfront.requests.request|integer|Sum|
|Transfer|BytesDownloaded<br>BytesUploaded|cloudfront.transfer.download<br>cloudfront.transfer.upload|bytes|Sum|
|ErrorRate|4xxErrorRate<br>5xxErrorRate|cloudfront.error_rate.4xx_error_rate<br>cloudfront.error_rate.5xx_error_rate|float|Average|

<h2 id="notes">注意事項</h2>
CloudFrontはグローバルなサービスである為、どのリージョンを選択していてもCloudFrontと連携できます。
