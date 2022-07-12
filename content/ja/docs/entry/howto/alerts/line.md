---
Title: LINE にアラートを通知する
Date: 2016-09-27T14:18:28+09:00
URL: https://mackerel.io/ja/docs/entry/howto/alerts/line
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10328749687186515230
---

[LINE](http://line.me/)は、無料で音声・ビデオ通話・チャットが楽しめるコミュニケーションアプリです。

Mackerel では、[LINE Notify](https://notify-bot.line.me/ja/)と連携することで、Mackerel からのアラート通知を LINE のトークに直接送ることができます。LINE Notify 側で通知先を設定することにより、ユーザー個人宛のトークやグループトークでアラート通知を受け取ることが出来ます。

<span itemscope="" itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160928/20160928141810.png" alt="f:id:mackerelio:20160928141810p:plain" title="f:id:mackerelio:20160928141810p:plain" class="hatena-fotolife" itemprop="image"></span>

## LINE へのアラート通知を追加する

通知の設定は、[チャンネルページのLINEフォーム](https://mackerel.io/my/channels/-/create#line)から、新しいチャンネルを追加してください。

<span itemscope="" itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160928/20160928141848.png" alt="f:id:mackerelio:20160928141848p:plain" title="f:id:mackerelio:20160928141848p:plain" class="hatena-fotolife" itemprop="image"></span>

チャンネルフォームの "LINE で認証してチャンネルを作成" をクリックすると、 LINE Notify 側の認証画面へと遷移します。 LINE Notify にログインしていない場合はログイン画面が表示されるため、ここでログインしてください。

<span itemscope="" itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160928/20160928141903.png" alt="f:id:mackerelio:20160928141903p:plain" title="f:id:mackerelio:20160928141903p:plain" class="hatena-fotolife" itemprop="image"></span>

認証画面で、 LINE Notify の利用に関する注意事項を確認の上、通知を送信するトークルームを選択して "同意して連携する" をクリックすると Mackerel のチャンネル一覧に遷移します。この段階で Mackerel から LINE に通知するチャンネルは作成されています。チャンネル名には通知先のトークルーム名とランダムな文字列を含んだ名前が自動的に設定されるため、必要に応じてチャンネル名を変更してください。

<!-- グループトークに送信するときは、ここで notify bot くんをそのグループトークに招待する必要がある。この辺は将来挙動変わるかもしれない... -->

### 通知先のトークルームについて

LINE への通知チャンネルでは、チャンネルを一度作成した後に通知先のトークルーム名を確認したり変更することはできません。どのトークルームに通知されるか確認したい場合は、作成した通知チャンネルでテスト通知を送信してください。また、通知先のトークルームを変更したい場合は、チャンネルを削除した上で新しくチャンネルを作り直す必要があります。
