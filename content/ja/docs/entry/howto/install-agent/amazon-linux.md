---
Title: Amazon Linuxにmackerel-agentをインストールする
Date: 2016-05-20T15:09:49+09:00
URL: https://mackerel.io/ja/docs/entry/howto/install-agent/amazon-linux
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653812171397064073
---

このページに記載の内容は、[Mackerel Web画面・新規ホスト登録画面](https://mackerel.io/my/instruction-agent)からも利用可能です。

<h2 id="v2">Amazon Linux 2 LTS をご利用の場合</h2>

以下のコマンドを実行してください:

```
curl -fsSL https://mackerel.io/file/script/amznlinux/setup-all-yum-v2.sh | MACKEREL_APIKEY='<YOUR_API_KEY>' sh
```

APIキーは[オーガニゼーションページ内・APIキータブ](https://mackerel.io/my?tab=apikeys)から確認できます。このAPIキーでオーガニゼーションを識別しますので、APIキーは外部に漏らさないようご注意ください。

<h2 id="v1">Amazon Linux をご利用の場合</h2>

以下のコマンドを実行してください:

```
curl -fsSL https://mackerel.io/file/script/amznlinux/setup-all-yum.sh | MACKEREL_APIKEY='<YOUR_API_KEY>' sh
```

APIキーは[オーガニゼーションページ内・APIキータブ](https://mackerel.io/my?tab=apikeys)から確認できます。このAPIキーでオーガニゼーションを識別しますので、APIキーは外部に漏らさないようご注意ください。

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
<h3>Amazon Linux 2 LTS をご利用の場合</h3>

以下のコマンドを実行することで、エージェントが起動します。

```
sudo systemctl start mackerel-agent
```

ログはJournalに出力されます。mackerel-agentのログの確認は以下のコマンドでおこなえます。

```
sudo journalctl -u mackerel-agent.service
```

エージェントが正しく動きはじめると、Mackerelにホストとして登録されます。[ダッシュボード](https://mackerel.io/my/dashboard)などでご確認ください。

<h3>Amazon Linux をご利用の場合</h3>

以下のコマンドを実行することで、エージェントが起動します。

```
sudo /sbin/service mackerel-agent start
```

`/var/log/mackerel-agent.log` にエージェントのログが出力されます。

エージェントが正しく動きはじめると、Mackerelにホストとして登録されます。[ダッシュボード](https://mackerel.io/my/dashboard)などでご確認ください。


<h2 id="uninstall">エージェントのアンインストール</h2>

mackerel-agentをアンインストールするには、以下のコマンドを実行してください。


```
sudo yum erase mackerel-agent
```

またその際、デフォルトでは `/var/lib/mackerel-agent/id` にホストIDの記録されたファイルが残っているため、これを削除してください。
