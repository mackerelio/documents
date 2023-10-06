---
Title: Red Hat Enterprise Linux派生OSにインストールする
Date: 2014-10-28T17:08:02+09:00
URL: https://mackerel.io/ja/docs/entry/howto/install-agent/rpm
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450070885613
---

- <a href="./amazon-linux">Amazon Linuxをご利用の場合はこちら</a>
- <a href="#kcps">KCPS版Mackerelをご利用の場合はこちら</a>

このページに記載の内容は、[Mackerel Web画面・新規ホスト登録画面](https://mackerel.io/my/instruction-agent)からも利用可能です。

<h2 id="v2">RHEL 8 以降の派生OSをご利用の場合</h2>

以下のコマンドを実行してください:

```
curl -fsSL https://mackerel.io/file/script/setup-all-yum-v2.sh | MACKEREL_APIKEY='<YOUR_API_KEY>' sh
```

APIキーは[オーガニゼーションページ内・APIキータブ](https://mackerel.io/my?tab=apikeys)から確認できます。このAPIキーでオーガニゼーションを識別しますので、APIキーは外部に漏らさないようご注意ください。

アップデートの際は、`yum` コマンドからおこなうことができます。

```
sudo yum update mackerel-agent
```

<h2 id="rpm">rpmコマンドを使用する</h2>

`rpm`コマンドでインストールするには、以下のコマンドを実行します:

```
sudo rpm -ivh https://mackerel.io/file/agent/rpm/mackerel-agent-latest.noarch.rpm
```

アップデートの際は、以下のコマンドを実行してください:

```
sudo rpm -Uvh https://mackerel.io/file/agent/rpm/mackerel-agent-latest.noarch.rpm
```

<h2 id="config">設定ファイルを編集</h2>

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
<h3>RHEL 8 以降の派生OSをご利用の場合</h3>

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

`yum`でインストールした場合:

```
sudo yum erase mackerel-agent
```

`rpm`でインストールした場合:

```
sudo rpm -e mackerel-agent
```

またその際、デフォルトでは `/var/lib/mackerel-agent/id` にホストIDの記録されたファイルが残っているため、これを削除してください。


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
sudo yum erase mackerel-agent-kcps
```

`rpm`でインストールした場合:

```
sudo rpm -e mackerel-agent-kcps
```

またその際、デフォルトでは `/var/lib/mackerel-agent-kcps/id` にホストIDの記録されたファイルが残っているため、これを削除してください。
