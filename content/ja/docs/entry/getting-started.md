---
Title: Mackerelをはじめる
Date: 2014-05-01T12:46:18+09:00
URL: https://mackerel.io/ja/docs/entry/getting-started
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815723048668
---

Mackerel は、複数のサーバのリソース状況やサービスのパフォーマンスを可視化、監視するためのサービスです。

新しく Mackerel を使う場合は、まずはオーガニゼーション（[→用語集](https://mackerel.io/ja/docs/entry/glossary#organization)）を作成します。もしくはすでに Mackerel を利用中のユーザーからオーガニゼーションに招待してもらうことで、既存のオーガニゼーションに所属します。

[:contents]

## オーガニゼーションに所属する

### オーガニゼーションを新規作成する

1. [サインアップページ](https://mackerel.io/signup) にアクセスします。
2. メールアドレスを入力します。
3. 利用規約に同意いただき、成人している方、もしくは未成年者の場合には親権者の同意を得ている場合は、2 つのチェックボックスにチェックを入れます。
4. 「サインアップ」をクリックします。
5. 画面の指示に従ってオーガニゼーションを作成します。
6. 先ほど入力したメールアドレスに確認メールが送信されますので、メールの内容に従ってパスワードの設定を行ってください。

### 既存のオーガニゼーションに加入する

1. すでにオーガニゼーションに所属している、管理者以上のユーザーに招待してもらいます。（→ [他のユーザーを招待する](https://mackerel.io/ja/docs/entry/howto/invite-others)）
2. 招待メールに記載されている URL にアクセスすると、サインアップページが表示されます。
3. 利用規約に同意いただき、成人している方、もしくは未成年者の場合には親権者の同意を得ている場合は、2 つのチェックボックスにチェックを入れます。
4. 「サインアップ」をクリックします。
5. オーガニゼーションへの招待ページが表示されます。「<オーガニゼーション名> に参加」をクリックして、オーガニゼーションに参加します。
6. 招待したメールアドレスに確認メールが送信されますので、メールの内容に従ってパスワードの設定を行ってください。

<h2 id="next-step">次に行うこと</h2>

### ホストを登録する

監視対象のサーバに mackerel-agent をインストールすることで Mackerel にホスト（[「ホスト」とは](https://mackerel.io/ja/docs/entry/spec/about-host)）として登録されます。<br>また、クラウドのマネージドサービス向けにはクラウドインテグレーション、コンテナサービスである ECS のタスクや Kubernetes の Pod の監視には mackerel-container-agent を利用することで、監視対象ホストとして扱えます。登録したホストには、エージェントが収集するメトリック（[メトリック仕様](https://mackerel.io/ja/docs/entry/spec/metrics)）や、クラウドインテグレーションによって取得したメトリックが投稿されます。

* mackerel-agent をインストールしてホストを登録する
  * [エージェントをインストールする](https://mackerel.io/ja/docs/entry/howto/install-agent)
* クラウド製品をホストとして登録する
  * [AWSインテグレーション](https://mackerel.io/ja/docs/entry/integrations/aws)
  * [Azureインテグレーション](https://mackerel.io/ja/docs/entry/integrations/azure)
  * [Google Cloudインテグレーション](https://mackerel.io/ja/docs/entry/integrations/gcp)
* タスク（ECS）や Pod（Kubernetes）をホストとして登録する
  * [コンテナを監視する](https://mackerel.io/ja/docs/entry/howto/container-agent)

### ホストを管理する

登録したホストを役割に応じて「サービス」「ロール」という単位でまとめて管理できます（[「サービス」「ロール」とは](https://mackerel.io/ja/docs/entry/spec/about-service-role)）。共通するロールでまとめられたホスト群のグラフ（ロールグラフ）を、[サービス一覧](https://mackerel.io/my/services) からサービスごとに確認できます。

* [サービス、ロールを作成する](https://mackerel.io/ja/docs/entry/howto/create-services-and-roles)


### ホストを監視する

監視ルールを設定することで監視結果に応じてアラートが発生し、通知が行われます。たとえば「ホストメトリック監視」では、ホストに投稿されたメトリックの値が監視ルールに設定したしきい値を超える（あるいは下回る）場合にアラートが発生します。

* [監視・通知を設定する](https://mackerel.io/ja/docs/entry/howto/alerts)

mackerel-agent にチェック監視を設定することで、サーバ内のプロセスの状態やログの監視を行えます。チェック監視を行うためにはあらかじめチェックプラグインのインストールが必要です。

* [チェック監視に公式チェックプラグイン集を使う](https://mackerel.io/ja/docs/entry/howto/mackerel-check-plugins)

チェック監視の仕様に沿ってユーザーが自作したプラグインも監視に利用できます。

* [ユーザー定義のチェック監視項目を追加する](https://mackerel.io/ja/docs/entry/custom-checks)


### さまざまなメトリックを投稿する

エージェントやクラウドインテグレーションによって投稿されるメトリック以外に、メトリックプラグインを利用することでミドルウェアなどのメトリックを投稿できます。メトリックプラグインを利用するためにはあらかじめインストールが必要です。

* [ミドルウェアのメトリック可視化に公式プラグイン集を使う](https://mackerel.io/ja/docs/entry/howto/mackerel-agent-plugins)

カスタムメトリックの仕様に沿ってユーザーが自作したプラグインもメトリックの投稿に利用できます。

* [ユーザー定義のメトリックを投稿する](https://mackerel.io/ja/docs/entry/advanced/custom-metrics)

特定のホストに紐づかないメトリックは、API などを利用することでサービスメトリックとして投稿できます。サービスメトリックは [サービス一覧](https://mackerel.io/my/services) からサービスごとに確認できます。

* [サービスメトリック - Mackerel API ドキュメント (v0)](https://mackerel.io/ja/api-docs/entry/service-metrics#post)
* [サービスメトリックを投稿する](https://mackerel.io/ja/docs/entry/advanced/fluentd)


### 独自のダッシュボードを作成する

目的に応じて必要なグラフなどを自由に配置したダッシュボードが作成できます。複数のホストの情報を横断的に見る場合などに活用できます。

* [カスタムダッシュボードを利用する](https://mackerel.io/ja/docs/entry/howto/dashboard)


### ユーザーを管理する

オーガニゼーションにユーザーを招待することで、共同で管理できます。また、ユーザー単位で操作権限を設定できます。

* [他のユーザーを招待する](https://mackerel.io/ja/docs/entry/howto/invite-others)
* [ユーザー権限](https://mackerel.io/ja/docs/entry/spec/authority)


## 困った時は

### ヘルプやFAQを活用する

Mackerel の使い方については [ヘルプ](https://mackerel.io/ja/docs/) ページにて公開しています。また、よくあるご質問を [FAQ](https://support.mackerel.io/hc/ja) ページにて公開しています。ご不明な点についてはこれらのページをご覧いただくことで解決できる場合があります。

### サポートチームに問い合わせる

ヘルプや FAQ をご覧になっても問題が解決できない場合は、[お問い合わせフォーム](https://support.mackerel.io/hc/ja/requests/new) からご連絡ください。Mackerel のテクニカルサポートチームが問題の解決をサポートします。サポートポリシーについては [Mackerelのテクニカルサポートについて](https://support.mackerel.io/hc/ja/articles/360043006972-Mackerel%E3%81%AE%E3%83%86%E3%82%AF%E3%83%8B%E3%82%AB%E3%83%AB%E3%82%B5%E3%83%9D%E3%83%BC%E3%83%88%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6) をご覧ください。また、以下のリンクの内容を意識してお問い合わせいただくと、お困りごとを早期解決しやすくなります。

* [サポートチームへのお問い合せで早期解決に導くポイントのご紹介](https://mackerel.io/ja/blog/entry/20200616)
