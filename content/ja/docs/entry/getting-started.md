---
Title: Mackerelをはじめる
Date: 2014-05-01T12:46:18+09:00
URL: https://mackerel.io/ja/docs/entry/getting-started
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815723048668
---

Mackerelは複数のサーバーのリソース状況やサービスのパフォーマンスを可視化、監視するためのサービスです。

Mackerelでは、mackerel-agentと呼ばれるプログラムをお使いのサーバーにインストールすることでホストを登録します。協調してはたらくホストをまとめる『サービス』、サービス内のホストの役割である『ロール』によってホストを管理し、エージェントが収集したホストの状況をウェブ上で視覚的に確認できます。

またMackerelでは、ユーザはオーガニゼーション（団体、組織）（[→用語集](https://mackerel.io/ja/docs/entry/glossary#organization)）に所属し、複数人で共同で管理できます。

## 新規登録し、あたらしくオーガニゼーションを作成する

新しくMackerelを使う場合は、まずはオーガニゼーションを作ります。

1. [サインアップページ](https://mackerel.io/signup)にアクセスします。
2. メールアドレスを入力し、フォームを送信します。
3. 画面の指示に従って、使用するオーガニゼーションを作成してください。先ほど入力したメールアドレスに確認メールが送信されているので、指示に従ってパスワードの設定を行ってください。

## 新規登録し、既存のオーガニゼーションに加入する

既に同僚がMackerelを利用している場合は、同僚から招待してもらうことで既存のオーガニゼーションに所属できます。

1. すでにオーガニゼーションに加入している他のユーザに招待メールを送信してもらいます。（→ [他のユーザを招待する](https://mackerel.io/ja/docs/entry/howto/invite-others)）
2. 招待メールに記載されているURLにアクセスするとサインインページが表示されるので、「Sign Up」をクリックします。
3. サインアップページでメールアドレスを入力し、フォームを送信します。
4. オーガニゼーションへの招待ページが開きます。「Join ○○」をクリックして、オーガニゼーションに参加してください。
5. これでMackerelを使いはじめられます。先ほど入力したメールアドレスに確認メールが送信されているので、指示に従ってパスワードの設定を行ってください。

<h2 id="next-step">次に行うこと</h2>

- ホストを管理する
  - → ホストを登録するには、[エージェントをインストールする](https://mackerel.io/ja/docs/entry/howto/install-agent)
  - → ホストを整理するには、[サービス、ロールを作成する](https://mackerel.io/ja/docs/entry/howto/create-services-and-roles)
- 様々なメトリックを投稿する
  - → [ユーザ定義のメトリックを投稿する](https://mackerel.io/ja/docs/entry/advanced/custom-metrics)
  - → [サービスメトリックを投稿する](https://mackerel.io/ja/docs/entry/advanced/fluentd)
- ユーザーを管理する
  - → [他のユーザを招待する](https://mackerel.io/ja/docs/entry/howto/invite-others)
- いろいろなノウハウ/Tipsを見る
  - → [はてなブックマークのMackerelタグページ](http://b.hatena.ne.jp/search/tag?safe=off&q=mackerel)
  - → [QiitaのMackerelタグページ](https://qiita.com/tags/mackerel)
  - → [Twitterの#mackerelio検索](https://twitter.com/search?q=%23mackerelio)
