---
Title: Amazon EventBridgeにアラートを通知する
Date: 2020-01-22T00:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/howto/alerts/eventbridge
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613498451525
---

Mackerelでは、アラートの内容のJSONをAmazon EventBridgeで受け取れます。通知を受け取った後の処理をAWS Lambdaなどを用いて自動化したい場合などに重宝するでしょう。

## Amazon EventBridgeへの通知を追加する

### コンソール画面で設定する

通知の設定は、[チャンネルページのAmazon EventBridgeのフォーム](https://mackerel.io/my/channels/-/create#amazon-event-bridge)から、追加ができます。ご利用のAWSのアカウントID、通知を受け取るAWSリージョン、通知を識別するためのイベント名を指定して下さい。

<img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20191211/20191211105919.png" style="max-width:400px">

- AWSのアカウントIDの確認方法は、[AWS アカウント ID - AWS 全般のリファレンス](https://docs.aws.amazon.com/ja_jp/general/latest/gr/acct-identifiers.html)を参照してください。
- Amazon EventBridgeでのMackerelのイベントソース名は以下のように作られます。
  - `aws.partner/mackerel.io/{オーガニゼーション名}/{お客様が指定したイベント名}`

### AWSコンソールで連携設定をする

Mackerelでの設定が完了したら、Amazon EventBridgeでイベントバス・ルールを作成し、Mackerelからの通知を受け取れるようにする必要があります。

1. AWSコンソールの[パートナーイベントソース](https://console.aws.amazon.com/events/home#/partners)のページを開きます。
2. コンソールの操作するリージョンを、Mackerelで設定した通知を受け取るAWSリージョンに切り替えてください。
3. 追加したイベントソースを選択し、右上にある イベントバスと関連付ける ボタンを押してください。
   - <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20191211/20191211105938_original.png" style="max-width:400px">
4. イベントバスと関連付ける画面で必要なアクセス許可を選択し、 関連付ける ボタンを押してください。
   - <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20191211/20191211105942_original.png" style="max-width:400px">
5. 左のパネルの、ルールのページを開き、イベントバスのドロップダウンリストから、作成したイベントバスを選択し、下部に表示されている ルールを作成 をクリックします
   - <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20191211/20191211105928_original.png" style="max-width:400px">
6. ルールを作成画面では、名前、パターン、ターゲットを設定し、イベントバスの選択では、 カスタムイベントまたはパートナーイベントバス を選択し、追加したイベントバスを設定します。
   - <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20191211/20191211105923_original.png" style="max-width:400px">
   - <img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20191211/20191211105932_original.png" style="max-width:400px">

## JSONフォーマット

通知されるJSONは以下のような内容を含んでいます。
(項目は任意のタイミングで追加される場合があります)

```javascript
{
  "orgName": "Macker...",
  "event": "alert",
  "memo": "memo....",
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
  },
  "resourceInfo": {
    "service":"ec2",
    "region":null,
    "accountId":null,
    "resourceType":"instance",
    "resourceId":"i-12345..."
  }
}
```

## JSONの各項目

|KEY|TYPE|DESCRIPTION|
|:--|:--|:-|
|orgName|string|アラートが発生しているオーガニゼーションの名前|
|event|string|`alert`固定|
|memo|string|監視ルールのメモ|
|host|object|ホスト情報(ホストメトリック時のアラートのみ出力)|
|service|object|サービス情報(サービスメトリック時のアラートのみ出力)
|alert|object|アラート情報|
|resourceInfo|object|AWSのリソース情報(対応しているコンポーネントの場合のみ出力)|

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

### JSONの各項目(AWSのリソース情報)

#### EC2 の場合

mackerel-agentまたはAWSインテグレーションを導入しているEC2インスタンスで、アラートが発生した場合に、Mackerelですでに取得している情報<a href="#1">*1</a>を、ARN形式のフォーマットに従ったフィールドの情報を返却します。

|KEY|TYPE|DESCRIPTION|
|:--|:--|:-|
|service|string|[optional] サービス名 `ec2`|
|region|string|[optional] リージョン名|
|accountId|string|[optional] AWSアカウントID|
|resourceType|string|[optional] リソースの種別 `instance`|
|resourceId|string|[optional] リソースID（EC2 インスタンスID） `i-12345...`|

<div id="1" style="position:relative; top:-80px;"></div>
<a href="#1">*1</a> ただし、mackerel-agentの設定で `cloud_platform` を `none` としている場合は、これらの情報を取得できません。
