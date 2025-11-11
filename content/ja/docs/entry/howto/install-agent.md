---
Title: エージェントをインストールする
Date: 2014-04-30T16:10:16+09:00
URL: https://mackerel.io/ja/docs/entry/howto/install-agent
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815722986017
---

Mackerelにユーザー登録し、オーガニゼーションを作成すると、エージェントをインストールしてホストを登録できるようになります。エージェントのインストール手順については、お使いの OS ごとのヘルプを参照してください。なお、動作をサポートする環境については [対応環境](https://mackerel.io/ja/docs/entry/overview#support-environments) を参照してください。

<h2 id="install">インストール手順</h2>

インストール手順についてはOSごとのヘルプを参照してください。

- [Amazon Linuxにインストールする](./install-agent/amazon-linux)
- [CentOS / Red Hat Enterprise Linux派生OSにインストールする](./install-agent/rpm)
- [Ubuntu / Debian にインストールする](./install-agent/deb)
- [Linuxでバイナリファイルを直接使用する](./install-agent/binary)
- [Windows Serverにインストールする](./install-agent/msi)

<h2 id="communication">エージェントをインストールする際に発生する通信</h2>

セットアップスクリプトを使用したエージェントのインストール時は下記への通信が行われます。インターネットへの通信が制限されている環境においては、あらかじめ通信を許可してからエージェントのインストールを行ってください。

- `https://mackerel.io/`
  - 接続先の IP アドレスは [サービスがホストされているIPアドレスとポート番号を教えて下さい](https://support.mackerel.io/hc/ja/articles/360039633271) を参照してください
- リポジトリ
  - Amazon Linux / CentOS / Red Hat Enterprise Linux 派生 OS の場合
    - `http://yum.mackerel.io/`
  - Ubuntu / Debian の場合
    - `http://apt.mackerel.io/`
  - リポジトリは Amazon S3 でホストされています。Amazon S3 が使用する IP アドレス範囲は [AWS IP アドレスの範囲](https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/aws-ip-ranges.html) を参照してください

<h2 id="start">エージェントが動作する際に発生する通信</h2>

エージェントのインストール後は下記への通信が行われます。通信できない場合にはエージェントが起動せず、メトリックの投稿が行えません。

- `https://api.mackerelio.com/`
  - 接続先の IP アドレスは [サービスがホストされているIPアドレスとポート番号を教えてください](https://support.mackerel.io/hc/ja/articles/360039633271) を参照してください

インターネットへ直接通信できないサーバー向けに、エージェントでは [プロキシサーバー](https://mackerel.io/ja/docs/entry/spec/agent#config-file-proxy) の利用が可能となっています。

<h2 id="configuration">エージェントの設定ファイル</h2>

エージェントの設定ファイルは下記の場所にあります。設定ファイルを利用して行えることは [mackerel-agent仕様](https://mackerel.io/ja/docs/entry/spec/agent) をご覧ください。

- Amazon Linux / CentOS / Red Hat Enterprise Linux 派生 OS / Ubuntu / Debian の場合
  - `/etc/mackerel-agent/mackerel-agent.conf`
  - KCPS版：`/etc/mackerel-agent/mackerel-agent-kcps.conf`
- Windows Server の場合
  - 64bit版：`C:\Program Files\Mackerel\mackerel-agent\mackerel-agent.conf`
  - 32bit版：`C:\Program Files (x86)\Mackerel\mackerel-agent\mackerel-agent.conf`

<h2 id="uninstall">エージェントのアンインストール</h2>

アンインストール方法はOSごとの[インストール手順](#install)のヘルプに記載があります。

<h3 id="uninstall-files">エージェントのアンインストール時に削除されないファイル</h3>

下記のディレクトリ配下にあるエージェントの設定ファイルや idファイルは、エージェントをアンインストールしても削除されません。これらのファイルが残った状態でエージェントを再インストールすると、ホストIDや設定を再利用できます。ただし、そのIDのホストが退役済みの場合はIDを再利用できず、また、エージェントの起動に失敗するため、idファイルを削除したうえでエージェントを起動してください。

- Amazon Linux / CentOS / Red Hat Enterprise Linux 派生 OS / Ubuntu / Debian の場合
  - `/var/lib/mackerel-agent`
  - KCPS版：`/var/lib/mackerel-agent-kcps`
- Windows Server の場合
  - 64bit版：`C:\Program Files\Mackerel\mackerel-agent`
  - 32bit版：`C:\Program Files (x86)\Mackerel\mackerel-agent`

## KCPS外のホストをKCPS版Mackerelで監視する場合

設定ファイルの一番上の行に以下の設定を記述してください。

`apibase = "https://kcps-mackerel.io/"`
