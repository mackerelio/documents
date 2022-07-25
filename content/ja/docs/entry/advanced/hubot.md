---
Title: Hubotでチャットにアラートを通知する
Date: 2014-09-06T00:12:16+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/hubot
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815732107358
---

注）本ページで紹介している[hubot-mackerel-notifier][]は現在メンテナンスしておらず、**非推奨**です。

[Hubot][]スクリプトの[hubot-mackerel-notifier][]を利用すると、Mackerelからのアラート通知のWebhookを受けとり、IRCやSlack, HipChatなどのチャットツールに次のような通知を流すことができます。(SlackにはHubot経由ではなく直接送ることもできます。詳しくは[Slackにアラートを通知する](https://mackerel.io/ja/docs/entry/howto/alerts/slack)を参照してください。)

```
17:06 hubot: [Mackerel] CRITICAL: IOwait at app01 (working) Service: app https://mackerel.io/orgs/.../alerts/...
```
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20140915/20140915165010.png?1410767426)

またホスト名に反応してホスト詳細ページへのリンクを表示させることができます。
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20140915/20140915165009.png?1410767427)

Webhookのリクエスト先URLのパスは`/hubot/mackerel`を設定してください。例えば、hubotを`http://some-hubot.herokuapp.com/`にセットアップしている場合、WebhookのURLは`http://some-hubot.herokuapp.com/hubot/mackerel`とします。
WebhookのURL設定の詳細については、[監視・通知を設定する][]を参照してください。

Hubot本体の詳細やインストール方法については、[公式ドキュメント][]や[解説記事][]などを参照してください。

## Hubotにhubot-mackerel-notifierを追加する

* hubot-mackerel-notifierをインストールします。
```
npm install hubot-mackerel-notifier --save
```

* `hubot-mackerel-notifier`を`external-scripts.json`に次のように追加します。
```
["hubot-mackerel-notifier"]
```

* リポジトリにコミットします。
```
git commit -a -m 'add hubot-mackerel-notifier'
git push
```

* 環境変数を設定します。例（Herokuの場合）:
```
heroku config:add HUBOT_MACKEREL_API_KEY="..."
heroku config:add HUBOT_MACKEREL_HOST_REGEXP="\\b[a-zA-Z0-9._-]+[.]local-domain\\b"
heroku config:add HUBOT_MACKEREL_ORG_NAME="example"
heroku config:add HUBOT_MACKEREL_NOTIFIER_ROOM="#general"
```

* Hubotにデプロイします。例（Herokuの場合）:
```
git push heroku master
```

[Hubot]: https://hubot.github.com/
[hubot-mackerel-notifier]: https://github.com/mackerelio/hubot-mackerel-notifier
[監視・通知を設定する]: https://mackerel.io/ja/docs/entry/howto/alerts
[公式ドキュメント]: https://github.com/github/hubot/tree/master/docs
[解説記事]: http://gihyo.jp/dev/serial/01/hubot/0001

* Enjoy!!
