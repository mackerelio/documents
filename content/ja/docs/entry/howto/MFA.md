---
Title: 2段階認証を利用する
Date: 2016-08-03T10:42:17+09:00
URL: https://mackerel.io/ja/docs/entry/howto/MFA
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10328749687177179436
---

<h2 id="about-mfa">2段階認証とは</h2>

Mackerel 2段階認証を、お使いのMackerelアカウントで有効にすると、Mackerelにサインインする際に、ユーザー名とパスワードに加え、デバイスからの認証コードを必要とするようになります。
所属するオーガニゼーションのセキュリティを強化し、お客さまの各種リソースの保護に貢献します。

<h2 id="enable-mfa">2段階認証を有効にする</h2>

1. 外部アカウントによる認証のみを設定している場合は、 [アカウント設定](https://mackerel.io/settings/user?tab=account) ページからパスワードによる認証を設定してください。
2. [2段階認証の有効化](https://mackerel.io/settings/user/mfa/enroll) ページに移動し、ページのガイドに従って有効化してください。

設定が完了すると、次回以降Mackerelにサインインする際に、認証コードの入力を求められるようになります。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/h/haya14busa/20160802/20160802173713.png)

例えば認証アプリとして [Google Authenticator](https://support.google.com/accounts/answer/1066447) を使っている場合は、
以下の画像のように認証コードが表示されるので、この認証コードを入力してください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/h/haya14busa/20160802/20160802170630.png)

なお、2段階認証を有効化した際にその他の端末のログインセッションが無効になることはありません。
また2段階認証を有効化しても API によるアクセスには影響はありません。

<h2 id="about-recovery-code">リカバリーコードによる認証</h2>

認証コードを発行するデバイスが利用できなくなった場合、1度だけ使える10個のリカバリーコードを控えておけば、認証コードの変わりにリカバリーコードを入力することによってサインインできます。
[2段階認証の設定](https://mackerel.io/settings/user/mfa/configure) ページからリカバリーコードをダウンロード、または印刷して安全な場所に補完しておくことをおすすめします。
リカバリーコードは[2段階認証の設定](https://mackerel.io/settings/user/mfa/configure) ページから再発行できます。

### リカバリーコードでサインインする

メールアドレスとパスワードによる認証後、認証コードの入力を求められるページで "リカバリーコードを入力" するをクリックしてください。
リカバリーコードによる認証ページへ移動するので、このページで控えているリカバリーコードのうち使用していないコードを1つを入力してください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/h/haya14busa/20160802/20160802175127.png)

リカバリーコードを印刷して控えている場合は以下の画像のように使用したコードにチェックしておくことをオススメします。
[2段階認証の設定](https://mackerel.io/settings/user/mfa/configure) ページからも使用していないリカバリーコードを確認することが可能で、
残りのコードが少なくなったら再発行することもできます。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/h/haya14busa/20160802/20160802174701.png)

<h2 id="disable-mfa">2段階認証を無効化する</h2>

[2段階認証の設定](https://mackerel.io/settings/user/mfa/configure) ページから2段階認証を無効化できます。
2段階認証を無効化すると既存の認証アプリのコードとリカバリーコードはすべて無効化されます。

<h2 id="move-to-new-device">新しいデバイスに移行する</h2>

新しいデバイスを購入して、そのデバイスを使用して2段階認証を行いたい場合は以下のガイドに従ってください。

1. Mackerel にサインインしている別の端末を用意してください。リカバリーコードを控えておけばリカバリーコードによっても Mackerel にサインインできます。
2. サインインしている端末から [2段階認証の設定](https://mackerel.io/settings/user/mfa/configure) ページへ移動し "認証アプリを再設定" するボタンをクリック、または
[2段階認証の有効化](https://mackerel.io/settings/user/mfa/enroll) ページへ直接移動し、ページのガイドに従って2段階認証を新しい端末を使って有効化してください。
3. 最後に古いデバイスの認証アプリが発行する認証コードは無効化されているので、古いデバイスの認証アプリから Mackerel のアカウントを削除してください。
