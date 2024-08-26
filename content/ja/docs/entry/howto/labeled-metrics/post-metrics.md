---
Title: ラベル付きメトリックを Mackerel に投稿する
Date: 2024-02-02T16:07:16+09:00
URL: https://mackerel.io/ja/docs/entry/howto/labeled-metrics/post-metrics
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6801883189090642476
---

ラベル付きメトリックは [OpenTelemetry](https://opentelemetry.io/) の仕様に準拠した機能です。OpenTelemetry のエコシステムを利用してメトリックを Mackerel に投稿することができます。

OpenTelemetry は特定のベンダーに依存しない API を提供しているため、すでに OpenTelemetry ライブラリがアプリケーションやサービスに組み込まれている場合には、そのままの構成で監視バックエンドを Mackerel に切り替えることができます。

Mackerel では OpenTelemetry が掲げる 3 つのテレメトリーデータ（メトリック・ログ・トレース）のうち、現時点ではメトリックのみをサポートしています。

## Mackerel でサポートされているメトリックの種類

Mackerel では以下の種類のメトリックをサポートしています。

- カウンター
- ゲージ
- ヒストグラム（部分的にサポート）

### ヒストグラムについて

Mackerel ではヒストグラムのすべてのバケットを保存せずに、以下の代表値のみを保存しています。

- `count`：ヒストグラムに含まれるサンプルの数
- `sum`：合計値
- `min`：最小値
- `max`：最大値
- `99`：99 パーセンタイル値
- `95`：95 パーセンタイル値
- `90`：90 パーセンタイル値

各代表値は `.99` といったサフィックスをメトリック名に付与することで取得できます。例として `http.server.request.duration` というヒストグラムメトリックの場合、以下のメトリックが取得できます。

- `http.server.request.duration.99`
- `http.server.request.duration.95`
- `http.server.request.duration.90`
- `http.server.request.duration.max`
- `http.server.request.duration.min`
- `http.server.request.duration.sum`
- `http.server.request.duration.count`

なお、代表値を計算した結果値が以下のいずれかに該当する場合、そのメトリックは保存されません。

- `null`
- `inf`
- `-inf`
- `NaN`

例として `http.server.request.duration` の 99 パーセンタイル値が `inf` となる場合、`http.server.request.duration.99` というメトリックは存在しないものとして扱われます。

また、計装の設定によっては以下の代表値のメトリックが取得できない場合があります。

- `max`
- `min`

代表値のみを保存しているため、PromQL の `histogram_` 系の関数は利用できません。例えば `histogram_quantile` 関数を利用して任意の分位数を計算することはできません。

OpenTelemetry の仕様には Explicit Histogram と Exponential Histogram の 2 つの仕様がありますが、どちらの仕様で投稿されたとしても Mackerel 上での見え方に違いはありません。

## メトリックを計装する

まずはアプリケーションやサービスに OpenTelemetry ライブラリを組み込み、メトリックを計装してください。OpenTelemetry には多くの言語向けの SDK が提供されています。詳しくは [OpenTelemetry Language APIs & SDKs](https://opentelemetry.io/docs/languages/) をご覧ください。

言語によってはメトリックの自動計装がサポートされている場合があります。その場合は、アプリケーションのコードを大きく変更することなくメトリックを計装を開始できます。

SDK を利用する他に、[OpenTelemetry Collector](https://opentelemetry.io/docs/collector/) の [receiver](https://opentelemetry.io/docs/collector/configuration/#receivers) を利用してメトリックを収集することもできます。receiver を利用することでホストのメトリックを能動的に収集したり、SDK により送信されたメトリックを受信したりできます。

## ラベル付きメトリックを投稿する

OpenTelemetry で計装したメトリックを Mackerel に投稿するには、[OpenTelemetry Protocol（OTLP）](https://opentelemetry.io/docs/specs/otlp/) を利用して Mackerel にメトリックを送信する必要があります。エンドポイントは以下の通りです。

```
otlp.mackerelio.com:4317
```

また API キーを `Mackerel-Api-Key` ヘッダーに含めて送信する必要があります。API キーは Mackerel の [ダッシュボードの API キータブ](https://mackerel.io/my?tab=apikeys) から取得できます。必ず Write 権限を持つ API キーを利用してください。

## OpenTelemetry コレクターの構成例

以下は、OpenTelemetry コレクターの構成例です。環境変数 `MACKEREL_APIKEY` に API キーを設定してください。

```yaml
receivers:
  hostmetrics:
    scrapers:
      cpu:
      disk:
      filesystem:
      load:
      memory:
      network:
      paging:

processors:
  batch:
    timeout: 1m

# Mackerel にメトリックを送信するエクスポーター
exporters:
  otlp/mackerel:
    endpoint: otlp.mackerelio.com:4317
    headers:
      Mackerel-Api-Key: ${env:MACKEREL_APIKEY}

service:
  pipelines:
    metrics:
      receivers: [hostmetrics]
      processors: [batch]
      exporters: [otlp/mackerel]
```

上記の例では従来のホストメトリックに相当するメトリックを収集し、Mackerel に送信しています。より詳細な説明は以下のブログをご覧ください。

https://mackerel.io/ja/blog/entry/using-open-telemetry-labeled-metrics-with-mackerel
