---
Title: Ubuntu / Debian にインストールする
Date: 2014-10-28T17:06:51+09:00
URL: https://mackerel.io/ja/docs/entry/howto/install-agent/deb
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450070885544
---

<h2>パッケージをインストールする</h2>

あらかじめ [エージェントのインストール時に発生する通信](https://mackerel.io/ja/docs/entry/howto/install-agent#install) を許可してください。

<h3 id="script">セットアップスクリプトを実行してインストールする</h2>

[新規ホストの登録](https://mackerel.io/my/instruction-agent) 画面で Ubuntu / Debian を選択し、表示されたコマンドを実行します。

```
wget -q -O - https://mackerel.io/file/script/setup-all-apt-v2.sh | MACKEREL_APIKEY='<YOUR_API_KEY>' sh
```

インストール完了後にエージェントは自動で起動します。エージェントが起動するとMackerelにホストとして登録されます。[ホスト一覧](https://mackerel.io/my/hosts) 画面を確認してください。

ホストが登録されない場合は、[FAQ：ホストが登録されない](https://support.mackerel.io/hc/ja/articles/30952244573337) の内容を確認してください。

<h3 id="rpm">dpkgコマンドを使用してインストールする</h3>

[GitHub の Release ページ](https://github.com/mackerelio/mackerel-agent/releases) で公開している最新版のパッケージを、dpkgコマンドを使用してインストールします。

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
sudo apt-get update
sudo apt-get install mackerel-agent
```

<h2 id="uninstall">エージェントをアンインストールする</h2>

エージェントをアンインストールする場合は以下のコマンドを実行します。

```
sudo apt-get remove mackerel-agent
```

エージェントの設定ファイルや idファイルも含めて完全に削除する場合は以下のコマンドを実行します。

```
sudo apt-get purge mackerel-agent
```
