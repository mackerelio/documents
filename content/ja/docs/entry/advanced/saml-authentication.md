---
Title: SAMLで認証する
Date: 2024-08-02T14:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/saml-authentication
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802340630902356589
CustomPath: advanced/saml-authentication
---

**本機能はSAML認証を含むMackerel上位プランをご契約、およびSAML認証を設定いただいたオーガニゼーションのユーザーにのみ有効です。**
SAML認証の設定については [SAMLを設定する](https://mackerel.io/ja/docs/entry/advanced/saml-settings) をご覧ください。

SAML認証を利用すると、お使いのIDプロバイダーの資格情報を使用してMackerelにサインインできます。

## 認証フロー

### SP initiatedフロー

SP initiatedフローでは、Mackerelの [サインインページ](https://mackerel.io/signin/saml) からSAML認証を開始します。

![MackerelのSP initiatedフローでのSAMLサインイン画面。オーガニゼーショングループ名の入力フォームと、サインインボタンがある。](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240830/20240830182144_original.png)

オーガニゼーショングループの管理者に指定されたオーガニゼーショングループ名を入力して「サインイン」ボタンをクリックすると、お使いのIDプロバイダーに遷移します。

IDプロバイダーでの認証が成功すると、Mackerelにサインインできます。

### IdP initiatedフロー

IdP initiatedフローでは、お使いのIDプロバイダーのアプリドロワーやアプリポータルなどからMackerelを起動することで、Mackerelにサインインできます。

## JIT Provisioning

Mackerelでは、JIT Provisioningによるサインアップをサポートしています。
SAML認証フローを通じてシームレスにMackerelの利用を開始できます。

1. [認証フロー](#認証フロー)を参考に、SAML認証を行います。
2. サインアップページが表示されます。利用規約に同意いただき、成人している方、もしくは未成年者の場合には親権者の同意を得ている場合は、2つのチェックボックスにチェックを入れます。
3. 「サインアップ」をクリックします。
4. メールアドレスを確認するためのメールが送信されます。メールに記載されているURLにアクセスすると登録が完了します。
