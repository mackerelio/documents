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

## SP initiatedフロー

SP initiatedフローでは、Mackerelの [サインインページ](https://mackerel.io/signin/saml) からSAML認証を開始します。

![MackerelのSP initiatedフローでのSAMLサインイン画面。オーガニゼーショングループ名の入力フォームと、サインインボタンがある。](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240830/20240830182144_original.png)

オーガニゼーショングループの管理者に指定されたオーガニゼーショングループ名を入力して「サインイン」ボタンをクリックすると、お使いのIDプロバイダーに遷移します。

IDプロバイダーでの認証が成功すると、Mackerelにサインインできます。

## IdP initiatedフロー

IdP initiatedフローでは、お使いのIDプロバイダーのアプリドロワーやアプリポータルなどからMackerelを起動することで、Mackerelにサインインできます。
