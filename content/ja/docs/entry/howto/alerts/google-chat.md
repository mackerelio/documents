---
Title: Google Chatにアラートを通知する
Date: 2026-11-13T16:42:25+09:00
URL: https://mackerel.io/ja/docs/entry/howto/alerts/google-chat
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/14729835035337875589
---

Mackerelでは、アラート通知を[Google Chat](https://chat.google.com/)のスペースに送信することができます。

### Google Chatへの通知チャンネルを追加する

通知を設定するには、[チャンネルページのGoogle Chatのフォーム](https://mackerel.io/my/channels/-/create#google-chat)から新しい通知チャンネルを作成してください。

![Google Chatのフォーム](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20261113/20261113121537.png)

連携にはGoogle ChatのIncoming webhook機能を利用しています。[Google Chat アプリを Webhook として作成する  |  Google for Developers](https://developers.google.com/workspace/chat/quickstart/webhooks?hl=ja#register-webhook)の「受信 Webhook を登録する」に従って発行した`https://chat.googleapis.com/v1/spaces/SPACE_ID/messages?key=KEY&token=TOKEN`形式のURLをMackerelのフォームに入力してください。

### レート制限について

- Google ChatのIncoming webhookには、スペースごとに1秒あたり1件のレート制限が適用されます（[制限事項と考慮事項](https://developers.google.com/workspace/chat/quickstart/webhooks?hl=ja#limits)）。
- 上記の制限から、MackerelはGoogle Chatに通知を送る速度をURLごとに1秒あたり1件に抑制しています。通知するイベントが同時に複数件発生した場合には、数秒単位の待ち時間が発生します。
- この待ち時間が伸び続ける事態を防ぐため、ある通知の待ち時間が一定時間（数十秒）を超えるとき、その通知は実際に送られず送信失敗扱いになります。その後、アラート通知であれば一定時間後に再送が試みられますが、それ以外の種類の通知は再送されません。
