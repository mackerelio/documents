---
Title: CentOS / Red Hat Enterprise Linux派生OSにインストールする
Date: 2014-10-28T17:08:02+09:00
URL: https://mackerel.io/ja/docs/entry/howto/install-agent/rpm
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450070885613
---

<a href="#kcps">KCPS版Mackerelをご利用の場合はこちら</a>

<h2>パッケージをインストールする</h2>

あらかじめ [エージェントのインストール時に発生する通信](https://mackerel.io/ja/docs/entry/howto/install-agent#install) を許可してください。

<h3 id="script">セットアップスクリプトを実行してインストールする</h2>

[新規ホストの登録](https://mackerel.io/my/instruction-agent) 画面で CentOS / Red Hat を選択し、表示されたコマンドを実行します。

```
curl -fsSL https://mackerel.io/file/script/setup-all-yum-v2.sh | MACKEREL_APIKEY='<YOUR_API_KEY>' sh
```

インストール完了後にエージェントは自動で起動します。エージェントが起動するとMackerelにホストとして登録されます。[ホスト一覧](https://mackerel.io/my/hosts) 画面を確認してください。

ホストが登録されない場合は、[FAQ：ホストが登録されない](https://support.mackerel.io/hc/ja/articles/30952244573337) の内容を確認してください。

<h3 id="rpm">rpmコマンドを使用してインストールする</h3>

[GitHub の Release ページ](https://github.com/mackerelio/mackerel-agent/releases) で公開している最新版のパッケージを、rpmコマンドを使用してインストールします。

インストールが完了したら mackerel-agent.conf にAPIキーを設定し、エージェントの再起動を行います。APIキーは [WebコンソールのAPIキータブ](https://mackerel.io/my?tab=apikeys) から取得できます。

```
apikey = "<YOUR_API_KEY>"
```

<h2 id="start-agent">エージェントを再起動する</h2>

エージェントを再起動する場合は以下のコマンドを実行します。

```
sudo systemctl restart mackerel-agent
```

<h2 id="log">エージェントのログを確認する</h2>

エージェントのログはJournalに出力されます。以下のコマンドで確認できます。

```
sudo journalctl -u mackerel-agent.service
```

<h2 id="version-up">エージェントをバージョンアップする</h2>

エージェントをバージョンアップする場合は以下のコマンドを実行します。

```
sudo yum update mackerel-agent
```

<h2 id="uninstall">エージェントをアンインストールする</h2>

エージェントをアンインストールする場合は以下のコマンドを実行します。

```
sudo yum remove mackerel-agent
```


<h1 id="kcps">KCPS版mackerel-agentをRPMパッケージでインストールする</h1>


<h2 id="yum">yumコマンドを使用する</h2>

まずMackerelのyumリポジトリを登録します:

```
curl -fsSL http://198.18.0.16/file/setup-yum-kcps.sh | sh
```

その後、`yum`コマンドを用いてインストールできます:

```
sudo yum install mackerel-agent-kcps
```

エージェントのアップデートも`yum`コマンドから行えます:

```
sudo yum update mackerel-agent-kcps
```

<h2 id="rpm">rpmコマンドを使用する</h2>

`rpm`コマンドでインストールするには、以下のコマンドを実行します:

```
sudo rpm -ivh http://198.18.0.16/file/rpm/mackerel-agent-kcps-latest.noarch.rpm
```

アップデートの際は、以下のコマンドを実行してください:

```
sudo rpm -Uvh http://198.18.0.16/file/rpm/mackerel-agent-kcps-latest.noarch.rpm
```

<h2 id="config">設定ファイルを編集</h2>

`/etc/mackerel-agent-kcps/mackerel-agent-kcps.conf` ファイルを編集して、APIキーを設定してください。

```
apikey = "<YOUR_API_KEY>"
```

APIキーは[オーガニゼーションのページ](https://kcps-mackerel.io/my)から確認できます。このAPIキーでオーガニゼーションを識別しますので、APIキーは外部に漏らさないようご注意ください。

詳細は[mackerel-agent仕様](https://mackerel.io/ja/docs/entry/spec/agent)をご覧ください。

設定ファイルを利用して、以下のことを実現できます:

- [サービス、ロールを設定する](https://mackerel.io/ja/docs/entry/spec/agent#setting-services-and-roles)
- [カスタムメトリックを投稿する](https://mackerel.io/ja/docs/entry/advanced/custom-metrics)
- [チェック監視項目を追加する](https://mackerel.io/ja/docs/entry/custom-checks)

<h2 id="start-agent">エージェントを起動する</h2>

以下のコマンドを実行することでエージェントが起動します。

```
sudo /etc/init.d/mackerel-agent-kcps start
```

`/var/log/mackerel-agent-kcps.log`にエージェントのログが出力されます。

エージェントが正しく動きはじめると、Mackerelにホストとして登録されます。[ダッシュボード](https://kcps-mackerel.io/my/dashboard)などでご確認ください。

<h2 id="uninstall">エージェントのアンインストール</h2>

mackerel-agentをアンインストールするには、以下のコマンドを実行してください。

`yum`でインストールした場合:

```
sudo yum remove mackerel-agent-kcps
```

`rpm`でインストールした場合:

```
sudo rpm -e mackerel-agent-kcps
```

またその際、デフォルトでは `/var/lib/mackerel-agent-kcps/id` にホストIDの記録されたファイルが残っているため、これを削除してください。
