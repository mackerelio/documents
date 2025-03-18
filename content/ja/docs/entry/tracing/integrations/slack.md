---
Title: トレース - Slackに通知する
Date: 2025-03-13T15:54:37+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/integrations/slack
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333961506
---

Mackerelトレーシング機能ではアラートを Slack に送信することができます。

Setting 画面にある、下図の「Add Slack」ボタンから Slack を設定できます。

<figure class="figure-image figure-image-fotolife" title="Setting → Add Slack への動線">[f:id:mackerelio:20250305173633p:plain]<figcaption>Setting → Add Slack への動線</figcaption></figure>

[Slack の Incoming Webhook](https://api.slack.com/messaging/webhooks) を利用してアラートを送信します。 Slack で発行した Incoming Webhook の URL を Slack Webhook 通知の URL に入力してください。

<figure class="figure-image figure-image-fotolife" title="Slack 通知のための Incoming Webhook URL を入力する画面">[f:id:mackerelio:20250305173656p:plain]<figcaption>Slack 通知のための Incoming Webhook URL を入力する画面</figcaption></figure>
