---
Title: Ubuntu / Debian にインストールする
Date: 2014-10-28T17:06:51+09:00
URL: https://mackerel.io/ja/docs/entry/howto/install-agent/deb
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450070885544
---

<p><a href="#kcps">KCPS版Mackerelをご利用の場合はこちら</a></p>

このページに記載の内容は、[Mackerel Web画面・新規ホスト登録画面](https://mackerel.io/my/instruction-agent)からも利用可能です。

<h2 id="v2">aptコマンドを使用する</h2>

以下のコマンドを実行してください:

```
wget -q -O - https://mackerel.io/file/script/setup-all-apt-v2.sh | MACKEREL_APIKEY='<YOUR_API_KEY>' sh
```

APIキーは[オーガニゼーションページ内・APIキータブ](https://mackerel.io/my?tab=apikeys)から確認できます。このAPIキーでオーガニゼーションを識別しますので、APIキーは外部に漏らさないようご注意ください。

アップデートの際は、`apt-get` コマンドからおこなうことができます。

```
sudo apt-get update
sudo apt-get install mackerel-agent
```

<h2 id="dpkg">dpkgコマンドを使用する</h2>

まず、パッケージファイルをダウンロードします:

```
curl -LO https://mackerel.io/file/agent/deb/mackerel-agent_latest.all.deb
```

その後、`dpkg`コマンドを用いてインストールします:

```
sudo dpkg -i mackerel-agent_latest.all.deb
```

アップデートの際は、パッケージファイルをダウンロードしなおしてから同様の手順を実行してください。

<h2 id="config">設定ファイルを編集する</h2>

`/etc/mackerel-agent/mackerel-agent.conf` ファイルを編集して、APIキーを設定してください。

```
apikey = "<YOUR_API_KEY>"
```

APIキーは[オーガニゼーションページ内・APIキータブ](https://mackerel.io/my?tab=apikeys)から確認できます。このAPIキーでオーガニゼーションを識別しますので、APIキーは外部に漏らさないようご注意ください。

詳細は[mackerel-agent仕様](https://mackerel.io/ja/docs/entry/spec/agent)をご覧ください。

設定ファイルを利用して、以下のことを実現できます:

- [サービス、ロールを設定する](https://mackerel.io/ja/docs/entry/spec/agent#setting-services-and-roles)
- [カスタムメトリックを投稿する](https://mackerel.io/ja/docs/entry/advanced/custom-metrics)
- [チェック監視項目を追加する](https://mackerel.io/ja/docs/entry/custom-checks)

<h2 id="start-agent">エージェントを起動する</h2>

以下のコマンドを実行することで、エージェントが起動します。

```
sudo systemctl start mackerel-agent
```

ログはJournalに出力されます。mackerel-agentのログの確認は以下のコマンドでおこなえます。

```
sudo journalctl -u mackerel-agent.service
```

エージェントが正しく動きはじめると、Mackerelにホストとして登録されます。[ダッシュボード](https://mackerel.io/my/dashboard)などでご確認ください。

<h2 id="uninstall">エージェントのアンインストール</h2>

mackerel-agentをアンインストールするには、以下のコマンドを実行してください。

`apt-get`でインストールした場合:

```
sudo apt-get remove mackerel-agent
```

`dpkg`でインストールした場合:

```
sudo dpkg -r mackerel-agent
```

またその際、デフォルトでは `/var/lib/mackerel-agent/id` にホストIDの記録されたファイルが残っているため、これを削除してください。


<h1 id="kcps">KCPS版mackerel-agentをdebパッケージでインストールする</h1>

KCPS向けMackerelではdebパッケージはサポートしておりません。
