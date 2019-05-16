---
Title: AWSインテグレーション - SES
Date: 2019-04-08T18:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/ses
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17680117127020837213
CustomPath: integrations/aws/ses
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/ses/" target="_blank">Amazon Simple Email Service</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1リージョン = 1マイクロホストと換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのSES対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関してはAWSのヘルプをご確認ください。<br><a href="https://docs.aws.amazon.com/ja_jp/ses/latest/DeveloperGuide/monitor-sending-activity.html" target="_blank">Amazon SES 送信アクティビティのモニタリング</a><br><a href="https://docs.aws.amazon.com/ja_jp/ses/latest/DeveloperGuide/receiving-email-metrics.html" target="_blank">Amazon SES によるメール受信のメトリクスの表示</a>

最大で `10 + 2 × (ルール数)` のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|Email Sending Events|Send<br>Delivery<br>Bounce<br>Complaint<br>Reject<br>Open<br>Click|ses.email_sending_events.send<br>ses.email_sending_events.delivery<br>ses.email_sending_events.bounce<br>ses.email_sending_events.complaint<br>ses.email_sending_events.reject<br>ses.email_sending_events.open<br>ses.email_sending_events.click|integer|Sum|
|Reputation|Reputation.BounceRate<br>Reputation.ComplaintRate|ses.reputation.bounce_rate<br>ses.reputation.complaint_rate|percentage|Average|
|Email Receiving Failure|PublishFailure<br>PublishExpired|ses.email_receiving_failure.#.failure<br>ses.email_receiving_failure.#.expired|integer|Sum|
|Sent Last 24 Hours|-|ses.sent_last_24_hours.sent|integer|-|

- "Mackerel上のメトリック名"の#には、SESのRuleNameが入ります。

<h2 id="notes">注意事項</h2>
- "Email Sending Events"の"Open", "Click"は<a href="https://docs.aws.amazon.com/ja_jp/ses/latest/DeveloperGuide/using-configuration-sets.html" target="_blank">設定セット</a>を使用し、オープン、クリックイベントをキャプチャした場合に発生します。詳細は<a href="https://docs.aws.amazon.com/ja_jp/ses/latest/DeveloperGuide/configure-custom-open-click-domains.html" target="_blank">AWSのヘルプ</a>をご確認ください。
- AWSインテグレーションのSES対応ではタグによるホストの絞り込みに対応していません。
