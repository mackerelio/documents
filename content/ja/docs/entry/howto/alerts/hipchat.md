---
Title: HipChatに通知を行う
Date: 2014-10-29T16:02:34+09:00
URL: https://mackerel.io/ja/docs/entry/howto/alerts/hipchat
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450070986285
---

[HipChat](https://www.hipchat.com/) は企業やチームのための、ホスティング型プライベートチャットサービスです。常設グループチャットルーム、ビデオ通話等の機能により、リアルタイムコラボレーションを加速させます。

Mackerel上でHipChatチャンネルを登録すると、Mackerelで監視しているホストのアラート情報を適宜チャットに流す事が出来ます。重大なアラートが発生した際にもリアルタイムにその情報を受け取る事が出来ます。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150424/20150424105422.png)

## HipChatとの連携を追加する

連携の設定は、[チャンネルページのHipChatフォーム](https://mackerel.io/my/channels?new=hipchat)から、新しいチャンネルを追加してください。
 
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150508/20150508104153.png?1431049486)

HipChatへの通知には、HipChatの[ROOMS APIの1つ](https://www.hipchat.com/docs/apiv2/method/send_room_notification)を利用しています。
チャンネルの登録には、チャンネル名以外にHipChatから取得するRoom API IDとRoomNotification Tokenを入力します。Room API IDとRoom Notification Tokenは以下の方法で取得して下さい。

### Room API IDの取得
- [HipChat](https://my.hipchat.com)のページから`Rooms`タブをクリック
- 通知を流す部屋を選択
- 画面中央部に記載されたものがRoom API IDです
  - <p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141029/20141029160124.png" alt="f:id:mackerelio:20141029160124p:plain" title="f:id:mackerelio:20141029160124p:plain" class="hatena-fotolife" itemprop="image"></span></p>


### Room Notification Tokenの発行と取得
- [HipChat](https://my.hipchat.com)のページから`Rooms`タブをクリック
- 通知を流す部屋を選択した状態から、左サイドバーの`Token`をクリック
  - <p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141029/20141029160136.png" alt="f:id:mackerelio:20141029160136p:plain" title="f:id:mackerelio:20141029160136p:plain" class="hatena-fotolife" itemprop="image"></span></p>
- Labelを入力し、新しいTokenを発行してください
  - [TIP]Label名が通知名として表示されるので、`Mackerel`のようなLabelを入力すると分かりやすくなります
- 画面中央部に表示されたTokenがRoom Notification Tokenとなります
  - <p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141029/20141029160148.png" alt="f:id:mackerelio:20141029160148p:plain" title="f:id:mackerelio:20141029160148p:plain" class="hatena-fotolife" itemprop="image"></span></p>


また、MackerelからHipChatへのアラートを送る際に、監視のステータスに応じてHipChatの通知を鳴らす事も可能です。必要に応じて設定してください。

どうぞご利用下さい。
