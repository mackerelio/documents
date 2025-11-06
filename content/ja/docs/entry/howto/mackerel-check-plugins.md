---
Title: チェック監視に公式チェックプラグイン集を使う
Date: 2015-11-05T11:49:37+09:00
URL: https://mackerel.io/ja/docs/entry/howto/mackerel-check-plugins
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653458415126984680
---

チェック監視に対応した公式のプラグインを利用して、プロセス監視やログ監視などのチェック監視を簡単に実現できます。

公式チェックプラグイン集に含まれるプラグインは下記を参照してください。

[https://mackerel.io/ja/docs/entry/plugins/check-plugins-list:embed:cite]

[:contents]

## 公式チェックプラグイン集をインストールする

### Linux系OSの場合

インストールにはお使いの環境に合わせてyumリポジトリもしくはaptリポジトリを使うことを推奨しています。リポジトリは [新規ホストの登録](https://mackerel.io/my/instruction-agent) を行う際に設定されます。rpmファイルやdebファイルを直接取得したい場合は、[GitHub Releases](https://github.com/mackerelio/go-check-plugins/releases) を参照してください。


#### rpmパッケージの場合

```
sudo yum install mackerel-check-plugins
```

アップデートの際は、以下のコマンドでおこなうことができます。

```
sudo yum update mackerel-check-plugins
```

#### debパッケージの場合

```
sudo apt-get install mackerel-check-plugins
```

アップデートの際は、以下のコマンドからおこなうことができます。

```
sudo apt-get update
sudo apt-get install mackerel-check-plugins
```

### Windows Serverの場合

公式プラグインはエージェントに同梱されます。同梱されているプラグインは [プラグイン一覧](https://mackerel.io/ja/docs/entry/plugins/check-plugins-list) を参照してください。エージェントのインストール方法については [新規ホストの登録](https://mackerel.io/my/instruction-agent) を参照してください。

エージェントをアップデートすることで同梱のプラグインもアップデートされます。アップデート方法については [Windowsにmackerel-agentをインストールする](https://mackerel.io/ja/docs/entry/howto/install-agent/msi) を参照してください。

同梱されていないプラグインについては、公式でのサポート対象外となっていることに加えて、ご自身でビルドを実施していただく必要があります。


## 公式チェックプラグイン集を利用する

各プラグインの利用方法の詳細は、[プラグイン一覧](https://mackerel.io/ja/docs/entry/plugins/check-plugins-list) のドキュメントを参照してください。

### Linux系OSの場合

各プラグインは`/usr/bin`にインストールされていますので、mackerel-agentの設定ファイルに利用するプラグインに合わせて以下のような設定を追加してください。設定の反映には、mackerel-agentの再起動が必要です。

プロセス監視プラグインを使ってcrondの監視を行いたい場合は以下のようになります。

```toml
[plugin.checks.check_cron]
command = ["check-procs", "-p", "crond"]
```

### Windows Serverの場合

各プラグインはエージェントのインストールフォルダに格納されており、パスも通っています。mackerel-agentの設定ファイルに利用するプラグインに合わせて以下のような設定を追加してください。設定の反映には、mackerel-agentの再起動が必要です。

プロセス監視プラグインを使ってプロセス名「foobar」の監視を行いたい場合は以下のようになります。

```toml
[plugin.checks.check_foobar]
command = ["check-procs", "-p", "foobar"]
```


## 活用例

- [ログ監視をおこなう](https://mackerel.io/ja/docs/entry/howto/check/log)
- [プロセス監視をおこなう](https://mackerel.io/ja/docs/entry/howto/check/process)
- [TCPサーバーの監視をおこなう](https://mackerel.io/ja/docs/entry/howto/check/tcp)

## 公式チェックプラグイン集のコード

公式チェックプラグイン集は [GitHub](https://github.com/mackerelio/go-check-plugins) で公開しています。
新しいミドルウェアに対応するプラグインのPull Requestなど、いつでもお待ちしています。

プラグインを作るためには [チェック監視項目を追加する](https://mackerel.io/ja/docs/entry/custom-checks) を参照してください。
