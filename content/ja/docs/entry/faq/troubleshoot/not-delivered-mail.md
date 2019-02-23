---
Title: FAQ・メールが届かない
Date: 2017-11-20T15:15:46+09:00
URL: https://mackerel.io/ja/docs/entry/faq/troubleshoot/not-delivered-mail
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8599973812319470035
---

Mackerelからのアカウント登録確認メールやアラート通知メールなど、Mackerel では様々なメールを送信しています。メールが届かない場合は以下を確認してください。

* `noreply@mackerel.io` からのメールを受け取れない。
  * メーリングリストや携帯のキャリアメールなどで送信元アドレスでフィルタリングしている場合は `mackerel.io` からのメールを受け取れるようにフィルタ設定を変更してください。
* 送信先にメーリングリストアドレスを指定している場合、そのリスト内のメンバーがドメイン指定受信拒否設定をしていないかどうかを確認してください。
* メールが迷惑メールになってしまう。
  * 迷惑メールフォルダに入ってしまう場合はお使いの迷惑メールフィルタの設定をご確認ください。
  * gmailの場合はgmailのサポートページ( https://support.google.com/mail/answer/1366858 )をご参照ください。
  * mackerel.ioドメインには[SPF](http://wikipedia.org/wiki/Sender_Policy_Framework)の設定をしておりますので、SPFが設定されている場合は迷惑メールと判定されないような設定をする方法もあります。
* アラートメールが届かない
  * Mackerel仮登録状態の場合はアラートメールが送信されません。アラートメールを受け取るには、アカウント登録確認メール本文にあるリンクをクリックして本登録を完了してください。

上記全てを確認しても原因が不明な場合は、Mackerel にログインした状態でヘッダに表示される「サポートチームへ連絡」からご連絡ください。

---

[< FAQに戻る](https://mackerel.io/ja/docs/entry/faq)
