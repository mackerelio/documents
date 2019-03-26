---
Title: チェック監視に公式チェックプラグイン集を使う
Date: 2015-11-05T11:49:37+09:00
URL: https://mackerel.io/ja/docs/entry/howto/mackerel-check-plugins
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653458415126984680
---

チェック監視に対応した公式プラグイン集を利用して、プロセス監視やログ監視などのチェック監視を簡単に実現できます。

[:contents]

## 公式チェックプラグイン集をインストールする

インストールにはお使いの環境に合わせてyumリポジトリもしくはaptリポジトリを使うことを推奨しています。リポジトリの設定方法は「[Mackerelエージェントをインストールする][]」をご参照ください。rpmファイルやdebファイルを直接取得したい場合は、[GitHub Releases][]を参照してください。

[Mackerelエージェントをインストールする]: https://mackerel.io/my/instruction-agent
[GitHub Releases]: https://github.com/mackerelio/go-check-plugins/releases


### rpmパッケージの場合

```
yum install mackerel-check-plugins
```

### debパッケージの場合

```
apt-get install mackerel-check-plugins
```

## 公式プラグイン集を利用する

各プラグインは`/usr/bin`にインストールされていますので、mackerel-agentの設定ファイルに利用するプラグインに合わせて以下のような設定を追加してください。設定の反映には、mackerel-agentの再起動が必要です。

プロセス監視プラグインを使ってcrondの監視を行いたい場合は以下のようになります。

```config
[plugin.checks.check_cron]
command = ["check-procs", "-p", "crond"]
```

## 活用例

- [ログ監視をおこなう](https://mackerel.io/ja/docs/entry/howto/check/log)
- [プロセス監視をおこなう](https://mackerel.io/ja/docs/entry/howto/check/process)
- [TCPサーバーの監視をおこなう](https://mackerel.io/ja/docs/entry/howto/check/tcp)

## プラグイン集のコード

公式のプラグイン集は、[https://github.com/mackerelio/go-check-plugins:title] で公開されています。
新しいミドルウェアに対応するプラグインのPull Requestなど、いつでもお待ちしています。

プラグインを作るためには[チェック監視項目を追加する](https://mackerel.io/ja/docs/entry/custom-checks)を参照してください。
