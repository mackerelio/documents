---
Title: チェック監視に公式チェックプラグイン集を使う
Date: 2015-11-05T11:49:37+09:00
URL: https://mackerel.io/ja/docs/entry/howto/mackerel-check-plugins
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653458415126984680
---

チェック監視に対応した公式プラグイン集を利用して、プロセス監視やログ監視などのチェック監視を簡単に実現できます。

[:contents]

## 公式チェックプラグイン集をインストールする（Linux系OSの場合）

インストールにはお使いの環境に合わせてyumリポジトリもしくはaptリポジトリを使うことを推奨しています。リポジトリの設定方法は「[Mackerelエージェントをインストールする][]」をご参照ください。rpmファイルやdebファイルを直接取得したい場合は、[GitHub Releases][]を参照してください。

[Mackerelエージェントをインストールする]: https://mackerel.io/my/instruction-agent
[GitHub Releases]: https://github.com/mackerelio/go-check-plugins/releases


### rpmパッケージの場合

```
sudo yum install mackerel-check-plugins
```

アップデートの際は、以下のコマンドでおこなうことができます。

```
sudo yum update mackerel-check-plugins
```

### debパッケージの場合

```
sudo apt-get install mackerel-check-plugins
```

アップデートの際は、以下のコマンドからおこなうことができます。

```
sudo apt-get update
sudo apt-get install mackerel-check-plugins
```

## 公式チェックプラグイン集をインストールする（Windows Serverの場合）

Windows Server で公式チェックプラグイン集を利用する場合、エージェントに同梱されているプラグインを利用すると便利です。（エージェントのインストール方法については「[Mackerelエージェントをインストールする][]」を参照してください。）

同梱されているプラグイン一覧は、[GitHubリポジトリにある同梱プラグインリスト][]を参照してください。同梱されていないプラグインについては、公式でのサポート対象外となっていることに加えて、ご自身でビルドを実施していただく必要があります。

エージェントをアップデートすることで同梱のプラグインもアップデートされます。アップデート方法については[Windowsにmackerel-agentをインストールする](https://mackerel.io/ja/docs/entry/howto/install-agent/msi)を参照してください。

[Mackerelエージェントをインストールする]: https://mackerel.io/my/instruction-agent
[GitHubリポジトリにある同梱プラグインリスト]: https://github.com/mackerelio/mackerel-agent/blob/master/wix/plugins_windows.go


## 公式プラグイン集を利用する（Linux系OSの場合）

各プラグインは`/usr/bin`にインストールされていますので、mackerel-agentの設定ファイルに利用するプラグインに合わせて以下のような設定を追加してください。設定の反映には、mackerel-agentの再起動が必要です。

プロセス監視プラグインを使ってcrondの監視を行いたい場合は以下のようになります。

```config
[plugin.checks.check_cron]
command = ["check-procs", "-p", "crond"]
```

## 公式プラグイン集を利用する（Windows Serverの場合）

上述のとおり、Windows Server で公式チェックプラグイン集を利用する場合、エージェントに同梱されているプラグインを利用すると便利です。同梱されているプラグインを利用する場合、各プラグインはエージェントのインストールフォルダに格納されており、パスも通っています。mackerel-agentの設定ファイルに利用するプラグインに合わせて以下のような設定を追加してください。設定の反映には、mackerel-agentの再起動が必要です。

プロセス監視プラグインを使ってプロセス名「foobar」の監視を行いたい場合は以下のようになります。

```config
[plugin.checks.check_foobar]
command = ["check-procs", "-p", "foobar"]
```

## 活用例

- [ログ監視をおこなう](https://mackerel.io/ja/docs/entry/howto/check/log)
- [プロセス監視をおこなう](https://mackerel.io/ja/docs/entry/howto/check/process)
- [TCPサーバーの監視をおこなう](https://mackerel.io/ja/docs/entry/howto/check/tcp)

## プラグイン集のコード

公式のプラグイン集は、[https://github.com/mackerelio/go-check-plugins:title] で公開されています。
新しいミドルウェアに対応するプラグインのPull Requestなど、いつでもお待ちしています。

プラグインを作るためには[チェック監視項目を追加する](https://mackerel.io/ja/docs/entry/custom-checks)を参照してください。
