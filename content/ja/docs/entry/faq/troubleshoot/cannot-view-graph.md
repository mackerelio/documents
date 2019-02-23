---
Title: FAQ・Mackerel管理画面にグラフが表示されていない／データが投稿されていないようだ
Date: 2017-11-20T15:08:32+09:00
URL: https://mackerel.io/ja/docs/entry/faq/troubleshoot/cannot-view-graph
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8599973812319468568
---

* mackerel-agentが起動しているかどうかを確認してください。
* mackerel-agentが起動している場合、サーバーの時刻が正しく設定されているかを確認してください。
    * サーバーに設定された時刻と現在時刻との間で相違がある場合、データが正しく投稿されない可能性があります。
* mackerel-agent がインストールされているサーバから、mackerel.io / api.mackerelio.com への通信がおこなえるかどうかを確認してください。
* 新規ホスト登録から初回メトリック投稿まで少しタイムラグがあるため、その間はグラフが表示されません。表示されるまでしばらくお待ちください。

---

[< FAQに戻る](https://mackerel.io/ja/docs/entry/faq)
