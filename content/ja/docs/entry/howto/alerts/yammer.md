---
Title: Yammer にアラートを通知する
Date: 2016-01-14T15:27:01+09:00
URL: https://mackerel.io/ja/docs/entry/howto/alerts/yammer
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653586347152970462
---

Yammer は、社員が部門、場所、ビジネスアプリケーションを横断しコラボレーションするのを促進するプライベートソーシャルネットワークです。Mackerel のチャンネル機能と連携することにより、Yammer のグループへアラートメッセージを通知し、グループメンバーでアラート内容を容易に共有出来ます。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160114/20160114113016.png)

## Yammerへのアラート通知を追加する

通知の設定は、[チャンネルページのYammerフォーム](https://mackerel.io/my/channels?new=yammer)から、新しいチャンネルを追加してください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160114/20160114113017.png)

## Access Token と Group ID の取得方法について

チャンネルの登録には、Access Token と Group ID の設定が必要です。以下の方法にてこれらの値を取得出来ます。

以下、アプリケーション登録時のリダイレクトURL(Expected redirect) `redirect_uri` が `https://localhost` であり、また 2) にて取得した `Client ID` と `Client secret` が `mackerel_client` と `mackerel_secret`、そして 3) にて取得した `code` が `mackerel_code` の場合を想定した Access Token 取得例を紹介します。ご利用の環境に合わせ、例示したアクセス先URLなどを適時変更してください。

1) Mackerel と連携するためのアプリケーションの登録を [アプリケーション登録ページ](https://www.yammer.com/client_applications)より行ってください。
アプリケーション登録方法は、[公式ページ](https://developer.yammer.com/docs/app-registration)を参考にしてください。


2) 登録したアプリケーションの `Client ID` と `Client secret` を取得してください。
[アプリケーション登録ページ](https://www.yammer.com/client_applications)より、登録したアプリケーションを選択してください。画面上の `Client ID` と `Client secret`項目に記載されています。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160114/20160114104339.png)


3) 登録したアプリケーションの `code` を取得してください。
下記URLに `client_id`、また `redirect_uri` のクエリを設定してアクセスすることにより Code が取得出来ます。

`https://www.yammer.com/oauth2/authorize?client_id=mackerel_client&response_type=code&redirect_uri=https%3A%2F%2Flocalhost`

ブラウザーで適切なURLにアクセスした場合、アプリケーション認証画面へ移行します。承認(Allow)ボタンをクリックしてください。
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160114/20160114104340.png)


承認後、ブラウザURL表示部分を参照して `code` クエリ値から `code` を取得できます。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160114/20160114104341.png)



4) 登録したアプリケーションの Access Token を取得してください
下記URLに `client_id`、 `client_secret`、 `code` のクエリを設定してアクセスすることにより Access Token が取得出来ます。

`https://www.yammer.com/oauth2/access_token.json?client_id=mackerel_client&client_secret=mackerel_secret&code=mackerel_code`

返却されるJSONに含まれる "token" キーの値が `Access Token` です。

## Group ID の取得方法について
下記方法にて Group ID の取得が出来ます。

1) 連携時に投稿予定のグループページにアクセスします。

2) ブラウザー上のURL項目に表示された `feedId` クエリ値を取得します

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160114/20160114104342.png)


`feedId` の値が `Group ID` となります。

## 連携時のグループへのメッセージ投稿回数について
Yammer は現時点(ヘルプページ最終更新時)では、[30秒間で可能なメッセージAPIコール回数が10回と制限されています](https://developer.yammer.com/docs/rest-api-rate-limits)。そのため30秒間で制限回数を超えるアラートが起こった場合、その超過期間中に超過分のアラートメッセージはグループへ投稿されません。この点をご留意ください。
