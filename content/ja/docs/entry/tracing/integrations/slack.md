---
Title: トレース - Slackに通知する
Date: 2025-03-13T15:54:37+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/integrations/slack
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333961506
---

Mackerelに送信したトレースによって課題が発生した際に、Slack へ通知することができます。通知には [Slack の Incoming Webhook](https://docs.slack.dev/messaging/sending-messages-using-incoming-webhooks/) を利用します。

通知先の設定は、[課題一覧画面](https://mackerel.io/my/issues)の右上にある「Slack通知連携」ボタンからおこないます。

<figure class="figure-image figure-image-fotolife" title="課題一覧画面">[f:id:mackerelio:20260824154003p:plain]<figcaption>課題一覧画面</figcaption></figure>

「新規通知先を追加」ボタンをクリックします。なお、この画面には設定済みの通知先が一覧で表示されます。

<figure class="figure-image figure-image-fotolife" title="Slack通知連携画面">[f:id:mackerelio:20260824153958p:plain]<figcaption>Slack通知連携画面</figcaption></figure>

Slack で発行した Incoming Webhook の URL を Webhook URL 欄に入力します。

<figure class="figure-image figure-image-fotolife" title="新規通知先の追加画面">[f:id:mackerelio:20260824153953p:plain]<figcaption>新規通知先の追加画面</figcaption></figure>
