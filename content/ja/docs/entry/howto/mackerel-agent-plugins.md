---
Title: ミドルウェアのメトリック可視化に公式プラグイン集を使う
Date: 2014-09-26T18:10:26+09:00
URL: https://mackerel.io/ja/docs/entry/howto/mackerel-agent-plugins
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450066401130
---

各種ミドルウェアに対応した公式のプラグインを利用して簡単にミドルウェアのメトリックを収集できます。動作確認環境は、`mackerel-agent`と同一ですので、[Mackerelの概説の動作環境](https://mackerel.io/ja/docs/entry/overview#support-environments)を参照してください。

AWS ELB・AWS RDSなど、`mackerel-agent`をインストールできないAWSクラウド環境は[AWSインテグレーション](https://mackerel.io/ja/docs/entry/integrations/aws)をご利用ください。サービスメトリックとして投稿するためには、[fluentd](http://www.fluentd.org/)をご利用ください。詳しくは「[AWSのCloudWatchのメトリックからELB関連のメトリックをサービスメトリックに投稿する](https://mackerel.io/ja/docs/entry/advanced/fluentd#example-elb)」を参照してください。

公式プラグイン集に含まれるプラグインは下記を参照してください。

[https://mackerel.io/ja/docs/entry/plugins/metric-plugins-list:embed:cite]

[:contents]

## 公式プラグイン集をインストールする

### Linux系OSの場合

インストールにはお使いの環境に合わせてyumリポジトリもしくはaptリポジトリを使うことを推奨しています。リポジトリは [新規ホストの登録](https://mackerel.io/my/instruction-agent) を行う際に設定されます。rpmファイルやdebファイルを直接取得したい場合は、[GitHub Releases](https://github.com/mackerelio/mackerel-agent-plugins/releases) を参照してください。

#### rpmパッケージの場合

```
sudo yum install mackerel-agent-plugins
```

アップデートの際は、以下のコマンドでおこなうことができます。

```
sudo yum update mackerel-agent-plugins
```

#### debパッケージの場合

```
sudo apt-get install mackerel-agent-plugins
```

アップデートの際は、以下のコマンドからおこなうことができます。

```
sudo apt-get update
sudo apt-get install mackerel-agent-plugins
```

### Windows Serverの場合

公式プラグインはエージェントに同梱されます。同梱されているプラグインは [プラグイン一覧](https://mackerel.io/ja/docs/entry/plugins/metric-plugins-list) を参照してください。エージェントのインストール方法については [新規ホストの登録](https://mackerel.io/my/instruction-agent) を参照してください。

エージェントをアップデートすることで同梱のプラグインもアップデートされます。アップデート方法については [Windowsにmackerel-agentをインストールする](https://mackerel.io/ja/docs/entry/howto/install-agent/msi) を参照してください。

同梱されていないプラグインについては、公式でのサポート対象外となっていることに加えて、ご自身でビルドを実施していただく必要があります。

## 公式プラグイン集を利用する

各プラグインの利用方法の詳細は、[プラグイン一覧](https://mackerel.io/ja/docs/entry/plugins/metric-plugins-list) のドキュメントを参照してください。

### Linux系OSの場合

各プラグインは`/usr/bin`にインストールされていますので、mackerel-agentの設定ファイルに利用するプラグインに合わせて以下のような設定を追加してください。設定の反映には、mackerel-agentの再起動が必要です。

Apache2用プラグインの場合
```toml
[plugin.metrics.apache2]
command = ["mackerel-plugin-apache2"]
```


### Windows Serverの場合

各プラグインはエージェントのインストールフォルダに格納されており、パスも通っています。mackerel-agentの設定ファイルに利用するプラグインに合わせて以下のような設定を追加してください。設定の反映には、mackerel-agentの再起動が必要です。

「mackerel-plugin-mssql」プラグインを使う場合は以下のようになります。

```toml
[plugin.metrics.mssql]
command = ["mackerel-plugin-mssql"]
```

## 公式プラグイン集のコード
公式プラグイン集は [GitHub](https://github.com/mackerelio/mackerel-agent-plugins) で公開しています。
新しいミドルウェアに対応するプラグインのPull Requestなど、いつでもお待ちしています。

プラグインを作るためには [ホストのカスタムメトリックを投稿する](https://mackerel.io/ja/docs/entry/advanced/custom-metrics) を参照してください。
