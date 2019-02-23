---
Title: バイナリファイルで直接mackerel-agentをインストールする
Date: 2014-10-28T17:05:24+09:00
URL: https://mackerel.io/ja/docs/entry/howto/install-agent/binary
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450070885461
---

<p><a href="#kcps">KCPS版Mackerelをご利用の場合はこちら</a></p>

<h2 id="download">アーカイブをダウンロードする</h2>

[mackerel-agent-latest.tar.gz](https://mackerel.io/file/agent/tgz/mackerel-agent-latest.tar.gz)をダウンロードします:

```
curl -LO https://mackerel.io/file/agent/tgz/mackerel-agent-latest.tar.gz
tar xvzf mackerel-agent-latest.tar.gz
cd mackerel-agent
```

アーカイブ中の`mackerel-agent`がLinuxで動作するバイナリです。

<h2 id="config">設定ファイルを記述する</h2>

`mackerel-agent.conf` ファイルを編集して、APIキーを設定してください。

```
apikey = "<YOUR_API_KEY>"
```

APIキーは[オーガニゼーションのページ](https://mackerel.io/my)から確認できます。このAPIキーでオーガニゼーションを識別しますので、APIキーは外部に漏らさないようご注意ください。

詳細は[mackerel-agent仕様](https://mackerel.io/ja/docs/entry/spec/agent)をご覧ください。

設定ファイルを利用して、以下のことを実現できます:

- [サービス、ロールを設定する](https://mackerel.io/ja/docs/entry/spec/agent#setting-services-and-roles)
- [カスタムメトリックを投稿する](https://mackerel.io/ja/docs/entry/advanced/custom-metrics)

<h2 id="start">エージェントを起動する</h2>

編集した設定ファイルを指定して、`mackerel-agent`を起動します:

```
./mackerel-agent --conf=./mackerel-agent.conf
```

エージェントはフォアグラウンドで起動しますので、ご利用の環境にあわせてサービス化してください。

<h2 id="uninstall">エージェントのアンインストール</h2>

mackerel-agentをアンインストールするには、ダウンロードしたバイナリファイルを削除してください。

またその際、デフォルトでは `/var/lib/mackerel-agent/id` にホストIDの記録されたファイルが残っているため、これを削除してください。

<h1 id="kcps">KCPS版mackerel-agentをバイナリファイルでインストールする</h1>

KCPS向けMackerelではバイナリファイルはサポートしておりません。
