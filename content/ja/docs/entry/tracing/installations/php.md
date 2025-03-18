---
Title: トレース - PHPにOpenTelemetryを導入する
Date: 2025-03-13T16:38:43+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/installations/php
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333960649
---

MackerelはOpenTelemetryの仕組み (計装)を利用してデータを取得しています。

このページではPHPのデータをMackerelに送信する方法を解説します。

[:contents]

## PHP向けOpenTelemetry

OpenTelemetryにはPHP用のSDKが用意されています。

このSDKに加えて、LaravelやPDO用のSDKを使用すると、既存の実装を変更せずに計装することができます。

[https://opentelemetry.io/ja/docs/languages/php/instrumentation/:embed:cite]

### Collectorを使用するべきか

データをMackerelに送信する際に、SDKから直接送信するだけではなく、Collectorを使うこともできます。

しかし、PHPの多くのランタイムは全ての処理が終わるまでリクエストをブロッキングするため、直接Mackerelにデータを送信すると、Mackerelに送信している時間の分だけリクエストも遅くなってしまいます。

そのため、Collectorを使用して、ユーザーへの返答をできるだけ速くすることを推奨しています。

ただし、`fastcgi` の `fastcgi_finish_request()` を使用すると、Mackerelへの送信を待つことなくユーザーにレスポンスを返すことができます。その場合は、直接Mackerelにデータを送信しても問題になることはないでしょう。

Collectorを使用する方法は、以下のページを参照してください。

[https://mackerel.io/ja/docs/entry/tracing/installations/opentelemetry-collector:embed:cite]

## 導入方法

PHPには複数のWebフレームワークが存在しますが、このページでは `Laravel` への導入方法を説明します。

他のフレームワークを使っている場合もほぼ同じ方法で計装することができます。

以下のステップでMackerelトレーシング機能を導入できます。

1. 拡張ライブラリ (PECL) の追加
2. パッケージの追加
3. 環境変数の設定
4. 独自の計装 (任意)

### 1. 拡張ライブラリ (PECL) の追加

PECL を使用して OpenTelemetry 用の拡張ライブラリをインストールします。

```bash
pecl install opentelemetry-beta
```

その後、インストールした拡張を `php.ini` に追加します。

```ini
[opentelemetry]
extension=opentelemetry.so
```

### 2. パッケージの追加

Laravel や PDO用のパッケージを追加します。

```bash
composer require open-telemetry/api \
  open-telemetry/exporter-otlp \
  open-telemetry/opentelemetry-auto-http-async \
  open-telemetry/opentelemetry-auto-io \
  open-telemetry/opentelemetry-auto-laravel \
  open-telemetry/opentelemetry-auto-pdo \
  open-telemetry/opentelemetry-auto-psr3 \
  open-telemetry/opentelemetry-auto-psr15 \
  open-telemetry/opentelemetry-auto-psr18 \
  open-telemetry/sdk
```

#### ⚠️ 他のフレームワークを使用している場合

このページではLaravelを例にするため `opentelemetry-auto-laravel` を使用しています。SlimやSymfonyなど別のフレームワークを使用している場合、それぞれにあったパッケージを、以下のページから検索することができます。

[https://opentelemetry.io/ecosystem/registry/?language=php&component=instrumentation:embed:cite]

### 3. 環境変数の設定

パッケージのインストール後、以下の環境変数を設定するとデータがCollectorへ送信されるようになります。

```
OTEL_PHP_AUTOLOAD_ENABLED: true
OTEL_SERVICE_NAME: "acme_service"
OTEL_TRACES_EXPORTER: otlp
# デバッグ時にはconsoleと設定すると便利
# OTEL_TRACES_EXPORTER: console
OTEL_EXPORTER_OTLP_PROTOCOL: http/protobuf
OTEL_EXPORTER_OTLP_TRACES_ENDPOINT: 'http://localhost:4318/v1/traces'
OTEL_METRICS_EXPORTER: none
OTEL_LOGS_EXPORTER: none
OTEL_PROPAGATORS: baggage,tracecontext
```

これらの環境変数を、オートロードが開始する前に設定してください。

** `OTEL_SERVICE_NAME` と `OTEL_EXPORTER_OTLP_TRACES_ENDPOINT` の値は各環境にあわせた値へ変更してください。 また、`OTEL_TRACES_EXPORTER` を `console` に設定すると、データがログに出力されるようになるので、送信が動かない時のデバッグに便利です。 **

以上で、インストールが完了し、Collectorを通して、Mackerelにデータが送られるようになりました。

### 4. 独自の計装 (任意)

独自のSpanを追加することで、任意の範囲を計装することができます。

計装によって、変数の値や処理時間を記録することができるようになります。

具体的には、下のように `startSpan` を使用すると計装が追加できます。

```php
use OpenTelemetry\API\Globals;

function awesomeFunction(string $arg) {
  $tracer = Globals::tracerProvider()->getTracer("...");
  $childSpan = $tracer->spanBuilder('Awesome span name')->startSpan();
  $childSpan->setAttribute('arg', $arg);
  $scope = $childSpan->activate();

  try {
    // 計装対象の処理
  } finally {
    $childSpan->end();
    $scope->detach();
  }
}
```

計装の方法は他にも用意されています。詳細はOpenTelemetryのドキュメントを参照してください。

[https://opentelemetry.io/ja/docs/languages/php/instrumentation/:embed:cite]
