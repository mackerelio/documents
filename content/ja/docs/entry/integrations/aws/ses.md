---
Title: AWSインテグレーション - SES
Date: 2019-04-08T18:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/ses
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17680117127020837213
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/ses/" target="_blank">Amazon Simple Email Service</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1リージョン = 1マイクロホストと換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのSES対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関してはAWSのヘルプをご確認ください。<br><a href="https://docs.aws.amazon.com/ja_jp/ses/latest/DeveloperGuide/monitor-sending-activity.html" target="_blank">Amazon SES 送信アクティビティのモニタリング</a><br><a href="https://docs.aws.amazon.com/ja_jp/ses/latest/DeveloperGuide/receiving-email-metrics.html" target="_blank">Amazon SES によるメール受信のメトリクスの表示</a>

最大で `10 + 2 × (ルール数) + 1 × (CloudWatchイベント送信先の設定数)` のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|Email Sending Events|Send<br>Delivery<br>Bounce<br>Complaint<br>Reject<br>Open<br>Click|ses.email_sending_events.send<br>ses.email_sending_events.delivery<br>ses.email_sending_events.bounce<br>ses.email_sending_events.complaint<br>ses.email_sending_events.reject<br>ses.email_sending_events.open<br>ses.email_sending_events.click|integer|Sum|
|Reputation|Reputation.BounceRate<br>Reputation.ComplaintRate|ses.reputation.bounce_rate<br>ses.reputation.complaint_rate|percentage|Average|
|Email Receiving Failure|PublishFailure<br>PublishExpired|ses.email_receiving_failure.RULE_NAME.failure<br>ses.email_receiving_failure.RULE_NAME.expired|integer|Sum|
|Sent Last 24 Hours|-|ses.sent_last_24_hours.sent|integer|-|
|Delivery Delay|DeliveryDelay|ses.delivery_delay.DIMENSION_NAME.DIMENSION_VALUE|integer|Sum|

- "Mackerel上のメトリック名"の以下の項目は次の通り設定されます。
    - `RULE_NAME`：SESのRuleName
    - `DIMENSION_NAME`：設定セットのCloudWatchイベント送信先で設定されているディメンションの名前
    - `DIMENSION_VALUE`：設定セットのCloudWatchイベント送信先で設定されているディメンションの値

<h2 id="notes">注意事項</h2>
- AWSインテグレーションのSES対応ではタグによるホストの絞り込みに対応していません。
- AWSインテグレーションのSES対応ではタグによるサービス・ロール割り当てに対応していません。
- "Email Sending Events"の"Open", "Click"は<a href="https://docs.aws.amazon.com/ja_jp/ses/latest/DeveloperGuide/using-configuration-sets.html" target="_blank">設定セット</a>を使用し、オープン、クリックイベントをキャプチャした場合に発生します。詳細は<a href="https://docs.aws.amazon.com/ja_jp/ses/latest/DeveloperGuide/configure-custom-open-click-domains.html" target="_blank">AWSのヘルプ</a>をご確認ください。
- "Delivery Delay"は<a href="https://docs.aws.amazon.com/ja_jp/ses/latest/DeveloperGuide/using-configuration-sets.html" target="_blank">設定セット</a>を使用し、イベント送信先に`DELIVERY_DELAY`イベントタイプを設定する必要があります。詳細は<a href="https://docs.aws.amazon.com/ja_jp/ses/latest/DeveloperGuide/monitor-using-event-publishing.html" target="_blank">AWSのヘルプ</a>をご確認ください。
- "Mackerel上のメトリック名"の`DIMENSION_NAME`、`DIMENSION_VALUE`は以下の場合に値が加工されます。<br>
また、加工後のメトリック名が他のメトリック名と一致する場合、取得したメトリックが混同されます。
    - CloudWatchイベント送信先に複数のディメンションが設定されている場合、"_"(アンダースコア)で連結された文字列が適用されます。
    - 文字列長が100文字を超えた場合、超過部分は切り捨てられます。
    - `DIMENSION_VALUES`に含まれる"."(ドット)、"@"(アットマーク)は、"_"(アンダースコア)に置換されます。
