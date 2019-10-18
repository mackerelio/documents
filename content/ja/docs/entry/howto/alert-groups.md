---
Title: アラートグループでアラートをまとめる
Date: 2018-06-28T17:11:02+09:00
URL: https://mackerel.io/ja/docs/entry/howto/alert-groups
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17391345971658196778
---

アラートグループ機能を使うと、特定のロール内で発生するアラートや、同じ監視ルールによるアラートをまとめることができます。
また、アラートグループの通知を有効にすることにより、チャットツールでの通知がわかりやすくなります。


監視ルール・アラート・通知チャンネルについては以下のドキュメントを参照してください。
[https://mackerel.io/ja/docs/entry/howto/alerts:embed]


## アラートグループ設定を作成する
アラートグループは、ユーザーが明示的に設定したルールを元にアラートをまとめる機能です。
はじめに、まとめるルールを[アラートグループ設定作成ページ](https://mackerel.io/my/alert-group-settings/-/create)にて作成してください。

アラートグループ設定では、対象とするサービスやロール、または監視ルールを指定します。

- サービスを指定: 対象のサービス内で発生するアラートをまとめてアラートグループが作成されます。複数のサービスを指定することもできます。障害後に時系列で起きたアラートを振り返るときに便利です。
- ロールを指定: 対象ロールに属するホストで発生するアラートをまとめてアラートグループが作成されます。特に、ロールに属するホストが多いときに、ロール単位でアラートグループを作成するようにしておくと、チャットツールがアラート通知で埋まってしまうことを防げます。
- 監視ルールを指定: 特定の監視ルールでの通知が複数のホストで同時に発生することが多い場合は、監視ルールで絞り込んでアラートグループ設定を作成できます。サービスやロールの指定と共に設定するとAND条件となります。

## アラートグループを確認する
アラートグループが作られると、[アラート一覧](https://mackerel.io/my/alerts)で現在Open状態のアラートグループを確認できます。

[f:id:mackerelio:20180627160121p:plain:w750]
[f:id:mackerelio:20180627155026p:plain:w750]

また、[アラートグループ一覧ページ](https://mackerel.io/my/alert-groups)より過去のアラートグループを確認できます。

## 通知チャンネルにアラートグループを通知する
[通知チャンネル一覧](https://mackerel.io/my/channels)でアラートグループ通知の設定を有効にすると、アラートグループの通知が送信されます。
アラートグループに属するアラートは通知されなくなります。
アラートグループの通知を受け取りつつ、それに属するアラートの通知も受け取りたい場合は、別のチャンネル設定を作成してください。
アラートグループを通知できるチャンネルは、[Slack](https://mackerel.io/ja/docs/entry/howto/alerts/slack), Eメール, [Chatwork](https://mackerel.io/ja/docs/entry/howto/alerts/chatwork), [LINE](https://mackerel.io/ja/docs/entry/howto/alerts/line), [Typetalk](https://mackerel.io/ja/docs/entry/howto/alerts/typetalk), [Hipchat](https://mackerel.io/ja/docs/entry/howto/alerts/hipchat), [Yammer](https://mackerel.io/ja/docs/entry/howto/alerts/yammer), [Microsoft Teams](https://mackerel.io/ja/docs/entry/howto/alerts/microsoft-teams), [Webhook](https://mackerel.io/ja/docs/entry/howto/alerts/webhook)です。

[f:id:mackerelio:20180627151922p:plain:w500]

| アラート通知 | アラートグループ通知 |                                                                                    |
| ------------ | -------------------- | ---------------------------------------------------------------------------------- |
| 無効         | 無効                 | アラート・アラートグループは通知されません                                         |
| 有効         | 無効                 | アラートが個別に通知されます                                                       |
| 無効         | 有効                 | アラートグループが通知されます。アラートグループに属さないアラートは通知されません |
| 有効         | 有効                 | アラートグループと、アラートグループに属さないアラートが通知されます               |

