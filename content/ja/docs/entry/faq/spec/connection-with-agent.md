---
Title: FAQ・Mackerelサーバーとmackerel-agent間の通信はどのようになっていますか？
Date: 2017-11-20T14:40:16+09:00
URL: https://mackerel.io/ja/docs/entry/faq/spec/connection-with-agent
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8599973812319461854
---

* mackerel-agentからMackerelサーバーとHTTPのREST API経由にて通信しています。
  * Mackerelサーバー側からmackerel-agentが入っているホストに直接通信することはありません。
* SSL/TLS通信で暗号化されています。

---

[< FAQに戻る](https://mackerel.io/ja/docs/entry/faq)
