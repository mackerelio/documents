---
Title: FAQ・「metric rate monitor」監視ルールについて
Date: 2018-04-19T15:38:47+09:00
URL: https://mackerel.io/ja/docs/entry/faq/monitoring/rate-metric-monitor
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17391345971636460640
---

ホストメトリック監視ルールを作成する際、その監視対象として以下の4種類の項目を選択することができます。

- CPU %
- Memory %
- Swap %
- Filesystem %


[f:id:mackerelio:20180419153725p:plain]


これらは**metric rate monitor**というカテゴリに属する監視ルールで、「mackerel-agent から送信されるメトリック（システムメトリック）」の情報をもとに監視設定可能な項目です。このカテゴリの中のメトリックに対しては、その閾値を「割合」（全体を100%としたパーセンテージ）で指定します。

上記4項目の詳細な仕様は、以下のとおりです。

- CPU %
    - コア数に関わらず、全体を100%としたときの `100% - idle%` の割合を閾値として指定します。
- Memory %
    - `total` に対する `used` の割合を閾値として指定します。
- Swap %
    - `swap total` に対する `swap used` の割合を閾値として指定します。
- Filesystem %
    - `size` に対する `used` の割合を閾値として指定します。
    - 複数ファイルシステムのメトリックが投稿されている場合、最も使用率の高いファイルシステムと閾値とが比較に用いられます。

---

[< FAQに戻る](https://mackerel.io/ja/docs/entry/faq)
