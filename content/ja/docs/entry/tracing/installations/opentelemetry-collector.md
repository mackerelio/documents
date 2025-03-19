---
Title: トレース - OpenTelemetry Collector を導入する
Date: 2025-03-13T16:39:06+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/installations/opentelemetry-collector
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333960610
---

Mackerelトレーシング機能はOpenTelemetryの仕組み (計装)を利用してデータを送信します。

このページでは OpenTelemetry Collectorを使用してデータを送信する方法を解説します。

[:contents]

## OpenTelemetry Collectorとは

OpenTelemetry Collector は特定ベンダーに依存しない、OpenTelemetry 用エージェントです。

Dockerを使用してサイドカーにすることも、APKやDEBを通してインストールすることもできます。

このページではOpenTelemetry Collectorのインストール方法について説明します。

細かい実装については公式のドキュメントを参照してください。

[https://opentelemetry.io/ja/docs/collector/:embed:cite]

### Collectorを使用するべきか

Collector を使わずに、SDK から直接データを送信することもできます。

どちらにするかどうかを決める際は以下のページを参考にしてください。

[Collectorを使うかどうかの判断について](https://mackerel.io/ja/docs/entry/tracing/guide/what-is-opentelemetry#using-collector-or-not)

## 導入方法

OpenTelemetry Collectorを使用するためにはインストールと設定が必要です。

### インストール

OpenTelemetry Collectorは

* Docker
* Kubernetes
* Linux パッケージ

などで配布されています。

例えば、apkを使用する場合以下のコマンドでインストールできます。

```bash
wget https://github.com/open-telemetry/opentelemetry-collector-releases/releases/download/${VERSION}/otelcol_${VERSION}_linux_amd64.deb
sudo dpkg -i otelcol_${VERSION}_linux_amd64.deb
```

各プラットフォームに対応したコマンドは以下にまとめられています

[https://opentelemetry.io/ja/docs/collector/quick-start/:embed:cite]

### 設定

OpenTelemetry Collector はファイルと環境変数で設定することができます。

例えば、systemd を使用している場合は `/etc/otelcol/config.yaml` を以下のように設定すると、Mackerelに送信することができます。

```yaml
receivers:
  otlp:
    protocols:
      grpc:
      http:

processors:
  memory_limiter:
    check_interval: 1s
    limit_mib: 500
    spike_limit_mib: 100
  batch:
    # Mackerel では 6MB 以上のリクエストを受け付けません。
    # そのため、リクエストあたりの最大スパン数を適当に設定します。
    # スパンはトレースにおける作業または操作の単位です。
    # データベースへのクエリ実行やアプリケーションの処理の 1 部分などをスパンとして表現できます。
    # https://opentelemetry.io/docs/concepts/signals/traces/#spans
    # Mackerel には 1 オーガニゼーションあたり月間 500 万スパンまで送信できます
    send_batch_size: 5000
    send_batch_max_size: 5000

exporters:
  otlphttp/mackerel:
    endpoint: "https://otlp-vaxila.mackerelio.com"
    headers:
      Accept: "*/*"
      "Mackerel-Api-Key": ${env:MACKEREL_APIKEY}

extensions:
  health_check:

service:
  extensions: [health_check]
  pipelines:
    traces:
      receivers: [otlp]
      processors: [batch]
      exporters: [otlphttp/mackerel]
```

この例では、次の項目を設定しています。

* exporters
  * exportersに `otlphttp` を使用します。
  * endpointをMackerelのエンドポイント (**`https://otlp-vaxila.mackerelio.com`**) にします。
  * headersには **`Mackerel-Api-Key`** と **`Accept`** を設定します。
    * **Mackerel-Api-Key:** Mackerel で発行した API キーを設定します。*1
    * **Accept**: 常に `*/*` を設定します。 (Mackerelが内部でAWS Lambdaを使用しているために必要な設定です。)
* service
  * pipelines の traces に作成したexporterを設定します。

これらを設定することで、CollectorからMackerelへデータを送信するようになります。

*1 ：テレメトリデータを送信するための API キーは Mackerel のダッシュボードの API キータブで確認できます。 "Write" 権限が付与されている API キーを環境変数 `MACKEREL_APIKEY` にセットしてください。API キーの権限を変更した際は反映まで1分ほどお待ちください。

<figure class="figure-image figure-image-fotolife" title="API キーを確認する方法">[f:id:mackerelio:20250307170201p:plain]<figcaption>API キーを確認する方法</figcaption></figure>
