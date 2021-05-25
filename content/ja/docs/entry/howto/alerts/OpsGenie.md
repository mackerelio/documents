---
Title: Opsgenieに通知を表示する
Date: 2015-07-10T14:15:21+09:00
URL: https://mackerel.io/ja/docs/entry/howto/alerts/OpsGenie
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450100794470
---

[Opsgenie](https://www.opsgenie.com/)はMackerelのような監視ツールからのアラートを集約し、Opsgenie上に登録した任意の通知ルールに従って様々な通知を送ることが出来るアラート管理サービスです。通知の例として、音声、SMS、email、モバイルへアラート通知を送る事が出来ます。

Opsgenieとの連携を行う事で、Mackerelで設定した監視ルールに従い発生したアラート通知をOpsgenieへ送る事が出来ます。具体的にはMackerelでアラートが発生した際に、Opsgenieに新たなアラートが作成されます。また、Mackerelでアラートが解決された際にOpsgenieで作成されたアラートを閉じる仕組みとなっております。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150709/20150709113705.png)

## Opsgenieとの連携を追加する
連携の設定は、チャンネルページの[Opsgenieフォーム](https://mackerel.io/my/channels)から行って下さい。

[f:id:mackerelio:20210428161556p:plain]

連携には、[OpsgenieのAlert API](https://www.opsgenie.com/docs/web-api/alert-api)を利用しています。API Keyは以下の方法で発行したものを入力してください。

- Opsgenieメイン画面左側のIntegrationsタブを選択
- Integrations画面左のAPIタブをクリック
- 画面中央部、下の図の位置に書かれているものがAPI Keyとなります

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150709/20150709114619.png)

Opsgenieには、通知宛先の設定項目としてrecipientsがあります。チャンネル作成の際には、カンマ区切りでEmailアドレス、グループ名、チーム名を入力ください。
