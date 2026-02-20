---
Title: AWSインテグレーション - Lambda
Date: 2017-02-24T10:48:27+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/lambda
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10328749687220619369
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/lambda/" target="_blank">AWS Lambda</a>のメトリック取得や監視に対応しています。
AWSインテグレーションで連携をおこなった場合、課金対象として 1function = 1マイクロホスト と換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## メトリックの取得に関する注意事項

* Lambdaのメトリックのタイムスタンプは、関数が呼び出された時刻になるため、Mackerelのグラフにおいても、関数の呼び出し時刻でメトリックが記録されます。
  * 参考：[Lambda 関数のメトリクスの表示 - AWS Lambda](https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/monitoring-metrics-view.html)

## 取得メトリック
AWSインテグレーションのLambda対応で取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/monitoring-functions-metrics.html" target="_blank">AWSのヘルプ</a>をご確認ください。

最大で `10 + 10 × (Alias数) + 10 × (Version数)` 個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:---|:---|:---|:---|:---|
|Count|Invocations<br>Errors<br>DeadLetterErrors<br>Throttles|lambda.count.invocations<br>lambda.count.errors<br>lambda.count.dead_letter_errors<br>lambda.count.throttles|integer|Sum|
|Duration [ms]|Duration|lambda.duration.average<br>lambda.duration.max<br>lambda.duration.min|float|Average<br>Maximum<br>Minimum|
|Iterator Age [ms]|IteratorAge|lambda.iterator_age.average<br>lambda.iterator_age.max<br>lambda.iterator_age.min|float|Average<br>Maximum<br>Minimum|

## Aliasごとのメトリック
メトリック名の `ALIAS` にAliasの名前が入ります。 `LATEST` バージョンのメトリックは `unqualified` エイリアスのメトリックとして分類されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:---|:---|:---|:---|:---|
|Count per alias|Invocations<br>Errors<br>DeadLetterErrors<br>Throttles|lambda.count_per_alias.ALIAS.invocations<br>lambda.count_per_alias.ALIAS.errors<br>lambda.count_per_alias.ALIAS.dead_letter_errors<br>lambda.count_per_alias.ALIAS.throttles|integer|Sum|
|Duration [ms] per alias|Duration|lambda.duration_per_alias.ALIAS.average<br>lambda.duration_per_alias.ALIAS.max<br>lambda.duration_per_alias.ALIAS.min|float|Average<br>Maximum<br>Minimum|
|Iterator Age [ms] per alias|IteratorAge|lambda.iterator_age_per_alias.ALIAS.average<br>lambda.iterator_age_per_alias.ALIAS.max<br>lambda.iterator_age_per_alias.ALIAS.min|float|Average<br>Maximum<br>Minimum|

## Versionごとのメトリック
メトリック名の `VERSION` にVersionの名前が入ります。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:---|:---|:---|:---|:---|
|Count per version|Invocations<br>Errors<br>DeadLetterErrors<br>Throttles|lambda.count_per_version.VERSION.invocations<br>lambda.count_per_version.VERSION.errors<br>lambda.count_per_version.VERSION.dead_letter_errors<br>lambda.count_per_version.VERSION.throttles|integer|Sum|
|Duration [ms] per version|Duration|lambda.duration_per_version.VERSION.average<br>lambda.duration_per_version.VERSION.max<br>lambda.duration_per_version.VERSION.min|float|Average<br>Maximum<br>Minimum|
|Iterator Age [ms] per version|IteratorAge|lambda.iterator_age_per_version.VERSION.average<br>lambda.iterator_age_per_version.VERSION.max<br>lambda.iterator_age_per_version.VERSION.min|float|Average<br>Maximum<br>Minimum|
