---
Title: PagerDutyにアラートを通知する
Date: 2014-11-25T16:30:01+09:00
URL: https://mackerel.io/ja/docs/entry/howto/alerts/pagerduty
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450075241072
---

[PagerDuty](http://www.pagerduty.com/)はMackerelのような監視ツールからのアラートを集約し、PagerDuty上に登録した任意の通知ルールに従って様々な通知を送ることが出来るインシデント管理サービスです。通知の例として、音声、SMS、email、プッシュ型のアラート通知ができます。

PagerDutyと連携を行う事で、Mackerelで発生したアラートをPagerDuty上のインシデントとして管理できます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141125/20141125162901.png" alt="f:id:mackerelio:20141125162901p:plain" title="f:id:mackerelio:20141125162901p:plain" class="hatena-fotolife" itemprop="image"></span></p>

## PagerDutyとの連携を追加する

連携の設定は、[チャンネルページのPagerDutyフォーム](https://mackerel.io/my/channels?new=pagerduty)から行って下さい。

<p><span itemscope="" itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20171124/20171124160618.png" alt="f:id:mackerelio:20171124160618p:plain" title="f:id:mackerelio:20171124160618p:plain" class="hatena-fotolife" itemprop="image"></span></p>

### Integration Key

PagerDutyとの連携は[Events API](https://v2.developer.pagerduty.com/docs/events-api)を通して行います。
PagerDuty上でMackerelと連携する"サービス"を作成して発行されたIntegration Keyを、Mackerelのチャンネル設定画面で入力してください。

- PagerDuty 画面上部の`Configuration`から`Service`を選択
- 画面中央右上の`Add New Service`を選択し、Mackerelと連携するサービスを作成します
- `Integration Type`は`Use our API directly`を選択します
  - この際、API の種類は `Events API v2` を指定してください
- 作成完了後、画面にIntegration Keyが表示されます。


### Notification Level

PagerDuty連携では、アラート通知をPagerDutyへ送信する監視のステータスを選択すること出来ます。選択肢は`Warning & Critical`と`Critical only`の2つがあります。

- `Warning & Critical`
  - Warning, Criticalいずれかのアラートが発生した際に、PagerDutyにインシデント通知を送ります
  - ステータスが正常に戻った時に、PagerDutyのインシデントは自動的に解決されます
  - WarningとCritical間の状態遷移が生じた際にもPagerDutyに通知を送りますが、一度発生したインシデントに対する経過通知を送るのみとなります
- `Critical only`
  - Warning時のアラートでは通知は送らず、Criticalのアラート時のみにPagerDutyにインシデント通知を送ります
  - ステータスが正常に戻った時に、PagerDutyのインシデントは自動的に解決されます
  - Warning状態への遷移時にはPagerDutyに対しては何も行いません

### アラート解決時の処理

デフォルトでは、Mackerel上でアラートが閉じた時点でPagerDuty上のインシデントも閉じられますが、"アラートが閉じた時にインシデントも閉じる"オプションをオフにすると、アラートが閉じた場合でもPagerDuty上のインシデントは閉じられません。

<h2 id="api-version">API Versionについて</h2>

PagerDutyのチャンネル設定画面では利用するEvents APIのバージョンを選べますが、新規に作成する設定ではAPI v2を利用することを推奨しています。
2017年11月時点では通知される内容に大きな違いはありませんが、今後Events API v2経由の場合のみに利用できる通知パラメータを追加する可能性があります。
