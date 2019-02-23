---
Title: FAQ・アラートのチェック間隔はどれぐらいですか？
Date: 2017-11-20T14:38:26+09:00
URL: https://mackerel.io/ja/docs/entry/faq/monitoring/alert-interval
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8599973812319461379
---

* ホストの死活監視は、Mackerel管理サーバがmackerel-agentから通信を受けなくなって一定時間後にNG判断を下します。
* ホストメトリック監視・チェック監視はサーバーに情報が送信されたタイミング（通常は毎分）でOK/NGを判断しています。
* サービスメトリック監視はサーバーにメトリックが送信されたタイミングでOK/NGを判断しています。
* 外形監視は毎分間隔でチェックとOK/NGを判断しています。
* 上記チェック間隔については今後見直しが入る可能性があります。

---

[< FAQに戻る](https://mackerel.io/ja/docs/entry/faq)
