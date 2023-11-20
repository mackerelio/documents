---
Title: Typetalkにアラートを通知する
Date: 2014-12-11T15:05:44+09:00
URL: https://mackerel.io/ja/docs/entry/howto/alerts/typetalk
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450076122607
---

**Typetalkは2025年12月1日（月）をもってサービス終了となります。詳細については以下のお知らせをご確認ください。**

- [【重要】Typetalkサービス終了のお知らせ | 株式会社ヌーラボ(Nulab inc.)](https://nulab.com/ja/blog/typetalk/announcement-close-of-typetalk/)

---

[Typetalk（タイプトーク）](http://www.typetalk.com)は楽しく簡単にメッセージのやりとりができるチーム向けコミュニケーションツールです。

Mackerel上でTypetalkチャンネルを登録すると、Mackerelで監視しているホストのアラート情報を適宜チャットに流す事が出来ます。重大なアラートが発生した際にもリアル
タイムにその情報を受け取る事が出来ます。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150507/20150507163216.png)

### Typetalk との連携を追加する
連携の設定は、[チャンネルページのTypetalkフォーム](https://mackerel.io/my/channels/-/create#typetalk)から新しい連携を追加してください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170919/20170919114420.png)

Typetalkへの通知には、TypetalkのBot APIの機能を利用します。

チャンネルの登録には、TypetalkAPIへのトークンつきのURLが必要になります。URLは以下の方法で取得してください。

#### トークンの取得

Typetalkにログインし、通知を行いたいTopicの設定ページに入ります。

次にTopicの設定画面で、"ボット"のタブをクリックし、"新規追加"でMackerelの通知用botを登録します。

必要事項を記入します。このとき、ボットに対してメッセージを投稿可能な権限を付与してください。

次の画面で、投稿用URLとTypetalk Tokenが取得できます。

この投稿用URLと、Tokenを以下のように組み合わせたものを、Mackerelの設定画面に登録してください。

```
https://typetalk.com/api/v1/topics/{取得したURLに記載のID}?typetalkToken={取得したToken}
```

※Typetalk側の詳細な設定についてご不明の場合は、Typetalkのサポートにお問い合わせください

### オプション機能:プレフィックスについて

Typetalkに通知する際、ステータスに応じて任意のプレフィックスを先頭に付与できます。
（未設定の場合はプレフィックスはつきません）

チャンネル設定の際、下記入力欄に、ステータスごとの絵文字など、わかりやすいプレフィックスをつけてください。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141210/20141210110603.png" alt="f:id:mackerelio:20141210110603p:plain" title="f:id:mackerelio:20141210110603p:plain" class="hatena-fotolife" itemprop="image"></span></p>

このように設定すると…

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141210/20141210110619.png" alt="f:id:mackerelio:20141210110619p:plain" title="f:id:mackerelio:20141210110619p:plain" class="hatena-fotolife" itemprop="image"></span></p>

このように先頭に":smile:"の絵文字がついて通知されます。
