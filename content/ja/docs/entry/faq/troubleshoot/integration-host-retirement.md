---
Title: FAQ・インテグレーションを利用しているが、ホストが退役されずに残っている
Date: 2018-04-24T16:55:57+09:00
URL: https://mackerel.io/ja/docs/entry/faq/troubleshoot/integration-host-retirement
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17391345971638042008
---

[AWSインテグレーション](https://mackerel.io/ja/docs/entry/integrations/aws)や[Azureインテグレーション](https://mackerel.io/ja/docs/entry/integrations/azure)を利用することにより、エージェントソフトウェア（mackerel-agent）をインストールすることなく、Mackerelでモニタリングをおこなえます。

ただし、Mackerelのインテグレーション機能は「ホストの登録（連携）」のために利用することはできますが、連携の解除（[ホストの退役](https://mackerel.io/ja/docs/entry/howto/host-retirement)）は自動ではおこなわれません。実体となる仮想サーバーが削除された場合でも、Mackerelに連携済みのホスト情報は登録されたままとなります。また、インテグレーション設定を削除した際にも、ホスト情報は登録されたままとなります。

対象のホスト情報が不要な場合は、別途[登録されたホストを Mackerel の管理対象から外す](https://mackerel.io/ja/docs/entry/howto/host-retirement)作業の実施をおこなってください。

---

[< FAQに戻る](https://mackerel.io/ja/docs/entry/faq)
