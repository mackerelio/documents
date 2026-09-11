---
Title: Mackerel APM導入ガイド ― ゼロコード計装による最短データ取得
Date: 2026-09-11T11:01:03+09:00
URL: https://mackerel.io/ja/docs/entry/apm/getting-started/zero-code-instrumentation
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/14945776032076293257
---

「APMを導入したいけれども、アプリケーションのコードに手を入れるのは影響範囲が心配」「OpenTelemetryは聞いたことはあるが、何から始めればいいかわからない」と感じていませんか。

**ゼロコード計装**なら、アプリケーションのソースコードを一切変更せずに、トレースの収集を始められます。環境変数の設定と起動コマンドの調整だけで、Mackerel APMの画面に最初のトレースが表示されるところまで到達できます。

この章では、ゼロコード計装の仕組みを理解したうえで、**Mackerel OpenTelemetryコレクター**経由でMackerel APMにトレースを送信し、画面で確認するまでの流れを解説します。

[:contents]

## ゼロコード計装とは

**ゼロコード計装**は、アプリケーションのソースコードを変更せずに、トレースなどのテレメトリーデータを自動的に収集する手法です。ゼロコード計装には、大きく2つのアプローチがあります。

### ライブラリ計装

OpenTelemetryが提供する**ライブラリ計装**（インストルメンテーションライブラリ）が、HTTPリクエスト処理やデータベースクエリなど、主要なフレームワーク・ライブラリの動作を自動的にフックしてデータを記録します。アプリケーションの起動時に計装ライブラリを読み込ませることで、既存の処理に計装が挿入される仕組みです。

イメージとしては、アプリケーションの「入口」と「出口」に自動でセンサーを取り付けるようなものです。アプリケーション内部の動線そのものには手を加えません。

### OBI（OpenTelemetry eBPF Instrumentation）

**OBI**は、Linuxカーネルの**eBPF**（Extended Berkeley Packet Filter）技術を使って、アプリケーションのトラフィックをカーネルレベルで観測するアプローチです。

ライブラリ計装との最大の違いは、アプリケーション側にSDKや計装ライブラリをインストールする必要がない点です。OBI自体をデプロイするだけで、そのホスト上で動作するアプリケーションのHTTPリクエストやデータベースクエリを自動的に観測できます。

### 計装方式の比較

計装の方法には、ゼロコード計装（OBI・ライブラリ計装）のほかに**コードベース計装**があります。


| 観点 | OBI（eBPF計装） | ライブラリ計装 | コードベース計装 |
|------|----------------|--------------|----------------|
| コード変更 | 不要 | 不要 | 必要 |
| SDK・ライブラリのインストール | 不要 | 必要 | 必要 |
| アプリ側の環境変数設定 | 不要 | 必要 | 必要 |
| 対応言語 | 言語非依存 | 言語ごと | 言語ごと |
| 対応環境 | Linux限定 | 幅広い | 幅広い |
| データの粒度 | プロトコルレベル | フレームワークレベル | ビジネスロジックレベル |
| カスタムスパン | 不可 | 不可 | 可能 |
| 成熟度 | 開発中 | 安定 | 安定 |


OBIが最も手軽ですが、取得できるデータの粒度はプロトコルレベル（HTTPリクエスト/レスポンスなど）にとどまります。ライブラリ計装はフレームワーク内部の処理ステップまで可視化でき、コードベース計装はビジネスロジック固有のデータまで記録できます。

まずはゼロコード計装で全体像を把握し、必要に応じてコードベース計装で深掘りするのが現実的なアプローチです。

### ゼロコード計装で取得できるデータの例

ゼロコード計装では、言語やフレームワークに応じて以下のようなデータを自動的に取得できます。

* 受信したHTTPリクエストのURL、メソッド、ステータスコード、処理時間
* 外部サービスへのHTTPリクエストの詳細と応答時間
* データベースクエリの内容と実行時間
* フレームワーク固有の処理（ルーティング、ミドルウェアなど）

これらのデータがMackerel APMに送信されると、リクエストの処理フローと各ステップの所要時間をトレースとして可視化できます。

## 検証を始める前に ― 準備と前提条件

ゼロコード計装の検証を始める前に、いくつかの準備が必要です。

### Mackerelのトライアルオーガニゼーションを用意する

APMの検証には、**トライアルのオーガニゼーション**を新たに作成して使用することを強くおすすめします。

Mackerel APMのスタンダードプランでは、月間5M（500万）スパンまでが無料枠に含まれますが、超過分は1M（100万）スパンあたり330円（税込、2026年5月時点）が課金されます（[https://ja.mackerel.io/pricing](https://ja.mackerel.io/pricing)）。本番運用中のオーガニゼーションで検証を行うと、トラフィック量によっては想定外の課金が発生する可能性があります。

一方、トライアルでは**2週間・最大1億スパン**まで利用でき、検証には十分な枠が確保されています。トライアル期間の終了後は自動的にフリープランに移行するため、意図しない課金の心配もありません。

* Mackerelアカウントをお持ちでない場合は、アカウントを新規登録すれば、トライアルオーガニゼーションも作成できます

<figure class="figure-image figure-image-fotolife" title="Mackerelのサインアップ（https://mackerel.io/signup）">[f:id:mackerelio:20260909183646p:plain]<figcaption>Mackerelのサインアップ（https://mackerel.io/signup）</figcaption></figure>

* すでにMackerelをご利用中の場合は、検証用に別のトライアルオーガニゼーションを作成してください

<figure class="figure-image figure-image-fotolife" title="新しいオーガニゼーションの作成">[f:id:mackerelio:20260909183824p:plain]<figcaption>新しいオーガニゼーションの作成</figcaption></figure>

* Mackerelへのテレメトリーデータを送るのに使うAPIキーは、オーガニゼーション設定画面から確認できます。この後のコレクターの起動時に使用します

<figure class="figure-image figure-image-fotolife" title="APIキーの取得">[f:id:mackerelio:20260909183838p:plain]<figcaption>APIキーの取得</figcaption></figure>

### Mackerel OpenTelemetryコレクターの準備

テレメトリーデータをMackerelに送信するために、**Mackerel OpenTelemetryコレクター**をセットアップします。

Mackerel OpenTelemetryコレクターは、アプリケーションからテレメトリーデータを受け取り、Mackerelに転送する中継サービスです（[https://github.com/mackerelio/opentelemetry-collector-mackerel/blob/main/distributions/otelcol-mackerel/README.ja.md](https://github.com/mackerelio/opentelemetry-collector-mackerel/blob/main/distributions/otelcol-mackerel/README.ja.md)）。環境変数 `MACKEREL_APIKEY` を設定するだけで起動でき、設定ファイルの編集は不要です。

お使いの環境に合わせて、いずれかの方法でインストールしてください。

##### Docker

```
docker run -e MACKEREL_APIKEY=<YOUR_API_KEY> mackerel/otelcol-mackerel:latest
```

##### Linux（deb）

```
curl -fsSL https://mackerel.io/file/script/opentelemetry-collector-mackerel/setup-apt.sh | MACKEREL_APIKEY='<YOUR_API_KEY>' sh
```

##### Linux（rpm）

```
curl -fsSL https://mackerel.io/file/script/opentelemetry-collector-mackerel/setup-yum.sh | MACKEREL_APIKEY='<YOUR_API_KEY>' sh
```

デフォルト設定では、localhostの4317番ポート（gRPC）と4318番ポート（HTTP）でOTLPデータを受け付け、Mackerel APMに転送します。コレクターの詳しいカスタマイズ方法は[Mackerel OpenTelemetryコレクターによる送信設定](./otel-collector-setup)で解説しています。

なお、Mackerel OpenTelemetryコレクターはLinuxとDocker向けに提供されています。Windows環境では、公式のOpenTelemetry Collector Contribを使用してください。設定方法は[Mackerel OpenTelemetryコレクターによる送信設定](./otel-collector-setup)の「Windows環境での利用」で説明しています。

### 検証環境の前提条件

* 検証用のアプリケーションが動作する環境を用意してください。ローカル開発環境やステージング環境がおすすめです
* 本番環境での検証は、この段階では推奨しません。データ量やコストの見積もりがまだ済んでいないためです。見積もり方法は[コスト・データ量の見極め](./cost-estimation)で扱います

### 安全な検証のためのガードレール

検証を始める前に、いくつかの点を決めておきましょう。

* **サービス名を明確にする**：本番環境のデータと混在しないよう、検証用のサービス名を設定します（例：`my-app-staging`）
* **対象を絞る**：まずは1つのアプリケーション・1つのサービスだけに計装を適用します
* **検証期間を決めておく**：数時間〜1日程度を目安に検証期間を設定します

## OBI（OpenTelemetry eBPF Instrumentation）― もう1つのゼロコード計装

ライブラリ計装では、言語ごとにSDKや計装ライブラリをインストールし、環境変数を設定し、起動コマンドを調整する必要があります。**OBI**はこれらをすべて省略できる、より手軽なゼロコード計装の選択肢です（[https://opentelemetry.io/ja/docs/zero-code/obi/](https://opentelemetry.io/ja/docs/zero-code/obi/)）。

### OBIの仕組み

OBIは、Linuxカーネルの**eBPF**技術を使って、アプリケーションのネットワーク通信やシステムコールをカーネルレベルで観測します。アプリケーションプロセスの外側からトラフィックを捉えるため、アプリケーション側にはSDKのインストールも環境変数の設定も一切不要です。

<figure class="figure-image figure-image-fotolife" title="OBI eBPFのアーキテクチャ（ https://opentelemetry.io/docs/zero-code/obi/ に掲載の図「OBI eBPF architecture」をもとに翻訳）">[f:id:mackerelio:20260909184347p:plain]<figcaption>OBI eBPFのアーキテクチャ（ https://opentelemetry.io/docs/zero-code/obi/ に掲載の図「OBI eBPF architecture」をもとに翻訳）</figcaption></figure>

OBI自体を同じホスト上にデプロイするだけで、そのホスト上のアプリケーションのHTTP/gRPCリクエストやデータベースクエリを自動的に検出し、トレースデータとして記録します。Kubernetes環境では、DaemonSetとしてデプロイすることで、クラスター内の全ノードのアプリケーションを一括して観測できます。

OBIはOTLP形式でデータをエクスポートするため、Mackerel OpenTelemetryコレクターと連携できます。

### メリット

* **アプリケーションへの影響がゼロ**：SDK・ライブラリのインストールも、環境変数の設定も、起動コマンドの変更も不要
* **言語非依存**：eBPFはカーネルレベルで動作するため、Go、Java、Python、Node.js、Ruby、PHP、.NETなど、言語やランタイムを問わず利用できる
* **導入の速さ**：OBIをデプロイするだけで、すぐにトレースの収集が始まる

### 制約

* **Linux限定**：eBPFはLinuxカーネルの技術のため、WindowsやmacOSでは利用できない。カーネル5.8以降（BTF有効）が必要。またカーネルへのアクセス特権が必要なため、AWS Fargateのようなマネージドサーバーレス環境では利用できない
* **データの粒度がプロトコルレベルにとどまる**：HTTPリクエスト/レスポンスやSQLクエリの発行は観測できるが、フレームワーク内部のミドルウェア処理やビジネスロジックの個別ステップまでは可視化できない。ライブラリ計装であれば見えるルーティングやテンプレートレンダリングといった処理ステップは、OBIのトレースには含まれない
* **カスタムスパンの作成ができない**：アプリケーション固有の処理を独自のスパンとして記録することはできない
* **開発中のプロジェクト**：現在活発に開発されており、マイナーバージョン間で破壊的変更の可能性がある。本番環境への導入は、プロジェクトの成熟度を確認したうえで判断する必要がある

### 使い分けの指針

OBIとライブラリ計装は場面によって使い分けるのがよいでしょう。たとえば、まずOBIで手早くサービス全体のリクエストフローを把握し、詳細な可視化が必要なサービスにはライブラリ計装に切り替え、さらに詳細なビジネスロジックなどを見るためにコードベース計装を追加する、という段階的なアプローチが有効です。

この記事では、より詳細なトレースが得られる**ライブラリ計装**を中心にセットアップ手順を解説します。

## ライブラリ計装によるセットアップ ― 何をどう設定するのか

ゼロコード計装の全体的な流れは、次の4ステップです。

1. OpenTelemetry SDKと計装ライブラリをインストールする
2. 環境変数でエクスポート先（送信先）やサービス名を設定する
3. アプリケーションを起動する（起動方法に若干の変更あり）
4. Mackerel APMの画面でトレースが届いていることを確認する

### 環境変数による設定

OpenTelemetryでは、設定の多くを環境変数で制御できます。ゼロコード計装で使用する代表的な環境変数は以下のとおりです。


| 環境変数 | 説明 | 設定例 |
|---------|------|--------|
| `OTEL_SERVICE_NAME` | Mackerel APM上での識別名 | `my-web-app` |
| `OTEL_EXPORTER_OTLP_ENDPOINT` | テレメトリーデータの送信先URL | `http://localhost:4318` |
| `OTEL_EXPORTER_OTLP_PROTOCOL` | 送信プロトコル | `http/protobuf` |


ここでは概要を押さえておいてください。言語ごとの具体的な設定値は次のセクションで紹介します。

### 送信先 ― Mackerel OpenTelemetry コレクター経由を推奨

ゼロコード計装で収集したデータの送信先には、前のセクションでセットアップした**Mackerel OpenTelemetryコレクター経由での送信**を推奨しています。

コレクター経由の構成では、アプリケーションはローカルのコレクターにデータを送り、コレクターがMackerelに転送します。この構成には以下のメリットがあります。

* **送信処理の安定性**：アプリケーションの送信先がlocalhostになるため、ネットワーク遅延の影響を受けにくい
* **アプリケーションへの影響の遮断**：Mackerel APM側の障害やネットワーク問題が発生しても、アプリケーション自体の処理には影響が及ばない
* **認証情報の集約**：APIキーをコレクターに集約でき、アプリケーション側で管理する必要がない
* **本番と同じ構成**：検証段階から本番運用と同じ構成で進められるため、後から構成を変更する手間が不要

アプリケーションからMackerel APMへ直接送信する構成もとれますが、送信先の障害時にアプリケーションの処理がブロックされるリスクがあります。検証段階からコレクター経由で始めることをおすすめします。

<figure class="figure-image figure-image-fotolife" title="図：Mackerel OpenTelemetryコレクターのデータフロー">[f:id:mackerelio:20260910105941p:plain]<figcaption>図：Mackerel OpenTelemetryコレクターのデータフロー</figcaption></figure>

### コンテナ環境での計装（ECS・Kubernetes）

ECSやKubernetesなどのコンテナ環境でゼロコード計装を行う場合、ローカル環境とはいくつか異なる点があります。

##### コンテナイメージへの計装ライブラリの組み込み

コンテナ環境では、計装ライブラリやSDKをコンテナイメージに含めてビルドする必要があります。Dockerfileに`npm install`や`pip install`などのインストール手順を追加してください。言語ごとの具体的なインストール手順は、次の「言語別のセットアップ概要」セクションを参照してください。

##### 環境変数の設定

`OTEL_SERVICE_NAME` や `OTEL_EXPORTER_OTLP_ENDPOINT` などの環境変数は、ECSのタスク定義やKubernetesのDeploymentマニフェストでコンテナに渡します。

##### コレクターの配置

コンテナ環境では、Mackerel OpenTelemetryコレクターの配置方法として**サイドカー型**と**ゲートウェイ型**の2つのパターンがあります。

* **サイドカー型**：コレクターをアプリケーションと同じPod（Kubernetes）やタスク（ECS）にサイドカーコンテナとして配置します。アプリケーションからの送信先が `http://localhost:4318` のままで済むため、構成がシンプルです

  <figure class="figure-image figure-image-fotolife" title="サイドカー型の配置">[f:id:mackerelio:20260909184003p:plain]<figcaption>サイドカー型の配置</figcaption></figure>

* **ゲートウェイ型**：コレクターを独立したサービスとしてデプロイし、複数のアプリケーションからのデータを集約します。`OTEL_EXPORTER_OTLP_ENDPOINT` にはコレクターのサービスアドレス（例：`http://otel-collector.monitoring:4318`）を指定します

  <figure class="figure-image figure-image-fotolife" title="ゲートウェイ型の配置">[f:id:mackerelio:20260909184048p:plain]<figcaption>ゲートウェイ型の配置</figcaption></figure>

検証段階ではサイドカー型から始めるのがおすすめです。配置パターンの詳しい解説は[Mackerel OpenTelemetryコレクターによる送信設定](./otel-collector-setup)の「コレクターの配置パターン」を参照してください。

## 言語別のセットアップ概要

ここからは、**ライブラリ計装**方式でのセットアップ手順を言語ごとに紹介します。各言語の詳細な手順やトラブルシューティングは別章で解説しています。

### Node.jsの場合

##### 1. パッケージのインストール

```bash
npm install @opentelemetry/auto-instrumentations-node
```

##### 2. 環境変数の設定

```bash
export OTEL_SERVICE_NAME="my-node-app"
export OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:4318"
export OTEL_EXPORTER_OTLP_PROTOCOL="http/protobuf"
```

`OTEL_EXPORTER_OTLP_ENDPOINT` は、Mackerel OpenTelemetryコレクターが稼働しているアドレスを指定します。コレクターをデフォルト設定で起動している場合は `http://localhost:4318` です。

##### 3. アプリケーションの起動

通常の起動コマンドに `--require` オプションを追加して、計装ライブラリを読み込ませます。

```bash
node --require @opentelemetry/auto-instrumentations-node/register app.js
```

詳細は[Node.jsにおけるゼロコード計装](./nodejs-zero-code-instrumentation)を参照してください。

### PHPの場合

##### 1. PHP拡張のインストール

OpenTelemetry PHP拡張（ext-opentelemetry）をインストールします。

```bash
pecl install opentelemetry
```

拡張を有効化するために、PHPエンジンの決められた設定場所に追加します。たとえば`/usr/local/etc/php/conf.d`が設定場所であれば、`opentelemetry.ini`ファイルとして以下を記述します。

```
[opentelemetry]
extension=opentelemetry.so
```

##### 2. 計装ライブラリのインストール

Composerで計装ライブラリをインストールします。

```bash
composer require open-telemetry/sdk open-telemetry/exporter-otlp open-telemetry/auto-instrumentation-laravel
```

ここではLaravelの計装ライブラリをインストールしています。

##### 3. 環境変数の設定

```bash
export OTEL_SERVICE_NAME="my-php-app"
export OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:4318"
export OTEL_EXPORTER_OTLP_PROTOCOL="http/protobuf"
export OTEL_PHP_AUTOLOAD_ENABLED=true
```

PHPの場合は、HTTPプロトコル（ポート4318）での送信が一般的です。

詳細は[PHPにおけるゼロコード計装](./php-zero-code-instrumentation)を参照してください。

### Javaの場合（Java Agent）

Java Agentは、JVMの `-javaagent` オプションを使ったゼロコード計装です。アプリケーションコードの変更なしに、Spring、JDBC、Servletなど150以上のライブラリを自動計装できます。

##### 1. Java Agentのダウンロード

```bash
curl -OL https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/latest/download/opentelemetry-javaagent.jar
```

##### 2. 環境変数の設定

```bash
export OTEL_SERVICE_NAME="my-java-app"
export OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:4318"
export OTEL_EXPORTER_OTLP_PROTOCOL="http/protobuf"
```

##### 3. アプリケーションの起動

`-javaagent` オプションは `-jar` より前に指定する必要があります。

```bash
java -javaagent:opentelemetry-javaagent.jar -jar app.jar
```

詳細は[Java Agentによるゼロコード計装](./java-agent-instrumentation)を参照してください。

### Javaの場合（Spring Boot Starter）

Spring Bootアプリケーションでは、**OpenTelemetry Spring Boot Starter**を依存関係に追加する方式も利用できます。Java Agentとは異なり、バイトコード操作を行わないため、GraalVM Native Imageとの互換性があります。Spring Initializrで「OpenTelemetry」を選択すると自動的に追加されます。

##### 1. 依存関係の追加（Maven）

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-opentelemetry</artifactId>
    </dependency>
</dependencies>
```

##### 2. 設定（application.yml）

```yaml
spring:
  application:
    name: my-spring-app
management:
  opentelemetry:
    tracing:
      export:
        otlp:
          endpoint: http://localhost:4318/v1/traces
  tracing:
    sampling:
      probability: 1.0
```

コード変更は不要ですが、依存関係の追加とビルドが必要なため、厳密にはゼロコード計装ではありません。Java Agentとの違いや使い分けは[Spring BootにおけるOpenTelemetry計装](./spring-boot-instrumentation)を参照してください。

### Pythonの場合

##### 1. パッケージのインストール

```bash
pip install opentelemetry-distro opentelemetry-exporter-otlp
opentelemetry-bootstrap -a install
```

`opentelemetry-bootstrap` コマンドが、インストール済みのフレームワーク（Flask、Django、requestsなど）を自動検出し、対応する計装ライブラリをインストールします。

##### 2. 環境変数の設定

```bash
export OTEL_SERVICE_NAME="my-python-app"
export OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:4318"
export OTEL_EXPORTER_OTLP_PROTOCOL="http/protobuf"
```

##### 3. アプリケーションの起動

通常の `python` コマンドの代わりに `opentelemetry-instrument` 経由で起動します。

```bash
opentelemetry-instrument python app.py
```

詳細は[Pythonにおけるゼロコード計装](./python-zero-code-instrumentation)を参照してください。

### .NETの場合

.NETのゼロコード計装は、CLRプロファイラーを使ってアプリケーションの動作を自動的にフックします。

##### 1. インストールスクリプトの実行

```bash
curl -sSfL https://github.com/open-telemetry/opentelemetry-dotnet-instrumentation/releases/latest/download/otel-dotnet-auto-install.sh -O
sh ./otel-dotnet-auto-install.sh
```

##### 2. 環境変数の設定と起動

インストールスクリプトが生成する `instrument.sh` を読み込んだうえで、アプリケーションを起動します。

```bash
. $HOME/.otel-dotnet-auto/instrument.sh
export OTEL_SERVICE_NAME="my-dotnet-app"
export OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:4318"
./MyApp
```

詳細は[.NETにおけるゼロコード計装](./dotnet-zero-code-instrumentation)を参照してください。

### Ruby on Railsの場合（コードベース計装）

Railsはゼロコード計装には対応していませんが、イニシャライザーに数行のコードを追加することで計装できます。ここではコードベース計装の初期設定を紹介します。

##### 1. Gemのインストール

```ruby
# Gemfile
gem 'opentelemetry-sdk'
gem 'opentelemetry-exporter-otlp'
gem 'opentelemetry-instrumentation-all'
```

```bash
bundle install
```

`opentelemetry-instrumentation-all` は、Rails、ActiveRecord、ActiveJobなど主要なコンポーネントの計装ライブラリをまとめたメタパッケージです。

##### 2. イニシャライザーの作成

```ruby
# config/initializers/opentelemetry.rb
require 'opentelemetry/sdk'
require 'opentelemetry/instrumentation/all'

OpenTelemetry::SDK.configure do |c|
  c.service_name = ENV.fetch('OTEL_SERVICE_NAME', 'my-rails-app')
  c.use_all
end
```

##### 3. 環境変数の設定と起動

```bash
export OTEL_SERVICE_NAME="my-rails-app"
export OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:4318"
rails server
```

詳細は[Ruby on RailsにおけるOpenTelemetry計装](./rails-instrumentation)を参照してください。

### その他の言語

OpenTelemetryは上記のほかにも、Goなど複数の言語に対応しています。各言語でのゼロコード計装の手順は、OpenTelemetry公式ドキュメントで確認できます（[https://opentelemetry.io/ja/docs/zero-code/](https://opentelemetry.io/ja/docs/zero-code/)）。

## 最初のトレースを確認してみよう

セットアップが完了したら、実際にトレースデータを確認してみましょう。

### アプリケーションにリクエストを送る

検証用アプリケーションにいくつかのリクエストを送信します。ブラウザでアクセスしたり、curlでAPIを叩いたりしてみてください。

```bash
curl http://localhost:3000/
curl http://localhost:3000/api/users
```

データがMackerel APMに届くまで、少し時間がかかる場合があります。

### Mackerel APM画面での確認

1. MackerelのWebコンソールにサインインし、サイドバーの「APM」をクリックします
2. サービス一覧に、設定したサービス名（`OTEL_SERVICE_NAME` で指定した名前）が表示されていることを確認します

   <figure class="figure-image figure-image-fotolife" title="サイドバーの「APM」からサービス名をクリック">[f:id:mackerelio:20260909184117p:plain]<figcaption>サイドバーの「APM」からサービス名をクリック</figcaption></figure>


3. トレース一覧画面を開き、送信したリクエストが記録されていることを確認します

   <figure class="figure-image figure-image-fotolife" title="「トレース」タブをクリックし、一覧を表示">[f:id:mackerelio:20260909184244p:plain]<figcaption>「トレース」タブをクリックし、一覧を表示</figcaption></figure>

4. 個別のトレースをクリックして、スパン（処理の単位）の階層構造を見てみましょう

   <figure class="figure-image figure-image-fotolife" title="トレースのスパンの階層構造を表示">[f:id:mackerelio:20260909184306p:plain]<figcaption>トレースのスパンの階層構造を表示</figcaption></figure>

### 何が見えるか ― トレースとスパンの読み解き方

トレースを開くと、リクエストの処理が**スパン**の階層構造として表示されます。

**スパン**は、トレースを構成する処理の最小単位です。「HTTPリクエストを受け付ける」「データベースにクエリを発行する」「外部APIを呼び出す」といった個々の処理が、それぞれ1つのスパンとして記録されます。各スパンには処理名・所要時間・ステータスなどの情報が含まれており、スパンが親子関係で連なることで、リクエスト全体の処理フローが1つのトレースとして表現されます。

<!-- キャプチャ指示: Mackerel APMのトレース詳細画面のスクリーンショット。1つのトレースを展開した状態で、ルートスパン（HTTPハンドラ）の下に子スパン（データベースクエリなど）が階層表示されている画面。各スパンの処理時間がバー（ウォーターフォール表示）で可視化されていること -->

トレース画面で確認できる代表的なスパンの構造は以下のとおりです。

* **ルートスパン**：トレースの最上位にあるスパンで、リクエスト全体の処理時間を表します。HTTPリクエストのメソッド、URL、ステータスコードなどの情報が記録されています
* **子スパン**：ルートスパンの中で行われた個別の処理です。データベースクエリの実行、外部APIの呼び出し、ミドルウェアの処理などがそれぞれスパンとして記録されます
* **スパンの属性**：各スパンには、処理名や所要時間のほかに、URLやSQLクエリの内容などの詳細情報が付与されています

<figure class="figure-image figure-image-fotolife" title="トレースとスパン">[f:id:mackerelio:20260909184322p:plain]<figcaption>トレースとスパン</figcaption></figure>

たとえば、あるAPIリクエストのトレースを見ると「HTTPハンドラで200ms、そのうちデータベースクエリに150msかかっている」といったことが一目でわかります。コードに手を入れなくても、どの処理に時間がかかっているかがすぐに把握できる。これがゼロコード計装の価値です。

なお、Mackerel APMの利用料金はスパン数に基づいて計算されます。データ量やコストの見積もり方法は[コスト・データ量の見極め](./cost-estimation)で詳しく扱います。

## うまくいかないときは ― よくある問題と対処法

### トレースが表示されない場合

最も多いケースとその確認ポイントを挙げます。

* **コレクターが起動しているか**：コレクターのログにエラーが出ていないか確認してください。正常に起動していれば `Everything is ready. Begin running and processing data.` というメッセージが表示されます
* **送信先のアドレスが正しいか**：アプリケーションの `OTEL_EXPORTER_OTLP_ENDPOINT` がコレクターのアドレスを正しく指しているか確認してください。デフォルトは `localhost:4317`（gRPC）または `http://localhost:4318`（HTTP）です
* **Dockerの場合のネットワーク**：アプリケーションとコレクターをそれぞれDockerコンテナで動かしている場合、コンテナ間のネットワーク設定を確認してください。`localhost` ではなくコンテナ名やネットワークエイリアスの指定が必要な場合があります
* **APIキーが正しいか**：コレクターの `MACKEREL_APIKEY` 環境変数が正しく設定されているか確認してください

### 期待したスパンが出ない場合

* 使用しているフレームワークやライブラリが計装対象に含まれているか確認してください。ゼロコード計装で取得できるのは、ライブラリ計装が対応しているフレームワーク・ライブラリの処理に限られます
* 対応ライブラリの一覧は、OpenTelemetry公式のレジストリで確認できます

### データ量が多すぎると感じたら

検証段階では、まずはすべてのトレースを送信して全体像を把握することを優先してください。データ量が気になる場合は、[フィルタリングとサンプリングによるデータ量・コストの最適化](./filtering-and-sampling)で不要なデータを削減する方法を解説しています。

## 次のステップへ

ここまでの手順で、以下のことが達成できました。

* アプリケーションコードを変更せずに、ゼロコード計装でトレースデータを収集した
* Mackerel OpenTelemetryコレクター経由で、安定した送信構成を最初から採用した
* Mackerel APMの画面でリクエストの処理フローと各ステップの所要時間を確認した

最初のトレースが見えたことで、「アプリケーション内部で何が起きているか」を可視化する感覚が掴めたのではないでしょうか。ここからは、目的に応じて次のステップに進んでみてください。

* **コレクターの設定をもっと詳しく知りたい** → [Mackerel OpenTelemetryコレクター による送信設定](./otel-collector-setup)で、コレクターの仕組みやカスタム設定の方法を解説しています
* **取得したデータを分析したい** → [短期検証による可視化分析](./verification-analysis)で、トレースやREDメトリックから何を読み取れるかを解説しています
* **本番運用のコストを見積もりたい** → [コスト・データ量の見極め](./cost-estimation)で、検証データから月間コストを算出する方法を扱っています
* **APMの基本概念をもう少し理解したい** → [Mackerel APMの基礎理解](./mackerel-apm-basics)で、APMの全体像を解説しています

ゼロコード計装で最初のトレースを取得したら、ぜひデータの分析やコストの見積もりにも取り組んでみてください。

<nav>
<ul style="display:flex;justify-content:space-between;padding:0;margin:0">
<li style="list-style:none">前の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/mackerel-apm-basics" rel="prev">Mackerel APM の基礎理解</a></li>
<li style="list-style:none;margin-left:auto">次の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/otel-collector-setup" rel="next">Mackerel OpenTelemetryコレクターによる送信設定</a></li>
</ul>
</nav>
