---
Title: FAQ・エージェントインストール済みのテンプレート・マシンイメージ等から複製したサーバーがMackerelに認識されない
Date: 2018-04-19T17:49:38+09:00
URL: https://mackerel.io/ja/docs/entry/faq/troubleshoot/copy-from-template
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17391345971636489810
---

Mackerelでは、各ホストを一意のホストIDで識別しており、そのホストIDは、mackerel-agentが動作しているホスト上のファイル（Linux系OSのホストの場合は `/var/lib/mackerel-agent/id` 、Windows Server の場合はインストールフォルダ内の `id` ファイル。詳しくは[用語集のホストの項目](https://mackerel.io/ja/docs/entry/glossary#host)を参照してください）に保存されています。

同じホストIDが記録されたファイルを持つ別ホスト上で mackerel-agent が起動した場合、実際には複数のホストであっても Mackerel 側では同一のホストと認識され、メトリックの投稿や監視が正しく動作しなくなります。こちらも合わせて参照してください。[FAQ・インスタンスを再作成しても同じホストとして認識させたい](https://mackerel.io/ja/docs/entry/faq/spec/host-inheriting)

そのため、エージェントインストール済みのテンプレート・マシンイメージを作成する際には、ホストIDが記録されたファイルを削除した状態で作成するよう、注意してください。

既にIDファイルが他ホストと重複している状態で起動してしまったホストについては、以下の手順を実施することにより、別ホストとして連携追加させることが可能です。

1.対象ホスト内のホストIDファイルを削除する

2.対象ホストのエージェントの再起動をおこなう

---

[< FAQに戻る](https://mackerel.io/ja/docs/entry/faq)
