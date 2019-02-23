---
Title: URL外形監視をおこなう
Date: 2015-07-01T12:23:47+09:00
URL: https://mackerel.io/ja/docs/entry/external-monitoring
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450099476443
---

本機能ではhttpまたはhttpsに対しての監視を行います。

##URL外形監視を設定する
画面左側メニューのMonitorsより監視ルールを追加のボタンをクリックします。
External Httpのタブをクリックすると、以下の項目が表示されますので、
各項目に値・名称を記述して作成ボタンをクリックします

* URL：httpまたはhttpsを選択し、それ以後のURLを記述してください
* アラートの発生条件：監視ルールをサービスと関連させて、レスポンスタイムをサービスメトリックとして投稿します。サービスメトリックのタブでグラフを見ることができます。また、レスポンスタイムに閾値を設定できます。
* レスポンスボディのチェック：レスポンスボディに指定の文字列が含まれているかをチェックします。指定された文字列がレスポンスボディに含まれていない場合、アラートが発報されます。
* 通知の再送間隔：アラートの状態が、指定された時間を超えても変化がない場合、再度通知します。
* HTTP リクエストヘッダ：リクエストに任意のHTTPヘッダを指定できます。
  * User-Agentの指定がない場合は `User-Agent: mackerel-http-checker/x.y.z` が送信されます(`x.y.z` はバージョン番号が入ります)。
  * デフォルトで Cache-Control ヘッダに `no-cache` が指定されています。
* 証明書の有効期限の監視：SSL証明書の有効期限を監視します。有効期限の残り日数が閾値下回った場合、アラートが発報されます。
* 監視ルール名：本監視定義の名前を記述してください

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160125/20160125155134.png:image=https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160125/20160125155134.png]


##URL外形監視の仕様について
* チェック間隔は1分毎の固定となります。
* 次の条件でエラーと認識し、アラート通知が実行されます
    * ステータスコードが、4xxまたは5xx系の場合
    * タイムアウト(15秒)となった場合
    * SSL証明書が不正の場合
    * レスポンスタイムが閾値を超えている場合(任意設定)
    * レスポンスボディに指定文字列が含まれていない場合(任意設定)
    * SSL証明書の有効期限の残り日数が閾値を下回っている場合(任意設定)
* 2xxまたは3xx系の応答はエラーではない正常な応答として認識します
    * 3xx系はリダイレクトのフォローは行いません
* Basic認証を利用しているURLを監視したい場合は、Authorizationヘッダを適切に指定するか、"https://user:password@example.com/..." のようにURLに認証情報を含める形にしてください

##URL外形監視の監視元IPアドレスについて
Mackerelからの通知のリクエスト元IPアドレスレンジと同じとなります。  
詳細は[Webhook通知や外形監視など、Mackerelからの通知元IPアドレスは？](https://mackerel.io/ja/docs/entry/faq/spec/source-ip-addresses)をご覧下さい。

##利用可能なプランについて
本機能はTrialプランまたは有料プランでのみご利用いただけます。

監視先20個までは追加の料金なしでご利用いただくことが可能です。  
20個を超えると20個単位でホスト1台分としてカウントします。  

##アラートサンプル
監視設定を追加するとサイドメニューにあるMonitorsの一覧に以下のように表示されます。
[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150907/20150907153553.png:image=https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150907/20150907153553.png]

アラートが発生した際には以下の様な表示となり、アラート詳細画面で詳細が確認できます。  
画像上で表示されている503はHTTPステータスコードです。  
復旧した場合は復旧時のHTTPステータスコードが表示されるようになります。

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150907/20150907154229.png:image=https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150907/20150907154229.png]

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150907/20150907154532.png:image=https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150907/20150907154532.png]
