---
Title: 「ホスト」とは
Date: 2020-09-02T12:44:26+09:00
URL: https://mackerel.io/ja/docs/entry/spec/about-host
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613625779272
CustomPath: spec/about-host
---

Mackerelでは、「そのシステムを構成する最小単位」をホストとして扱います。1台の物理サーバーであったり、1つの仮想サーバーであったり、ときにはマネージドサービスであることもあります。

[f:id:mackerelio:20200902124054p:plain]

Mackerelで監視をはじめるためには、監視対象をホストとして登録する必要があります。監視対象の種類に応じた様々な方法で、Mackerel にホストを登録することができます。

<h2 id="howto-register-host-by-kinds">監視対象の種類ごとのホスト登録方法</h2>
監視対象を Mackerel にホスト登録する代表的な方法として、以下の3つの方法があります。

- [物理サーバーや仮想サーバーを監視するために、Mackerel にホスト登録する](#register-by-mackerel-agent)
- [Kubernetes などのコンテナ環境を監視するために、Mackerel にホスト登録する](#register-by-container-agent)
- [パブリッククラウドが提供するマネージドサービスを監視するために、Mackerel にホスト登録する](#register-by-cloud-integration)

<h3 id="register-by-mackerel-agent">物理サーバーや仮想サーバーを監視するために、Mackerel にホスト登録する</h3>
物理サーバーや仮想サーバーを Mackerel で監視する場合は、Mackerel 公式の監視エージェントソフトウェアである mackerel-agent を OS にインストールします。
以下のヘルプページを参照してください。

[https://mackerel.io/ja/docs/entry/howto/install-agent:title]

インストールした mackerel-agent が正常に動作した場合、そのホストの情報が Mackerel に「スタンダードホスト」として登録されます。詳しくは [「ホスト」の種類について](#host-kinds) を参照してください。

<h3 id="register-by-container-agent">Kubernetes などのコンテナ環境を監視するために、Mackerel にホスト登録する</h3>
Kubernetes などのコンテナ環境を Mackerel で監視する場合は、Mackerel 公式のコンテナ環境向けの監視エージェントソフトウェアである mackerel-container-agent を、対象の環境にサイドカーコンテナとして実行します。
以下のヘルプページを参照してください。

[https://mackerel.io/ja/docs/entry/howto/container-agent:title]

サイドカーとして実行された mackerel-container-agent が正常に動作した場合、そのホストの情報が Mackerel に「マイクロホスト」として登録されます。詳しくは [「ホスト」の種類について](#host-kinds) を参照してください。

<h3 id="register-by-cloud-integration">パブリッククラウドが提供するマネージドサービスを監視するために、Mackerel にホスト登録する</h3>
例えば以下のようなものも、Mackerel にホストとして登録し、統合的に監視することができます。

- Amazon Web Services（AWS）が提供するマネージドサービス・Amazon RDS など
- Microsoft Azure が提供するマネージドサービス・Azure App Service など

上記のようなマネージドサービスを Mackerel で監視するには、以下のようなクラウドインテグレーションを使用します。

- [https://mackerel.io/ja/docs/entry/integrations/aws:title]
- [https://mackerel.io/ja/docs/entry/integrations/azure:title]

クラウドインテグレーションによる連携が正常におこなえた場合、そのホストの情報が Mackerel に「スタンダードホスト」もしくは「マイクロホスト」として登録されます。詳しくは [「ホスト」の種類について](#host-kinds) を参照してください。

<h2 id="host-kinds">「ホスト」の種類について</h2>
Mackerel に登録されたホストは、自動的に「スタンダードホスト」「マイクロホスト」のいずれかに分類されます。登録する際に自由に選択することはできません。

<h3 id="billing-unit">「スタンダードホスト」「マイクロホスト」の特徴</h3>
<h4 id="standard-host">スタンダードホスト</h4>
- [料金表](https://mackerel.io/ja/pricing/) の「スタンダードホスト」の価格が適用されます。
- メトリック登録が200個までであれば、1つのスタンダードホストとして利用可能です。
	- [プラン上限超過時のホスト台数換算について](https://support.mackerel.io/hc/ja/articles/360040109431) も参照してください。

<h4 id="micro-host">マイクロホスト</h4>
- [料金表](https://mackerel.io/ja/pricing/) の「マイクロホスト」の価格が適用されます。
- メトリック登録が30個までであれば、1つのマイクロホストとして利用可能です。
	- [プラン上限超過時のホスト台数換算について](https://support.mackerel.io/hc/ja/articles/360040109431) も参照してください。


<h3 id="casestudy">ケーススタディ</h3>
<h4 id="casestudy-mackerel-agent">mackerel-agent を使ってホストを登録した場合</h4>
スタンダードホストとして登録されます。

<h4 id="casestudy-mackerel-container-agent">mackerel-container-agent を使ってホストを登録した場合</h4>
マイクロホストとして登録されます。

<h4 id="casestudy-integration-standard-host">クラウドインテグレーションを利用し、VM系サービスをホスト登録した場合</h4>
スタンダードホストとして登録されます。VM系サービスとは、以下のいずれかを指します。

- Amazon EC2
- Azure Virtual Machines
- Google Compute Engine

mackerel-agent のインストールと併用することも可能です。詳しくは以下を参照してください。

[https://support.mackerel.io/hc/ja/articles/360040109451:title]

<h4 id="casestudy-integration-micro-host">クラウドインテグレーションを利用し、VM系サービス以外をホスト登録した場合</h4>
マイクロホストとして登録されます。

<h4 id="casestudy-register-by-api">ホスト登録APIを利用し、ホスト登録した場合</h4>
Mackerelでは、[ホスト登録API](https://mackerel.io/ja/api-docs/entry/hosts#create)を用いてホストを登録することができます。

その場合、登録されるホストはスタンダードホストとなります。マイクロホストとして登録する方法はありません。
