---
Title: Twilio にアラートを通知する
Date: 2016-10-11T12:18:53+09:00
URL: https://mackerel.io/ja/docs/entry/howto/alerts/twilio
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10328749687188878378
---

[Twilio](https://www.twilio.com/) は、API 経由で電話や SMS の発信を行えるサービスです。

Mackerel では、Twilio で作成したアクセストークンや電話番号を登録することで、 Mackerel のアラート発生時に SMS や電話で通知を受信できるようになります。

ひとつの通知チャンネルに対して、 Twilio を用いた以下の3種類の通知のうちいずれかを設定できます。複数の種類の通知が必要な場合は、同じ AccountSid や AuthToken を用いて複数の通知チャンネルを追加してください。

####  SMS

アラートの内容を SMS で通知します。

#### 架電通知 (固定メッセージで通知)

アラートが発生した際に電話を発信し、固定の文言を読み上げます。

#### 架電通知 (TwiML エンドポイントを指定して通知)

アラートが発生した際に、指定されたエンドポイントから配信される [TwiML](https://jp.twilio.com/docs/api/twiml) を用いて電話を発信します。単純な文言の読み上げ以外の対応を自動化したい場合などにご利用ください。

Twilio を用いた通知を設定する場合は、[チャンネルページの Twilio フォーム](https://mackerel.io/my/channels/-/create#twilio)から、新しいチャンネルを追加してください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20161107/20161107180322.png)


## 共通の設定項目

設定項目のうち、以下の項目は SMS と電話のどちらで通知する場合でも共通の項目です。

### AccountSid / AuthToken

Twilio のダッシュボードに表示される認証用のパラメータです。ここに入力した AccountSid に対応するユーザーとして Twilio への API リクエストを行います。

### From

通知の送信元になる電話番号です。Twilio で購入した送信元の電話番号を、 `+` と国番号から始まる E.164 フォーマットで入力してください。任意の電話番号(お使いの携帯電話の番号など)を指定することはできません。

Twilio の番号によっては、電話および SMS に対応していない場合もあります。チャンネルを追加する際には、利用したい通知方法に対応した番号を Twilio で登録してください。
特に、2016年10月現在では Twilio は 日本国内の電話番号からの SMS 送信に対応していません。 SMS 通知を利用する場合は、(送信先が日本国内であっても) アメリカ合衆国など、 SMS 通知に対応した電話番号を選択してください。

### To

通知の送信先になる電話番号です。 From と同様、 `+` と国番号から始まる E.164 フォーマットで入力してください。

### Status Callback URL (オプション)

通知で SMS 送信や電話の発信を行った際に、 Twilio から HTTP でコールバックされるエンドポイントです。 SMS の送信の成否や電話が着信したかなどを受け取れます。
詳細は [Twilio のドキュメント](https://jp.twilio.com/docs/) をご覧下さい。

## 通知の種類による設定項目

"Notification Type" の項目で、前述の3種類のうちのどの通知を利用するかを選択することが出来ます。また、架電通知の2種類には、それぞれ追加の設定項目があります。

### Notification level

固定メッセージの場合と TwiML エンドポイントを指定する場合のどちらの場合でも、通知の閾値を設定できます。
デフォルトでは Warning と Critical の両方で電話が発信されるようになっていますが、 Critical でのみ発信されるように変更することもできます。
また、架電による通知の場合はアラートのステータスが `ok` に戻った際に電話の発信は行われません。

### Message / Language

固定メッセージでの通知でのみ設定する項目です。
アラート発生時の電話で読み上げられるメッセージの内容と、その言語パラメータの指定です。言語パラメータについて、詳細は [Twilio のドキュメント](https://jp.twilio.com/docs/api/twiml/say#attributes-language) をご覧下さい。

### TwiML URL

TwiML エンドポイントを指定して通知する場合のみ設定する項目です。

[TwiML](https://jp.twilio.com/docs/api/twiml) 形式のレスポンスを返す外部の URL を指定します。この URL に対して Twilio 側のサーバからアクセスされます。 Mackerel での Twilio 通知チャンネルでは、このエンドポイントへの Twilio からのアクセスは GET メソッドで行われるため、 GET メソッドでのアクセスに対して、通知を行うための [TwiML](https://jp.twilio.com/docs/api/twiml) で応答する必要があります。

また、設定された URL にクエリパラメータが含まれていない場合、リクエストされる URL には `?alertId={alertId}` という形で、対応するアラートの id を表すクエリパラメータが自動的に付与されます。[アラート API](https://mackerel.io/ja/api-docs/entry/alerts) と組み合わせることで、架電通知をトリガーとした自動対応の構築などに利用できます。
設定された URL に既にクエリパラメータが含まれている場合は、 Mackerel による追加でのクエリパラメータの付与は行われません。

## 備考

固定メッセージでの通知の際には以下のような内容の TwiML を用いています。

```xml
<Response>
<Say language="@(language)">
@(message)
</Say>
</Response>
```

