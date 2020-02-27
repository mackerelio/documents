---
Title: エージェントをインストールする
Date: 2014-04-30T16:10:16+09:00
URL: https://mackerel.io/ja/docs/entry/howto/install-agent
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815722986017
---

Mackerelにユーザー登録、オーガニゼーションを作成すると、エージェントをインストールしてホストを登録することができるようになります。「[Mackerelエージェントをインストールする][]」からお使いのOSに合わせたパッケージをダウンロードし、指示に従ってインストールします。

- [Amazon Linuxにインストールする](./install-agent/amazon-linux)
- [CentOS / RedHat にインストールする](./install-agent/rpm)
- [Ubuntu / Debian にインストールする](./install-agent/deb)
- [Linuxでバイナリファイルを直接使用する](./install-agent/binary)
- [Windowsにインストールする](./install-agent/msi)
- [macOSにインストールする](./install-agent/mac)

エージェントがインストールされ、正しく動きはじめると、Mackerelにホストとして登録されます。[ダッシュボード](https://mackerel.io/my/dashboard)などでご確認ください。

<h2 id="configuration">エージェントの設定</h2>

エージェントの設定ファイルは、デフォルトで `/etc/mackerel-agent/mackerel-agent.conf`(KCPS用エージェントの場合は`/etc/mackerel-agent-kcps/mackerel-agent-kcps.conf`) が読み込まれます。

詳細は[mackerel-agent仕様](https://mackerel.io/ja/docs/entry/spec/agent)をご覧ください。

設定ファイルを利用して、以下のことを実現できます:

- [サービス、ロールを設定する](https://mackerel.io/ja/docs/entry/spec/agent#setting-services-and-roles)
- [カスタムメトリックを投稿する](https://mackerel.io/ja/docs/entry/advanced/custom-metrics)
- [チェック監視項目を追加する](https://mackerel.io/ja/docs/entry/custom-checks)

## KCPS外のホストをKCPS版mackerelで監視する場合

設定ファイルの一番上の行に以下の設定を記述して下さい。

    apibase = "https://kcps-mackerel.io/"

## エージェントのアンインストール

エージェントのアンインストールは、お使いのパッケージングシステムにあわせて行ってください。

またその際、デフォルトでは `/var/lib/mackerel-agent/id` (KCPS用エージェントの場合は `/var/lib/mackerel-agent-kcps/id`)にホストIDの記録されたファイルが残っているため、これを削除してください。

[Mackerelエージェントをインストールする]: https://mackerel.io/my/instruction-agent
