---
Title: Microsoft Teamsにアラートを通知する
Date: 2019-02-07T11:38:56+09:00
URL: https://mackerel.io/ja/docs/entry/howto/alerts/microsoft-teams
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/98012380860278659
---

[Microsoft Teams](https://teams.microsoft.com)は、Office 365 でチームワークを実現するためのハブです。

通知のMicrosoft Teamsチャンネルを利用すると、Mackerelからのアラート通知を Microsoft Teamsのチャンネルに送ることができます。

### Microsoft Teamsへのアラート通知を追加する

**2024年8月現在、 Microsoft Teams の制限によりプライベートチャンネルに通知を送ることができません。**
**Microsoft によって対応中で、将来的に利用可能になる見込みとのことですので、それまでお待ちください。**

Microsoft Teams の 「アプリ」、「ワークフロー」、「通知」を順にクリックし、「Webhook 要求を受信するとチャネルに投稿する」を選択します。
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240821/20240821100853.png" alt="f:id:mackerelio:20240821100853:plain" title="f:id:mackerelio:20240821100853:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>

ワークフローに任意の名前を入力し、接続が有効であることを確認します。
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240821/20240821100905.png" alt="f:id:mackerelio:20240821100905:plain" title="f:id:mackerelio:20240821100905:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>

通知を送信したいチームとチャンネルを選択します。
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240821/20240821100913.png" alt="f:id:mackerelio:20240821100913:plain" title="f:id:mackerelio:20240821100913:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>

コピーボタンをクリックして Webhook の URL をコピーします。
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240821/20240821100922.png" alt="f:id:mackerelio:20240821100922:plain" title="f:id:mackerelio:20240821100922:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>

Mackerel の通知チャンネル設定画面から Microsoft Teams のチャンネルを選択し、 URL に前の手順でコピーした Webhook の URL を指定します。
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240821/20240821100929.png" alt="f:id:mackerelio:20240821100929:plain" title="f:id:mackerelio:20240821100929:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>

テスト通知を送信すると、次のように確認できます。
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240821/20240821101923.png" alt="f:id:mackerelio:20240821101923:plain" title="f:id:mackerelio:20240821101923:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>

アラートが発報されると、次のように通知が送信されます。
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240827/20240827121141.png" alt="f:id:mackerelio:20240827121141:plain" title="f:id:mackerelio:20240827121141:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>

また下記のように監視ルールやアラートのメモには一部の Markdown 記法を使用できます。
加えて `<at>****@****.onmicrosoft.com</at>` のようにユーザー識別子を指定することでメンションを送ることも可能です。
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240821/20240821113932.png" alt="f:id:mackerelio:20240821113932:plain" title="f:id:mackerelio:20240821113932:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240821/20240821115717.png" alt="f:id:mackerelio:20240821115717:plain" title="f:id:mackerelio:20240821115717:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>

### 【廃止】 Incoming Webhook で Microsoft Teams へのアラート通知を追加する

**Microsoft は Incoming Webhook の段階的な廃止を発表し、2024年8月15日をもって新規に Incoming Webhook を作成することができなくなりました。**
**以前からお使いの Incoming Webhook については、2025年1月31日までに Microsoft から案内される延長手続きを行うことで、2025年12月31日まで継続してご利用いただくことが可能です。**
**詳細については [Retirement of Office 365 connectors within Microsoft Teams](https://devblogs.microsoft.com/microsoft365dev/retirement-of-office-365-connectors-within-microsoft-teams/) をご覧ください。**

**またこちらの方式では、通知内で `<at>` を指定してもメンションを送ることはできません。**

Microsoft Teamsのチャンネルのコネクタをクリックし、Incoming Webhookを作成します。
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190207/20190207160507.png" alt="f:id:mackerelio:20190207160507:plain" title="f:id:mackerelio:20190207160507:plain" class="hatena-fotolife" itemprop="image" width="400"></span></p>
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190207/20190207160532.png" alt="f:id:mackerelio:20190207160532:plain" title="f:id:mackerelio:20190207160532:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>
画像をカスタマイズするには [ブランド](https://mackerel.io/ja/brand-assets/) のPNG画像をご利用ください。

作成後のURLを、[Mackerelの設定画面](https://mackerel.io/my/channels/-/create#microsoft-teams)で指定してチャンネルを作成します。
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190207/20190207161154.png" alt="f:id:mackerelio:20190207161154:plain" title="f:id:mackerelio:20190207161154:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>
テスト通知を送信すると次のように確認できます。
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190207/20190207161808.png" alt="f:id:mackerelio:20190207161808:plain" title="f:id:mackerelio:20190207161808:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>
アラートが発報されると、次のように通知が送信されます。
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190207/20190207172325.png" alt="f:id:mackerelio:20190207172325:plain" title="f:id:mackerelio:20190207172325:plain" class="hatena-fotolife" itemprop="image" width="600"></span></p>

