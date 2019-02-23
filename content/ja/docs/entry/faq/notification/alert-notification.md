---
Title: FAQ・アラートの通知は復旧するまで継続して何回も通知されますか？
Date: 2017-11-20T14:34:01+09:00
URL: https://mackerel.io/ja/docs/entry/faq/notification/alert-notification
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8599973812319460162
---

アラートはステータスが変わったタイミングで一度のみ通知されます。

例えばOK→Warning→Critical→OKといった推移をした場合は、3回ステータスが変わっているため、ステータスが変わった各タイミングで合計3回通知されます。

ただし、外形監視ルールによるアラートの場合、上記の仕様に加え、監視結果のメッセージが変動した場合にも通知がおこなわれます。

---

[< FAQに戻る](https://mackerel.io/ja/docs/entry/faq)
