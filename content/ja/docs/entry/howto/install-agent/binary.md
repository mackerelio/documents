---
Title: バイナリファイルで直接mackerel-agentをインストールする
Date: 2014-10-28T17:05:24+09:00
URL: https://mackerel.io/ja/docs/entry/howto/install-agent/binary
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450070885461
---

<h2 id="download">アーカイブをダウンロードする</h2>

[GitHub の Release ページ](https://github.com/mackerelio/mackerel-agent/releases) からお使いのアーキテクチャ向けの tar.gz ファイルをダウンロードします。

<h2 id="config">設定ファイルにAPIキーを記述する</h2>

taf.gz ファイルを展開し、`mackerel-agent.conf` ファイルを編集してAPIキーを設定します。APIキーは [WebコンソールのAPIキータブ](https://mackerel.io/my?tab=apikeys) から取得できます。

```
apikey = "<YOUR_API_KEY>"
```

<h2 id="start">エージェントを起動する</h2>

編集した設定ファイルを指定して、`mackerel-agent`を起動します。

```
./mackerel-agent --conf=./mackerel-agent.conf
```

エージェントはフォアグラウンドで起動しますので、ご利用の環境にあわせてサービス化してください。

<h2 id="uninstall">エージェントをアンインストールする</h2>

mackerel-agentをアンインストールするには、ダウンロードしたバイナリファイルを削除してください。
