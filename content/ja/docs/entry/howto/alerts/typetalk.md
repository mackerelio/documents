---
Title: Typetalkにアラートを通知する
Date: 2014-12-11T15:05:44+09:00
URL: https://mackerel.io/ja/docs/entry/howto/alerts/typetalk
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450076122607
---

[Typetalk（タイプトーク）](http://www.typetalk.com)は楽しく簡単にメッセージのやりとりができるチーム向けコミュニケーションツールです。

Mackerel上でTypetalkチャンネルを登録すると、Mackerelで監視しているホストのアラート情報を適宜チャットに流す事が出来ます。重大なアラートが発生した際にもリアル
タイムにその情報を受け取る事が出来ます。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150507/20150507163216.png)

### Typetalk との連携を追加する
連携の設定は、[チャンネルページのTypetalkフォーム](https://mackerel.io/my/channels?new=typetalk)から新しい連携を追加してください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170919/20170919114420.png)

Typetalkへの通知には、TypetalkのBot APIの機能を利用します。

チャンネルの登録には、TypetalkAPIへのトークンつきのURLが必要になります。URLは以下の方法で取得してください。

#### トークンの取得

Typetalkにログインし、通知を行いたいTopicの編集ボタンをクリックします。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141210/20141210105856.png" alt="f:id:mackerelio:20141210105856p:plain" title="f:id:mackerelio:20141210105856p:plain" class="hatena-fotolife" itemprop="image"></span></p>

この画面の右上にある。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141204/20141204180748.png" alt="f:id:mackerelio:20141204180748p:plain" title="f:id:mackerelio:20141204180748p:plain" class="hatena-fotolife" itemprop="image"></span></p>

このマークです。

次にTopicの編集画面で、"Bots"のタブをクリックし、"Add bot"でMackerelの通知用botを登録します。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141210/20141210110125.png" alt="f:id:mackerelio:20141210110125p:plain" title="f:id:mackerelio:20141210110125p:plain" class="hatena-fotolife" itemprop="image"></span></p>

次にbotの詳細情報を入力する画面が表示されるので、任意のID, Fullnameを入力。"API Scope"の "topic.post"にチェックをつけ、登録してください。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141210/20141210110323.png" alt="f:id:mackerelio:20141210110323p:plain" title="f:id:mackerelio:20141210110323p:plain" class="hatena-fotolife" itemprop="image"></span></p>

すると画面下部にTokenが表示されますので、"Get or post messages URL"の値をコピーし、Mackerel側に登録してください。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141210/20141210110442.png" alt="f:id:mackerelio:20141210110442p:plain" title="f:id:mackerelio:20141210110442p:plain" class="hatena-fotolife" itemprop="image"></span></p>


### オプション機能:プレフィックスについて

Typetalkに通知する際、ステータスに応じて任意のプレフィックスを先頭に付与することができます。
（未設定の場合はプレフィックスはつきません）

チャンネル設定の際、下記入力欄に、ステータスごとの絵文字など、わかりやすいプレフィックスをつけてください。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141210/20141210110603.png" alt="f:id:mackerelio:20141210110603p:plain" title="f:id:mackerelio:20141210110603p:plain" class="hatena-fotolife" itemprop="image"></span></p>

このように設定すると…

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141210/20141210110619.png" alt="f:id:mackerelio:20141210110619p:plain" title="f:id:mackerelio:20141210110619p:plain" class="hatena-fotolife" itemprop="image"></span></p>

このように先頭に":smile:"の絵文字がついて通知されます。
