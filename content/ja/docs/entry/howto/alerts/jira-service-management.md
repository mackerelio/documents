---
Title: Jira Service Managementにアラートを通知する
Date: 2025-09-30T15:46:12+09:00
URL: https://mackerel.io/ja/docs/entry/howto/alerts/jira-service-management
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802888565263923197
---

[Jira Service Management](https://www.atlassian.com/software/jira/service-management)との連携を行う事で、Mackerelで設定した監視ルールに従い発生したアラート通知をJira Service Managementへ送る事が出来ます。具体的にはMackerelでアラートが発生した際に、Jira Service Managementに新たなアラートが作成されます。また、Mackerelでアラートが解決された際にJira Service Managementで作成されたアラートを閉じる仕組みとなっております。

![Jira Service Managementのアラート](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20250908/20250908154304.png)

## Jira Service Managementとの連携を追加する
連携の設定は、チャンネルページの[Jira Service Managementフォーム](https://mackerel.io/my/channels/-/create#jira-service-management)から行って下さい。

![Jira Service Managementフォーム](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20250908/20250908140337.png)

連携には、Jira Service Managementの[Integration Events API](https://developer.atlassian.com/cloud/jira/service-desk-ops/rest/v1/api-group-integration-events/)を利用しています。API Keyは[API 統合をセットアップする | Jira Service Management Cloud | アトラシアン サポート](https://support.atlassian.com/ja/jira-service-management-cloud/docs/set-up-an-api-integration/)に従って発行したものを入力してください。
