---
Title: macOSにmackerel-agentをインストールする
Date: 2017-10-19T16:48:24+09:00
URL: https://mackerel.io/ja/docs/entry/howto/install-agent/mac
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8599973812309432945
---

**macOSでのご利用は公式サポートの対象外です。**

Mackerel では macOS 向けの mackerel-agent の [Homebrew](https://brew.sh/) Tap を提供しています。以下の手順で macOS マシンの監視を行うことが可能です。

ただし、公式サポート対象とはなっておりませんので、使用上のご質問等にお答えできかねます。なお、サポート対象OSについては[こちら](https://mackerel.io/ja/docs/entry/overview)をご確認ください。

<h2 id="install-command">mackerel-agent のインストール</h2>

Homebrew の予め導入された環境で、以下のコマンドを実行してエージェントをインストールできます。

```
brew install mackerelio/mackerel-agent/mackerel-agent
```

<h2 id="config">設定ファイルを編集</h2>

Homebrew でインストールしたエージェントの設定ファイルは `$(brew --prefix)/etc/mackerel-agent.conf` に配置されます。
エージェントを起動する前に、設定ファイルにAPIキーを設定する必要があります。 APIキーの設定は以下のコマンドでおこなえます。

```
mackerel-agent init -apikey="<YOUR_API_KEY>" -conf "$(brew --prefix)/etc/mackerel-agent.conf"
```

APIキーは[オーガニゼーションのページ](https://mackerel.io/my)から確認できます。このAPIキーでオーガニゼーションを識別しますので、APIキーは外部に漏らさないようご注意ください。

詳細は[mackerel-agent仕様](https://mackerel.io/ja/docs/entry/spec/agent)をご覧ください。

設定ファイルを利用して、以下のことを実現できます:

- [サービス、ロールを設定する](https://mackerel.io/ja/docs/entry/spec/agent#setting-services-and-roles)
- [カスタムメトリックを投稿する](https://mackerel.io/ja/docs/entry/advanced/custom-metrics)

<h2 id="start-agent">エージェントを起動/停止する</h2>

エージェントの起動や終了を行うには、それぞれ以下のコマンドを実行します。

```
brew services start mackerel-agent
```
```
brew services stop mackerel-agent
```

エージェントのログは `$(brew --prefix)/var/log/mackerel-agent.log` に出力されます。

エージェントが正しく動きはじめると、Mackerelにホストとして登録されます。[ダッシュボード](https://mackerel.io/my/dashboard)などでご確認ください。
