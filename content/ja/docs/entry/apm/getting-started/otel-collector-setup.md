---
Title: Mackerel APM導入ガイド ― Mackerel OpenTelemetryコレクターによる送信設定
Date: 2026-09-11T11:01:12+09:00
URL: https://mackerel.io/ja/docs/entry/apm/getting-started/otel-collector-setup
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/14945776032076293301
---

[ゼロコード計装による最短データ取得](./zero-code-instrumentation)では、Mackerel OpenTelemetryコレクターをデフォルト設定で起動し、最初のトレースをMackerel APMに送信しました。環境変数 `MACKEREL_APIKEY` を設定するだけでデータが流れ始める手軽さを体験できたかと思います。

一方で、「コレクターが中で何をしているのか、もう少し理解したい」「不要なデータの除外や送信先の変更など、設定をカスタマイズしたい場面が出てきた」と感じた方もいるのではないでしょうか。

この章では、OpenTelemetryコレクターの仕組み（パイプラインの概念）を理解したうえで、Mackerel OpenTelemetryコレクターのデフォルト設定が何をしているかを解き明かし、カスタム設定ファイル（config.yaml）を書いて構成をカスタマイズする方法を解説します。コレクターの中身を理解することで、自信を持って運用に臨めるようになります。

[:contents]

## OpenTelemetryコレクターの仕組み

### コレクターの役割

OpenTelemetryコレクターは、アプリケーションとバックエンド（Mackerel APM）の間に置く「中継地点」です。アプリケーションからテレメトリーデータを受け取り、加工・変換してからバックエンドに転送します。

たとえるなら、アプリケーションが「荷物を出す送り主」だとすれば、コレクターは「集荷センター」です。送り主は近くの集荷センターに荷物を渡すだけでよく、仕分けや最適な配送ルートの選択はセンターが担います。

### パイプラインの構成要素

コレクターの中核となるのが**パイプライン**の仕組みです。パイプラインは3種類のコンポーネントで構成されます。

* **Receivers（レシーバー）**：データの受け取り口。アプリケーションからのOTLP形式のデータを受信します
* **Processors（プロセッサー）**：受け取ったデータの加工を担います。メモリ制限、フィルタリング、サンプリングなどが可能です
* **Exporters（エクスポーター）**：加工済みデータの送信先。Mackerel APMのエンドポイントへ転送します

これらを `Receivers → Processors → Exporters` の順にパイプラインとして組み合わせ、シグナルごと（traces、metrics）に定義します。

<figure class="figure-image figure-image-fotolife" title="図：OpenTelemetryコレクターのパイプライン">[f:id:mackerelio:20260910110143p:plain]<figcaption>図：OpenTelemetryコレクターのパイプライン</figcaption></figure>

このほかに、パイプライン間をつなぐ**Connectors（コネクター）**というコンポーネントもあります。コネクターはあるパイプラインのExporter側と別のパイプラインのReceiver側の両方の役割を兼ね、シグナルを変換して橋渡しします。たとえば `spanmetrics` コネクターは、tracesパイプラインからスパンデータを受け取り、リクエスト数・エラー率・レイテンシーといったREDメトリックを生成してmetricsパイプラインに流します。

### コレクターを導入するメリット

コレクターを導入する理由は、[ゼロコード計装による最短データ取得](./zero-code-instrumentation)でも触れましたが、改めて整理します。

* **送信処理の安定性**：アプリケーションの送信先がlocalhostになるため、ネットワーク遅延の影響を受けにくい
* **アプリケーションへの影響の遮断**：テレメトリーデータの送信処理をコレクターにオフロード（肩代わり）させることで、Mackerel APM側の障害やネットワーク問題がアプリケーション自体の処理に影響を及ぼさなくなる
* **認証情報の集約**：APIキー（`MACKEREL_APIKEY`）をコレクターに集約でき、アプリケーション側で管理する必要がない
* **送信前のデータ加工**：フィルタリングやサンプリングなど、データをMackerel APMに送る前に加工できる
* **複数アプリケーションの集約**：複数のアプリケーションからのデータを1か所で管理できる

## Mackerel OpenTelemetryコレクターとは

### Mackerel専用ディストリビューション

OpenTelemetryコレクターには公式の「Core」版と「Contrib」版があり、さらにベンダーが独自のディストリビューションを配布している場合があります。

**Mackerel OpenTelemetryコレクター**は、Mackerelへの送信に必要なコンポーネントをあらかじめ組み込んだディストリビューションです。GitHubのリポジトリでオープンソースソフトウェアとして公開されています（[https://github.com/mackerelio/opentelemetry-collector-mackerel](https://github.com/mackerelio/opentelemetry-collector-mackerel)）。

対応環境はLinuxとDocker（Linuxコンテナ）です。Windowsについては後述の「Windows環境での利用」を参照してください。

### 最大の特徴：設定ファイル不要で始められる

Mackerel OpenTelemetryコレクターの最大の特徴は、環境変数 `MACKEREL_APIKEY` を設定するだけで起動できることです。設定ファイルを1から書く必要がないため、OpenTelemetryコレクターの設定に詳しくなくてもすぐに始められます。

デフォルト設定（`--config=mackerel:default`）では、以下のパイプラインが自動的に構成されます。

* **Receiver**：localhostの4317番ポート（gRPC）と4318番ポート（HTTP）でOTLPデータを受信
* **Processor**：`resourcedetection`（ホストリソース属性の自動検出）
* **Exporter**：`mackerel_otlp`（Mackerelのエンドポイントへ送信。APIキーは環境変数 `MACKEREL_APIKEY` から自動取得）
* **パイプライン**：metricsとtracesの両方を処理

前の章でコレクターを起動した際、特に設定ファイルを用意しなかったのは、このデフォルト設定が自動的に適用されていたためです。

### 環境変数によるカスタマイズ

設定ファイルを書かなくても、環境変数でいくつかの基本的なカスタマイズが可能です。


| 環境変数 | 説明 | デフォルト値 |
|---------|------|------------|
| `MACKEREL_APIKEY` | Mackerel APMのAPIキー（必須） | ― |
| `OTELCOL_MACKEREL_HOST` | OTLPレシーバーのバインドアドレス | `localhost` |
| `OTELCOL_MACKEREL_SAMPLING_PERCENTAGE` | トレースの確率的サンプリング率 | ―（サンプリングなし） |


たとえば、トレースの10%だけをサンプリングしたい場合は、以下の環境変数を追加するだけで設定できます（debまたはrpmの場合は/etc/otelcol-mackerel/otelcol-mackerel.confに追加してotelcol-mackerelを再起動します）。

```
OTELCOL_MACKEREL_SAMPLING_PERCENTAGE=10
```

この段階では設定ファイルを書く必要はありません。多くの検証シナリオでは、デフォルト設定と環境変数の組み合わせで十分に対応できます。

### 組み込み済みの主要コンポーネント

Mackerel OpenTelemetryコレクターには、Mackerel APMの利用に便利なコンポーネントがあらかじめ選定・組み込まれています。カスタム設定ファイルを書く際に、以下のコンポーネントを追加のインストールなしで利用できます。

##### Exporters

* `mackerel_otlp`：Mackerel APM専用エクスポーター
* `otlp`（gRPC）、`otlp_http`：汎用OTLPエクスポーター
* `debug`：デバッグ用出力

##### Processors

* `batch`：バッチ処理（`sending_queue.batch` での設定を推奨）
* `memory_limiter`：メモリ使用量の制限
* `filter`：条件に基づくデータのフィルタリング
* `tail_sampling`：テイルベースサンプリング
* `probabilistic_sampler`：確率的サンプリング
* `attributes`、`resource`、`transform`：データの属性操作・変換
* `resourcedetection`：ホストリソース属性の自動検出
* `span`：スパンの属性操作

##### Receivers

* `otlp`：OTLP形式のデータ受信（gRPC/HTTP）
* `hostmetrics`：ホストメトリックの収集
* `postgresql`、`mysql`、`redis`：データベースメトリックの収集

##### Connectors

* `spanmetrics`：スパンからメトリックを生成
* `servicegraph`：サービス間の呼び出し関係からメトリックを生成

## デフォルト設定からカスタム設定へ ― config.yamlを書いてみる

### いつカスタム設定が必要になるか

デフォルト設定と環境変数で多くの場面に対応できますが、以下のようなケースではカスタム設定ファイル（config.yaml）を書く必要があります。

* 不要なトレースをフィルタリングしたい（ヘルスチェックの除外など）
* テイルベースサンプリングで「エラートレースだけ確実に残す」といった条件付きの制御をしたい
* 複数の送信先（Mackerel APM + デバッグ出力など）にデータを送りたい
* Receiverをlocalhost以外でリッスンさせたい（ゲートウェイ構成など）
* エクスポーターのバッチ送信やメモリ制限の設定を細かく調整したい

### 最小限のカスタム設定

まずは、デフォルト設定とほぼ同等の動作をするカスタム設定ファイルを作ってみましょう。コレクターの設定ファイルがどのような構造になっているかを掴むのが目的です。

```yaml
receivers:
  otlp:
    protocols:
      grpc:
      http:

exporters:
  mackerel_otlp:

service:
  pipelines:
    metrics:
      receivers: [otlp]
      exporters: [mackerel_otlp]
    traces:
      receivers: [otlp]
      exporters: [mackerel_otlp]
```

各セクションの役割は以下のとおりです。

* **receivers**：アプリケーションからデータを受け取る口を定義します。`otlp` レシーバーのgRPC（4317番ポート）とHTTP（4318番ポート）を有効にしています
* **exporters**：データの送信先を定義します。`mackerel_otlp` はMackerel APM専用のエクスポーターで、環境変数 `MACKEREL_APIKEY` を自動的に参照します。設定ファイル内にAPIキーを直接書く必要はありません
* **service.pipelines**：上記のコンポーネントをパイプラインとして組み立てます。metricsとtracesそれぞれについて、どのReceiver・Exporterを使うかを定義しています

この設定ではProcessorを指定していないため、受け取ったデータがそのままMackerelに送信されます。

### プロセッサーを追加した構成

本番運用に向けて、プロセッサーの追加とエクスポーターのバッチ送信設定をしてみましょう。

```yaml
receivers:
  otlp:
    protocols:
      grpc:
      http:

processors:
  memory_limiter:
    check_interval: 1s
    limit_mib: 512
  resourcedetection:
    detectors: [env, system]

exporters:
  mackerel_otlp:
    sending_queue:
      batch:
        flush_timeout: 5s
        min_size: 1024

service:
  pipelines:
    metrics:
      receivers: [otlp]
      processors: [memory_limiter, resourcedetection]
      exporters: [mackerel_otlp]
    traces:
      receivers: [otlp]
      processors: [memory_limiter, resourcedetection]
      exporters: [mackerel_otlp]
```

追加した設定の役割は以下のとおりです。

* **memory_limiter**（プロセッサー）：コレクターのメモリ使用量に上限を設けます。大量のデータが流入した場合に、コレクターがメモリを使い切ってクラッシュするのを防ぎます
* **resourcedetection**（プロセッサー）：ホスト名やOS情報などのリソース属性を自動検出してスパンに付与します。デフォルト設定（`--config=mackerel:default`）にも含まれているプロセッサーです
* **sending_queue.batch**（エクスポーター設定）：データを一定量または一定時間ごとにまとめて送信します。1件ずつ送信するのに比べてネットワーク接続数が減り、データ圧縮の効率も向上します。この例では5秒ごと、または1MB（1,024バイト）溜まったタイミングで送信します（Mackerel OpenTelemetryコレクターのデフォルト設定は、10秒ごと、または5MBです）

`service.pipelines` でプロセッサーを指定する順序には意味があります。データはリストの先頭から順に各プロセッサーを通過するため、`[memory_limiter, resourcedetection]` と書くと、まずメモリ制限をチェックし、その後リソース属性の検出が行われます。`memory_limiter` はデータ流入量を制御する役割があるため、先頭に置くのが推奨されています。フィルタリングやサンプリングのプロセッサーを追加する場合も、`memory_limiter` を先頭に維持してください。

### カスタム設定での起動方法

カスタム設定ファイルを作成したら、起動時に `--config` オプションで指定します。

##### Docker

```bash
docker run \
  -e MACKEREL_APIKEY=<YOUR_API_KEY> \
  --mount type=bind,src=./config.yaml,dst=/home/nonroot/config.yaml \
  mackerel/otelcol-mackerel:latest \
  --config /home/nonroot/config.yaml
```

##### Linux（systemd）

deb/rpmパッケージでインストールした場合は、以下の手順で設定を変更します。

1. `/etc/otelcol-mackerel/config.yaml` に設定ファイルを配置する
2. `/etc/otelcol-mackerel/otelcol-mackerel.conf` の `OTELCOL_MACKEREL_OPTIONS` を変更する：
   ```
   OTELCOL_MACKEREL_OPTIONS="--config=/etc/otelcol-mackerel/config.yaml"
   ```
3. コレクターを再起動する：
   ```bash
   sudo systemctl restart otelcol-mackerel
   ```

起動前に設定ファイルのバリデーションを行うと安心です。

```bash
otelcol-mackerel --config config.yaml validate
```

エラーがなければ `Config validation succeeded` と表示されます。

## 動作確認とトラブルシューティング

### 正常動作の確認ポイント

カスタム設定でコレクターを起動したら、以下の点を確認してください。

* 起動ログに `Everything is ready. Begin running and processing data.` が表示されるか
* ログに `Starting GRPC server` / `Starting HTTP server` が表示され、レシーバーがリッスンしているか
* Mackerel APMの画面でトレースが更新されているか
* アプリケーション側にエクスポートエラーのログが出ていないか

### よくある問題と対処法

##### コレクターにデータが届かない場合

* アプリケーションの `OTEL_EXPORTER_OTLP_ENDPOINT` がコレクターのアドレスを正しく指しているか確認してください。デフォルトは `localhost:4317`（gRPC）または `http://localhost:4318`（HTTP）です
* Dockerの場合、アプリケーションとコレクターのネットワークが通信可能か確認してください。`docker-compose` のネットワーク設定や `--network` オプションの指定が必要な場合があります
* `OTELCOL_MACKEREL_HOST` やカスタム設定でバインドアドレスを変更している場合、アプリケーション側のエンドポイントと一致しているか確認してください

##### コレクターは受信しているが、Mackerel APMにデータが届かない場合

* `MACKEREL_APIKEY` 環境変数が正しく設定されているか確認してください
* カスタム設定の場合、エクスポーターが `mackerel_otlp` になっているか確認してください。汎用の `otlp` エクスポーターではエンドポイントなどの設定なしにMackerel APMに送信できません
* コレクターのログにエクスポートエラーが出ていないか確認してください
* ネットワーク接続（Mackerelのエンドポイントへのアウトバウンド通信）を確認してください

##### 設定ファイルのエラー

* `otelcol-mackerel --config config.yaml validate` コマンドでバリデーションし、エラー箇所を特定してください
* YAMLのインデントやスペルミスに注意してください。特にプロセッサー名やエクスポーター名の綴りを確認しましょう

## 本番運用を見据えた考慮事項

### コレクターの配置パターン

コレクターの配置方法には、大きく2つのパターンがあります。

##### サイドカー型

アプリケーションと同じホストやPodにコレクターを配置するパターンです。アプリケーションとコレクターの通信がlocalhostで完結するため、ネットワーク構成がシンプルです。Mackerel OpenTelemetryコレクターのデフォルト設定（localhost受信）はこのパターンに適しています。

<figure class="figure-image figure-image-fotolife" title="サイドカー型の配置">[f:id:mackerelio:20260909184813p:plain]<figcaption>サイドカー型の配置</figcaption></figure>

##### ゲートウェイ型

独立したホストやサービスとしてコレクターを配置し、複数のアプリケーションからのデータを集約するパターンです。`OTELCOL_MACKEREL_HOST` でバインドアドレスを変更するか、カスタム設定でReceiverのエンドポイントを調整（たとえば`endpoint: 0.0.0.0:4318`）して、外部からの接続を受け付けるようにします。

<figure class="figure-image figure-image-fotolife" title="ゲートウェイ型の配置">[f:id:mackerelio:20260909184826p:plain]<figcaption>ゲートウェイ型の配置</figcaption></figure>

検証段階ではサイドカー型（デフォルト設定）で十分です。本番規模ではアーキテクチャに応じて選択してください。

### 可用性の確保

コレクターが停止するとテレメトリーデータが失われる点には注意が必要です。

* deb/rpmパッケージでインストールした場合、systemdサービスとして自動起動・自動再起動が設定されます
* Docker環境では `--restart=always` オプションの付与を検討してください
* 本番運用では、ヘルスチェックや冗長構成の導入も検討しましょう

### Windows環境での利用

Mackerel OpenTelemetryコレクターはLinuxとDocker（Linuxコンテナ）向けに提供されており、Windows版は用意されていません。Windows環境でコレクターを利用する場合は、OpenTelemetryプロジェクトが公式に配布している**OpenTelemetry Collector Contrib**を使用します。

GitHub上のリリース物配布ページ（[https://github.com/open-telemetry/opentelemetry-collector-releases/releases](https://github.com/open-telemetry/opentelemetry-collector-releases/releases)）にある最新バージョンの「Assets」をクリックし、otelcol-contrib\_X.XXX.X\_windows\_x64.msiの名前のインストーラファイルをダウンロードしてインストールしてください。

公式コレクターを利用する場合、エクスポーターには `mackerel_otlp` の代わりに汎用の `otlp_http` を使い、Mackerel APMのエンドポイントとAPIキーを設定ファイル内で明示的に指定します。

```yaml
receivers:
  otlp:
    protocols:
      grpc:
      http:

exporters:
  otlp_http:
    endpoint: "https://otlp-vaxila.mackerelio.com"
    headers:
      Mackerel-Api-Key: "<YOUR_API_KEY>"
    sending_queue:
      batch:
  otlp:
    endpoint: "otlp.mackerelio.com:4317"
    headers:
      Mackerel-Api-Key: "<YOUR_API_KEY>"
    sending_queue:
      batch:

service:
  pipelines:
    traces:
      receivers: [otlp]
      exporters: [otlp_http]
    metrics:
      receivers: [otlp]
      exporters: [otlp]
```

トレースの送信先は `https://otlp-vaxila.mackerelio.com`（HTTP）、メトリックの送信先は `otlp.mackerelio.com`（gRPC）と、シグナルによってエンドポイントが異なる点に注意してください。Mackerel OpenTelemetryコレクターの `mackerel_otlp` エクスポーターではこうしたエンドポイントの違いやAPIキーの設定を自動的に処理してくれますが、公式コレクターではすべて明示的に設定する必要があります。

## 次のステップへ

ここまでの内容で、コレクターの仕組みとカスタム設定の方法が理解できました。次は、目的に応じてステップを進めてみてください。

* **取得したデータを分析したい** → [短期検証による可視化分析](./verification-analysis)で、トレースやREDメトリックからどんな情報を読み取れるかを解説しています
* **本番運用のコストを見積もりたい** → [コスト・データ量の見極め](./cost-estimation)で、検証データから月間コストを算出する方法を扱っています
* **不要なデータをフィルタリングしてコストを最適化したい** → [フィルタリングとサンプリングによるデータ量・コストの最適化](./filtering-and-sampling)で、フィルタリングとサンプリングの設定方法を解説しています

<nav>
<ul style="display:flex;justify-content:space-between;padding:0;margin:0">
<li style="list-style:none">前の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/zero-code-instrumentation" rel="prev">ゼロコード計装による最短データ取得</a></li>
<li style="list-style:none;margin-left:auto">次の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/verification-analysis" rel="next">短期検証による可視化分析</a></li>
</ul>
</nav>
