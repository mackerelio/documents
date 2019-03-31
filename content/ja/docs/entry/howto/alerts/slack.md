---
Title: Slackにアラートを通知する
Date: 2014-10-03T12:00:40+09:00
URL: https://mackerel.io/ja/docs/entry/howto/alerts/slack
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450067077655
---

[Slack](https://slack.com/)はリアルタイムコミュニケーションを加速させるチャットサービスです。

通知のSlackチャンネルを利用すると、Mackerelからのアラート通知をSlackのチャットへ直接送れます。Slackを利用されている方は、普段のコミュニケーションの中でMackerelからのアラート通知を閲覧できます。


<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150409/20150409174311.png" alt="f:id:mackerelio:20150409174311p:plain" title="f:id:mackerelio:20150409174311p:plain" class="hatena-fotolife cboxElement" itemprop="image"></span></p>

通知内容には、アラートへのリンクだけでなく、原因となるホストの属するサービスやロールへのリンクや関連するグラフも含まれます。

### Slackへのアラート通知を追加する

通知の設定は、[チャンネルページのSlackフォーム](https://mackerel.io/my/channels?new=slack)から、新しいチャンネルを追加してください。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190204/20190204181058.png" alt="f:id:mackerelio:20190129183300p:plain" title="f:id:mackerelio:20190129183300p:plain" class="hatena-fotolife" itemprop="image"></span></p>

Slackチャンネルへの通知には、Slack APIの1つである[Incoming Webhooks](https://my.slack.com/services/new/incoming-webhook)を利用しています。URLには、以下の方法で取得した値を入力して下さい。

- Configure Integrationsをクリック
  - <p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141003/20141003120426.png" alt="f:id:mackerelio:20141003120426p:plain" title="f:id:mackerelio:20141003120426p:plain" class="hatena-fotolife" itemprop="image"></span></p>
- "All Services" のタブから、Incoming WebHooks の "Add" をクリック
  - <p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/motemen/20141029/20141029155413.png" alt="f:id:motemen:20141029155413p:plain" title="f:id:motemen:20141029155413p:plain" class="hatena-fotolife" itemprop="image"></span></p>
- 通知を送るSlackのchannel、もしくはprivate groupを選択
  - <p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/motemen/20141029/20141029155439.png" alt="f:id:motemen:20141029155439p:plain" title="f:id:motemen:20141029155439p:plain" class="hatena-fotolife" itemprop="image"></span></p>
- 画面中央下部、"Integration Settings" の項目から、 "Webhook URL" を取得
  - <p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/motemen/20141029/20141029155456.png" alt="f:id:motemen:20141029155456p:plain" title="f:id:motemen:20141029155456p:plain" class="hatena-fotolife" itemprop="image"></span></p>

また、MackerelからSlackへのアラート通知を送る際に、監視のステータスに応じて自由にテキストを追記する事も可能です。@channel/@group/@here/@everyoneはSlack向けのコマンドに変換されます。メンションを含んだテキストを送るように設定しておくと、Slackアプリなどですぐに通知を受信する事が出来ます。

Slack互換のソフトウェアに関しても、こちらのSlackチャンネルをご利用いただけます（動作保証対象外）。Slack互換のソフトウェアには例えば <a href="http://www.mattermost.org/" target="_blank">Mattermost</a> があります。

どうぞご利用下さい。
