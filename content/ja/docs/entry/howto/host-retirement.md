---
Title: 登録されたホストを Mackerel の管理対象から外す
Date: 2017-04-04T14:35:52+09:00
URL: https://mackerel.io/ja/docs/entry/howto/host-retirement
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10328749687234488816
---

監視を行いたいサーバに[エージェントをインストール](https://mackerel.io/ja/docs/entry/howto/install-agent)したり、[AWSインテグレーション](https://mackerel.io/ja/docs/entry/integrations/aws)の設定を行うことで、そのサーバは Mackerel の管理対象ホストとして登録されます。

一度登録されたホストを Mackerel の管理対象から外すことを、Mackerel では「退役」と呼んでいます。ここではその退役方法について説明します。

何らかの理由により Mackerel での管理が不要になった場合に実施して下さい。


<h2 id="retire">ホストの退役の実施</h2>

Mackerel の管理対象から外したいホストのホスト詳細画面を開き、ホスト名の右にある歯車アイコンの設定リンクをクリックして下さい。

[f:id:mackerelio:20190222141218p:plain]

すると、以下のようなホストの設定画面が開きます。画面右下にこのホストを退役させるためのボタンがありますので、これをクリックしてホストを退役させて下さい。

[f:id:mackerelio:20190222141230p:plain]

エージェントをインストールしている場合は、エージェントのアンインストールも実施して下さい。エージェントのアンインストールの方法については [https://mackerel.io/ja/docs/entry/howto/install-agent:title] を参照して下さい。


また、ホストの退役は API やコマンドラインツール mkr、mackerel-agent の `retire` サブコマンドでも実施することが可能です。


[https://mackerel.io/ja/api-docs/entry/hosts#retire:embed:cite]

[https://mackerel.io/ja/docs/entry/advanced/cli:embed:cite]

[https://mackerel.io/ja/docs/entry/spec/agent:embed:cite]


<h2 id="faq">FAQ</h2>

### サーバの自動的な拡張・縮退（オートスケール）に合わせて Mackerel への登録・退役を行いたい

以下のページを参照して下さい。

[https://mackerel.io/ja/docs/entry/howto/auto-scaling:embed:cite]


### AWSインテグレーションを使用していて、AWS上にはリソース（EC2インスタンスなど）を残したまま、Mackerel 側だけ対象から外したい

AWS上に対象となるインスタンスなどが残った状態のまま Mackerel 側で退役作業だけを実施した場合、AWSインテグレーションの機能により再度 Mackerel ホストとして登録されてしまいます。

AWS 側のタグを用いて、インテグレーションによる連携対象から除外されるように設定をして下さい。詳しくは以下のヘルプを参照して下さい。

[https://mackerel.io/ja/docs/entry/integrations/aws#tag:embed:cite]


### 一度退役したホストを再度 Mackerel に登録したい

**一度退役を行なうと、そのホスト情報を元に戻すことはできません。** 退役の実施は必要なときだけ実施してください。

Mackerel から退役させたサーバを新規のホストとして再度 Mackerel に登録したい場合は、以下の作業を行って下さい（**退役前の情報を引き継ぐことはできません**）。

1. `/var/lib/mackerel-agent` (デフォルト設定の場合。KCPS用エージェントの場合は `/var/lib/mackerel-agent-kcps` 、Windows Server の場合はインストールフォルダ内) にホストIDの記録された `id` というファイルがあるかどうかを確認し、ある場合はこれを削除してください。
2. エージェントを起動（既に起動済みの場合は再起動）します。エージェントがアンインストール済みの場合は、再度インストールを実施して下さい。
3. サーバが新たなホストとして Mackerel に登録されます。

[https://mackerel.io/ja/docs/entry/howto/install-agent:embed:cite]
