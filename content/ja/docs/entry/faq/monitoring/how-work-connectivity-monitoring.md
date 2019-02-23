---
Title: FAQ・Mackerelの死活監視はどのような仕組みで動いていますか？
Date: 2017-11-20T14:35:18+09:00
URL: https://mackerel.io/ja/docs/entry/faq/monitoring/how-work-connectivity-monitoring
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8599973812318611343
---

Mackerel に登録されたホストの mackerel-agent からのデータ送信が途絶した期間が一定時間以上継続すると、NGとして判断し connectivity alert を発報します。

NG までの閾値となる時間については、日々調節をおこなっているため非公開です。ユーザー毎に設定変更することもできない仕様になっています。

---

[< FAQに戻る](https://mackerel.io/ja/docs/entry/faq)
