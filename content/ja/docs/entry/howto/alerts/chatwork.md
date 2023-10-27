---
Title: Chatworkに通知を表示する
Date: 2014-12-04T14:38:13+09:00
URL: https://mackerel.io/ja/docs/entry/howto/alerts/chatwork
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450076028632
---

[Chatwork](https://www.chatwork.com/) は、業務の効率化と会社の成長を目的とした、メール・電話・会議に代わるコミュニケーションツールです。

Mackerel上でChatworkチャンネルを登録すると、Mackerelで監視しているホストのアラート情報を適宜チャットに流す事が出来ます。重大なアラートが発生した際にもリアルタイムにその情報を受け取る事が出来ます。

<!--![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141204/20141204112031.png)-->

### Chatwork との連携を追加する
連携の設定は、[チャンネルページのChatworkフォーム](https://mackerel.io/my/channels/-/create#chatwork)から行って下さい。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190121/20190121191504.png)

Chatworkへの通知には、[Chatwork API](https://developer.chatwork.com/docs)から、[チャットにメッセージを投稿するAPI](https://developer.chatwork.com/reference/post-rooms-room_id-messages)のみを利用しています。


チャンネルの登録には、チャンネル名以外にChatworkのAPIを利用するためのトークンと、通知を送る部屋のROOM IDが必要になります。トークンとROOM IDは以下の方法で取得して下さい。

##### トークンの取得
- [Chatwork](https://www.chatwork.com/) にログイン後、画面右上から`動作設定`をクリック
  - ![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141204/20141204112033.png)
- `API発行`タブを選択し、トークン発行のためにパスワードを入力
  - ![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141204/20141204112034.png)
- 発行されたトークンをコピーし、MackerelのChatworkチャンネル登録フォームへ入力して下さい
  - ![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20141204/20141204112035.png)

##### ROOM IDの取得
- [Chatwork](https://www.chatwork.com/) にログイン後、通知を送りたいチャットに移動
- URL末尾、`rid`に続く数字がROOM IDとなります
  - ![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190123/20190123181957.png)

どうぞご利用下さい。
