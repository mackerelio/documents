---
Title: ホストにロールを割り当てる
Date: 2014-04-30T17:02:07+09:00
URL: https://mackerel.io/ja/docs/entry/howto/assign-roles-to-hosts
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815722988420
---

## ウェブから

作成したロールをホストに割り当てるには、Mackerelにログイン後、サイドバーの「Hosts」から[ホスト一覧](https://mackerel.io/hosts)にアクセスし、対象ホストの「SERVICE / ROLE」列をクリックします。このホストに割り当てるロールを選択できるフォームが表示されますので、一覧から選んで「Update」をクリックしてください。

## エージェントから

エージェントの設定ファイルを編集して、エージェントの起動時に自動でサービスおよびロールを割り当てられます。詳しくは[エージェントのヘルプ](https://mackerel.io/ja/docs/entry/spec/agent#setting-services-and-roles)をご覧ください。
