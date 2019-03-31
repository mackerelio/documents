---
Title: Reactioとの連携方法
Date: 2015-10-22T15:14:34+09:00
URL: https://mackerel.io/ja/docs/entry/howto/alerts/reactio
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653458415125439529
---

[Reactio](https://reactio.jp)はアラート情報を集約し、任意の一斉通知ルール設定による柔軟な連絡機能やグループチャット機能による情報共有などを提供するアラート管理サービスです。通知の例として、音声、SMS、Email通知をする事が出来ます。


Reactioとの連携ではアラート発生時のアクションとして、以下の三つから選べます。

1.  インシデントのみを作成する
2.  インシデントを作成し、一斉通知を行う
3.  インシデントを作成し、一斉通知と架電通知を行う


アラート発生時に作成されたインシデントに紐づくアラートログは、チャットメッセージとして以後投稿されます。
この様にReactioの通知機能を損なうことなく、Mackerelで発生したアラート通知をReactioへ集約できます。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20151022/20151022151145.png)

Reatio連携を完了するために、チャンネルページのReactioフォームに Organization ID と API Key を入力の上チャンネルを作成してください。Organization ID は Reactioサービスご登録時に発行されるものを、またAPI Keyは以下の方法で取得したものを入力してください。

- Reactioメイン画面右上のメニューより「プロジェクト設定」を選択します
- プロジェクト設定画面にてプロジェクトを選択します
- プロジェクト詳細画面の API Key 項目に記述されている文字列が API Key となります

より詳しい説明は以下をご参照ください。

[http://blog.reactio.jp/entry/2015/05/26/161408:title]
