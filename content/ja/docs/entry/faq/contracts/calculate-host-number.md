---
Title: FAQ・ホスト数の計算方法について
Date: 2017-11-20T15:21:21+09:00
URL: https://mackerel.io/ja/docs/entry/faq/contracts/calculate-host-number
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8599973812319471042
---

Mackerel におけるホスト数の計算は、以下のように行います。

* ホスト台数は過去一ヶ月分の移動平均での算出となります。
    * 平均台数であり、最大同時起動数や延べ台数ではありません。
    * 例えば、高負荷対策のために一時的にサーバー台数が大幅に増加することがあったとしても、その月の利用料金が大きく増えてしまう、といったことはありません。
* 1時間程度以内の間隔で定期的に、アクティブなホスト数をカウントします。
    * [ホストのステータスに関わらず](https://mackerel.io/ja/docs/entry/howto/alerts#host-statuses)、メトリック投稿APIにアクセスしたユニークなホスト数を計上します。
        * ホストのステータスを `poweroff` などにしていても、メトリック投稿があるホストはアクティブなホストとしてカウントされます。
    * 通常はmackerel-agentの起動数となります。
    * 退役（Retired）状態のホスト・メトリックの投稿がおこなわれていないホストは、アクティブなホストとしてカウントされません。
* 有料プランをご利用の場合、アクティブなホストがゼロであっても、最低利用料金として1台分の課金が発生します。

---

[< FAQに戻る](https://mackerel.io/ja/docs/entry/faq)
