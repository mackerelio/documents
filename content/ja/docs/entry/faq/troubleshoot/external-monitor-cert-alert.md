---
Title: 'FAQ・外形監視で「x509: certificate signed by unknown authority」エラーが出ます'
Date: 2018-07-25T18:51:08+09:00
URL: https://mackerel.io/ja/docs/entry/faq/troubleshoot/external-monitor-cert-alert
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10257846132604455875
---

外形監視ルールを設定することにより、「x509: certificate signed by unknown authority」というメッセージとともにアラートが発報することがあります。

一概に特定の要因を原因として断定することは難しいのですが、中間証明書の設定漏れを起因として同様のエラーが発生するケースが多いようです。

「x509: certificate signed by unknown authority」というメッセージが確認できた場合、改めて中間証明書のインストールが正しくされているかどうかを確認してください。

---

[< FAQに戻る](https://mackerel.io/ja/docs/entry/faq)
