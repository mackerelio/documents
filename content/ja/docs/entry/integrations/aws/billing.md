---
Title: AWSインテグレーション - Billing
Date: 2020-11-02T10:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/billing
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613651788338
CustomPath: integrations/aws/billing
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/aws-cost-management/" target="_blank">AWS Billing</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1マネジメントアカウント = 1マイクロホストと換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

<b><u>概算請求額のメトリックを連携するには、請求アラートを有効にしておく必要があります。設定方法についてはこちらのページをご確認ください。</u></b><br>
<a href="https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/monitor_estimated_charges_with_cloudwatch.html#turning_on_billing_metrics">請求アラートを有効にする</a>

## 取得メトリック
AWSインテグレーションのBilling対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/monitor_estimated_charges_with_cloudwatch.html" target="_blank">AWSのヘルプ</a>をご確認ください。

最大で `1 + 1 × (メンバーアカウント数) + 3 × 予算数` のメトリックが取得されます。

メトリック名の`ACCOUNT_ID`にはメンバーアカウントのIDが入ります。(例: 217452466226 など)

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:---|:---|:---|:---|:---|
|Estimated Charges|EstimatedCharges|billing.estimated_charges.total|float|Maximum|
|Estimated Charges By Account|EstimatedCharges|billing.estimated_charges_by_account.ACCOUNT_ID|float|Maximum|
|Budgets|-|billing.budgets.#.actual<br>billing.budgets.#.forecasted<br>billing.budgets.#.limit|float|-|

- "Mackerel上のメトリック名"の#には、予算名が入ります。予算名にメトリック名として使用不可能な文字が含まれる場合、その文字はエンコードされます。

<h2 id="notes">注意事項</h2>
- AWSインテグレーションのBilling対応ではタグによるサービス・ロール割り当てに対応していません。
- 概算請求額は1日に数回、予算は1日に1回以上更新されます。これはAWSの仕様です。
