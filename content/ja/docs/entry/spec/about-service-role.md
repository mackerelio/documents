---
Title: 「サービス」「ロール」とは
Date: 2020-09-23T11:35:06+09:00
URL: https://mackerel.io/ja/docs/entry/spec/about-service-role
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613631299016
CustomPath: spec/about-service-role
---

Mackerel では、ひとつひとつのホストをそれぞれそのまま管理するのではなく、適切な単位でグルーピングをおこない、そのグループ単位で管理や設定をおこないます。

「サービス」とは、監視対象となるシステムやサービスを構成している1つ以上のホスト群をグルーピングするための単位です。また、1つのホストは複数のサービスに所属することもできます。

[f:id:mackerelio:20200923113905p:plain]

「サービス」に属している限り、そのホストは何らかの役割、責務を担っています。それを表現するために、Mackerelでは「ロール」をサポートしています。

「ロール」とは、「サービス」に属する1つ以上のホスト群をさらに細かくグルーピングするための単位です。「ロール」は「サービス」ごとに作成することができ、1つのホストは複数のロールに所属することもできます。サービスに属するホストは、必ず何らかのロールに所属する必要があります。

[f:id:mackerelio:20200923113947p:plain]

サービスやロールは、無料でいくつでも作成することができます。サービスを作成する方法については、以下のページを参照してください。

[サービス、ロールを作成する - Mackerel ヘルプ](https://mackerel.io/ja/docs/entry/howto/create-services-and-roles)

<h2 id="benefit-service-role">「サービス」「ロール」を作成・活用することでできるようになること</h2>
「サービス」「ロール」を作成し、複数のホストを「サービス」「ロール」でグルーピングすることでできるようになることには、以下のようなものがあります。

- 特定のポリシーに基づいて「サービス」や「ロール」を作成し、それにホストを紐付けることで、ホスト群の管理がしやすくなります。
- 「サービス」や「ロール」に対してメタデータを付与・管理することができます。
    - [https://mackerel.io/ja/api-docs/entry/metadata#roleput:title]
- [監視ルール](https://mackerel.io/ja/docs/entry/howto/alerts) の `絞込条件` により、監視ルールの対象をサービス・ロール単位で絞り込むことができます。
- [ホスト一覧画面](https://mackerel.io/my/hosts) や [アラート一覧画面](https://mackerel.io/my/alerts) での一覧表示で、サービス・ロール単位でフィルタリングすることができます。

<figure class="figure-image figure-image-fotolife" title="「サービス」単位に監視ルールや通知グループを設定することができます。">[f:id:mackerelio:20200923114109p:plain]<figcaption>「サービス」単位に監視ルールや通知グループを設定することができます。</figcaption></figure>

<figure class="figure-image figure-image-fotolife" title="同様に、「ロール」単位でも監視ルールや通知グループを設定することができます。">[f:id:mackerelio:20200923114203p:plain]<figcaption>同様に、「ロール」単位でも監視ルールや通知グループを設定することができます。</figcaption></figure>

以下については、サービス単位で利用可能な特長です。

- [通知グループ](https://mackerel.io/ja/docs/entry/howto/alerts#notification-group) により、アラートの通知先をサービス単位で切り替えることができます。
- サービスの数値指標（特定のホストに紐付かない・紐付けるべきではないメトリック）を、「サービス」に対して投稿することができます（[サービスメトリック](https://mackerel.io/ja/api-docs/entry/service-metrics)）。
    - [URL外形監視ルール](https://mackerel.io/ja/docs/entry/external-monitoring) の利用によって取得できたレスポンスタイムを、サービスメトリックとして登録することができます。


以下については、ロール単位で利用可能な特長です。

- ロールに属する複数のホストのグラフを、ひとつのグラフにまとめて表示することができます。
    - このグラフでは、過去存在していた・現在は存在していないホストの負荷状況も確認することができます。
    - 利用する上での注意点もあります。下記「ロールに関する注意事項」を確認してください。

<figure class="figure-image figure-image-fotolife" title="「ロール」としてひとつのグラフにまとめて表示させた場合、各ホストごとに色分けされて描画されます。">[f:id:mackerelio:20200923114309p:plain]<figcaption>「ロール」としてひとつのグラフにまとめて表示させた場合、各ホストごとに色分けされて描画されます。</figcaption></figure>


<h2 id="unit-example-service-role">「サービス」「ロール」を作成する単位の例</h2>
監視対象となるシステムやサービスの規模や提供方法、組織構造などに応じて、最適な「サービス」「ロール」の単位は変わるため、正解はありません。以下を一例として、最適な単位を検討してください。

<h3 id="unit-example-service">「サービス」単位の例</h3>
- 提供しているシステムやサービス単位。
- 提供しているシステムやサービスの環境単位。
    - `staging` `production` など。
- マイクロサービスアーキテクチャで構成されるシステムなどの、マイクロサービス単位。

<h3 id="unit-example-role">「ロール」単位の例</h3>
- 「サービス」に属するホストの各役割単位。
    - 「アプリケーションサーバー」「バッチサーバー」「データベースサーバー」など。
- サーバー上で稼働しているアプリケーション・ミドルウェア単位。
    - 負荷傾向が同じになるはずのサーバー群ごとにグルーピングをおこないます。
- ホストのサーバースペック単位。
- 監視水準のレベル単位。
    - `filesystem-90` `filesystem-95` など。（別途、ロールに対応する[監視ルール](https://mackerel.io/ja/docs/entry/howto/alerts)を設定して運用します。）

「サービス」「ロール」は、複数のホスト群を絞り込み・フィルタリングするためにも使うことができる、という点も考慮しながら、最適な作成単位を検討してみてください。

<h2 id="service-consideration">「サービス」に関する注意点</h2>
「サービス」を利用する際は、以下の点に注意してください。

- 「サービス」はオーガニゼーションを跨いで設定することはできません。
- 一度作成したサービスの名称を変更することはできません。新しくサービスを作り直して、古い方のサービスを削除してください。サービスの削除は、サービス名の右に表示されている設定リンクからおこなうことができます。

[f:id:mackerelio:20200923114452p:plain]

<h2 id="role-consideration">「ロール」に関する注意点</h2>
「ロール」を利用する際は、以下の点に注意してください。

- 「ロール」はオーガニゼーションを跨いで設定することはできません。
- ロールを利用することにより、ロールに属する複数のホストのグラフをひとつのグラフにまとめて表示することができますが、このグラフで確認できる情報は、ホストにそのロールが設定されて以降の情報となります。
