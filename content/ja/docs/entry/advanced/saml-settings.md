---
Title: SAMLを設定する
Date: 2024-08-02T14:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/saml-settings
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802340630902356595
CustomPath: advanced/saml-settings
---

**本機能の利用にはSAML認証を含むMackerel上位プランのご契約が必要です。**

このドキュメントではMackerelとIDプロバイダーの連携設定について説明します。
SAMLで認証する手順については [SAMLで認証する](https://mackerel.io/ja/docs/entry/advanced/saml-authentication) をご覧ください。
その他の発展的なSAMLの使い方については下記ページをご覧ください。

- [属性マッピングを管理する](https://mackerel.io/ja/docs/entry/advanced/saml-attribute-mappings)
- [外部コラボレーターを管理する](https://mackerel.io/ja/docs/entry/advanced/saml-outside-collaborators)

## SAMLとオーガニゼーショングループ

SAMLを利用すると、お使いのIDプロバイダーの資格情報でMackerelにサインインできます。
Mackerelでは複数のオーガニゼーションからなるオーガニゼーショングループを構成し、IDプロバイダーと連携させることができます。

## SAMLを設定する

お使いのIDプロバイダーとMackerelの双方に設定が必要です。

### IDプロバイダーに設定する項目

お使いのIDプロバイダーには以下の表を参考に設定してください。

| 設定項目        | 値                                      |
| --------------- | --------------------------------------- |
| SP Entity ID    | `https://mackerel.io/saml/metadata.xml` |
| ACS URL         | `https://mackerel.io/saml/acs`          |
| Request Binding | `POST`                                  |
| NameIDPolicy    | `email`                                 |

各IDプロバイダーの設定については、IDプロバイダーのドキュメントを参照してください。

- [Entra ID](https://learn.microsoft.com/ja-jp/entra/identity-platform/single-sign-on-saml-protocol)
- [Okta](https://help.okta.com/oie/ja-jp/content/topics/apps/apps_app_integration_wizard_saml.htm)
- [Keycloak](https://www.keycloak.org/docs/latest/server_admin/index.html#assembly-managing-clients_server_administration_guide)

### オーガニゼーショングループの基本設定

オーガニゼーショングループの管理者は、ユーザーメニューからオーガニゼーショングループ一覧画面にアクセスできます。  
オーガニゼーショングループ一覧画面からオーガニゼーショングループを選択して、設定の確認・変更ができます。

![ユーザーメニューの画像。上から順に「アカウント設定」、「オーガニゼーショングループ設定」、「サインアウト」の項目が並んでいる。](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240925/20240925112726.png)

![オーガニゼーショングループの編集画面。表形式でオーガニゼーショングループの詳細情報が表示されている。表の最初にオーガニゼーショングループ名がある。続いて順に「詳細」、「IdP メタデータ XML」、「IdP Entity ID」、「Single Sign On URL」、「IdP X509証明書」、「SAML 認証を強制」、「属性マッピング」、「デフォルトでユーザーを閲覧者として追加」の入力フォームがあり、最後に「更新」ボタンと「キャンセル」ボタンがある。](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240809/20240809125519_original.png)

| 設定項目                               | 説明                                                                                                                                                                                            |
| -------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 詳細                                   | オーガニゼーショングループを説明するような、自由に設定可能な文章。                                                                                                                              |
| IdP メタデータ XML                     | IDプロバイダーから提供される XML。アップロードすることで、IdP Entity ID、Single Sign On URL、IdP X509 証明書を設定できます。                                                                   |
| IdP Entity ID                          | (必須)IDプロバイダーから提供される識別子。登録済みのIdP Entity IDは指定できません。                                                                                                          |
| Single Sign On URL                     | SP initiatedフロー時に使用される、IDプロバイダー側のエンドポイント。                                                                                                                          |
| IdP X509 証明書                        | (必須)IDプロバイダーから提供される証明書。                                                                                                                                                     |
| SAML認証を強制                        | 有効な場合、SAML認証したユーザーまたは [外部コラボレーター](https://mackerel.io/ja/docs/entry/advanced/saml-outside-collaborators) のみがオーガニゼーションを閲覧可能になります。                                                                                                                  |
| 属性マッピングを有効にする             | 有効な場合、[属性マッピング](https://mackerel.io/ja/docs/entry/advanced/saml-attribute-mappings) に基づいた権限割り当てが行われます。SAML認証の強制が有効な場合のみ、この設定を有効にできます。 |
| デフォルトでユーザーを閲覧者として追加 | 有効な場合、SAML認証したユーザーはオーガニゼーショングループに所属するすべてのオーガニゼーション閲覧者として参加します。属性マッピングが無効な場合のみ、この設定を有効にできます。             |

<h2 id="managing-orgs">所属オーガニゼーションの管理</h2>

オーガニゼーショングループの管理者は、サイドバーの「所属オーガニゼーション」から、所属オーガニゼーションの管理画面にアクセスできます。

![所属オーガニゼーションの一覧画面。オーガニゼーションの一覧が表形式で表示されている。画面右上には「所属オーガニゼーションを追加」ボタンがある。各オーガニゼーションの行の右端には「削除」ボタンがある。](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240809/20240809152719_original.png)

所属オーガニゼーションの管理画面には、オーガニゼーショングループに所属しているオーガニゼーションが一覧表示されます。

「所属オーガニゼーションの追加」ボタンから、オーガニゼーションを追加できます。以下の要件を満たすオーガニゼーションが追加可能です。

- ご契約中のMackerel上位プランが適用されている。
- 他のオーガニゼーショングループに所属していない。
- オーナーが追加先のオーガニゼーショングループの [外部コラボレーター](https://mackerel.io/ja/docs/entry/advanced/saml-outside-collaborators) ではない。

所属オーガニゼーションをはずすには、対象オーガニゼーション名の右側にある「削除」ボタンをクリックします。
オーガニゼーションのメンバーと権限は維持されます。
