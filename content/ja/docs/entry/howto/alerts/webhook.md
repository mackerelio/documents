---
Title: Webhookにアラートを通知する
Date: 2015-09-24T17:26:17+09:00
URL: https://mackerel.io/ja/docs/entry/howto/alerts/webhook
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653458415122476525
---

Webhookを利用することでアラートの内容のJSONをPOSTで受け取れます。通知を受け取った後の処理を自動化したい場合などに重宝するでしょう。

## Webhookへの通知を追加する

通知の設定は、[チャンネルページのWebhookのフォーム](https://mackerel.io/my/channels?new=webhook)から、追加ができます。MackerelサーバーからのPOSTを受け取るURLを指定して下さい。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150924/20150924174417.png)

## JSONフォーマット

通知されるJSONは以下のような内容を含んでいます。
(項目は任意のタイミングで追加される場合があります)

```json
{
  "orgName": "Macker...",
  "event": "alert",
  "host": {
    "id": "22D4...",
    "name": "app01",
    "url": "https://mackerel.io/orgs/.../hosts/...",
    "type": "unknown",
    "status": "working",
    "memo": "",
    "isRetired": false,
    "roles": [
      {
        "fullname": "Service: Role",
        "serviceName": "Service",
        "serviceUrl": "https://mackerel.io/orgs/.../services/...",
        "roleName": "Role",
        "roleUrl": "https://mackerel.io/orgs/.../services/..."
      }
    ]
  },
  "alert": {
    "openedAt": 1473129912,
    "closedAt": 1473130092,
    "createdAt": 1473129912693, 
    "criticalThreshold": 1.9588528112516932, 
    "duration": 5, 
    "isOpen": true, 
    "metricLabel": "MetricName", 
    "metricValue": 2.255356387321597, 
    "monitorName": "MonitorName", 
    "monitorOperator": ">", 
    "status": "critical", 
    "trigger": "monitor", 
    "id": "2bj...",
    "url": "https://mackerel.io/orgs/.../alerts/2bj...",
    "warningThreshold": 1.4665636369580741
  }
}
```

## JSONの各項目

|KEY|TYPE|DESCRIPTION|
|:--|:--|:-|
|orgName|string|アラートが発生しているオーガニゼーションの名前|
|event|string|`alert`固定|
|host|object|ホスト情報(ホストメトリック時のアラートのみ出力)|
|service|object|サービス情報(サービスメトリック時のアラートのみ出力)
|alert|object|アラート情報|

### JSONの各項目(ホスト情報)

|KEY|TYPE|DESCRIPTION|
|:--|:--|:-|
|id|string|ホストID|
|name|string|ホスト名|
|url|string|ホスト詳細のURL|
|status|string|ホストステータス(`working`, `standby`, `poweroff`, `maintenance`)|
|memo|string|ホストに登録されているメモ|
|isRetired|boolean|退役済みかどうか|
|roles|array[object]|ロール情報(ホストにロールが設定されている場合)|

### JSONの各項目(ロール情報)

|KEY|TYPE|DESCRIPTION|
|:--|:--|:-|
|fullname|string|サービス名:ロール名|
|serviceName|string|サービス名
|serviceUrl|string|サービス詳細のURL|
|roleName|string|ロール名|
|roleUrl|string|サービス詳細中のロールのURL|

### JSONの各項目(サービス情報)

|KEY|TYPE|DESCRIPTION|
|:--|:--|:-|
|id|string|サービスID|
|memo|string|サービスに登録されているメモ|
|name|string|サービス名|
|orgId|string|サービスが登録されているオーガニゼーションのID|
|roles|array[object]|ロール情報(サービスにロールが登録されている場合)|

### JSONの各項目(アラート情報)

|KEY|TYPE|DESCRIPTION|
|:--|:--|:-|
|id|string|アラートのID|
|status|string|アラートのステータス(`ok`, `warning`, `critical`, `unknown`)|
|isOpen|boolean|アラートのオープンの状態|
|trigger|string|通知が送信されたトリガー(`monitoring`(監視), `manual`(手動操作), `monitorDelete`(監視ルール削除), `hostRetire`(ホスト退役))|
|url|string|アラート詳細のURL|
|openedAt|number|アラートの発生時刻(エポック秒)|
|closedAt|number|アラートの解決時刻(エポック秒)|
|createdAt|number|アラートの発生時刻(エポックミリ秒)。非推奨で廃止予定。openedAtをご利用ください|
|monitorName|string|アラートを検知した監視項目名|
|metricLabel|string|監視対象のメトリックなどの名称|
|metricValue|number|アラート検知時のメトリックの値|
|criticalThreshold|number|CRITICALの閾値|
|warningThreshold|number|WARNINGの閾値|
|monitorOperator|string|`>` or `<`|
|duration|number|監視間隔|

※アラート情報の項目は、監視対象の種類（ホストメトリック, サービスメトリック, 外形監視, 式による監視)やそれぞれの設定項目によって増減します。たとえば外形監視でURLのみ設定されている場合のアラートではメトリックや閾値などの情報はアラートに含まれません。監視対象の種類と出力される項目は、監視ルール設定画面で入力可能な項目と対応します。

## Webhook活用方法

- [Hubotでチャットにアラートを通知する](https://mackerel.io/ja/docs/entry/advanced/hubot)
- [fluent-plugin-webhook-mackerelを利用する](https://mackerel.io/ja/docs/entry/advanced/fluent-plugin-webhook-mackerel)

## Webhookをセキュアに使うために

Webhookをセキュアに利用するために、以下の対策を組み合わせて利用することを推奨します。

- SSLを利用する
  - 独自証明書や一部の認証局には対応していません
- ベーシック認証を設定しURLに認証情報を含める
  - `https://user:p4ssw0rd@example.com` のような形式
- IP制限を実施する
  - Mackerelからの通知のリクエスト元IPアドレスレンジの詳細は[Webhook通知や外形監視など、Mackerelからの通知元IPアドレスは？](https://support.mackerel.io/hc/ja/articles/360039701332-Webhook%E9%80%9A%E7%9F%A5%E3%82%84%E5%A4%96%E5%BD%A2%E7%9B%A3%E8%A6%96%E3%81%AA%E3%81%A9-Mackerel%E3%81%8B%E3%82%89%E3%81%AE%E9%80%9A%E7%9F%A5%E5%85%83IP%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%81%AF-)をご覧下さい。
