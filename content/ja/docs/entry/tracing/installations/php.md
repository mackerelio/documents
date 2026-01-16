---
Title: PHPのアプリケーションからMackerelにトレースを送信する
Date: 2025-03-13T16:38:43+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/installations/php
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333960649
---

このページでは、PHPのアプリケーションからMackerelにトレースを送信する方法を解説します。

[:contents]

## 概要

MackerelはOpenTelemetryの仕組み（計装）を利用してトレースを取得します。OpenTelemetryに対応したトレースはさまざまな方法で取得できますが、今回はゼロコード計装と呼ばれる、アプリケーションの実装を変更せずにトレースを送信する方法を解説します。

<div class="note">
  <p>📝 補足</p>
  <p>PHPのゼロコード計装は、ライブラリ等で計装対象となるメソッドの実行前後に観測用関数を実行することで実現されています。詳しくは<a href="https://opentelemetry.io/ja/docs/zero-code/php/">PHP zero-code instrumentation</a>をご確認ください。</p>
</div>

## 動作要件

OpenTelemetryの[動作要件](https://opentelemetry.io/ja/docs/zero-code/php/#requirements)として、PHPが指定されたバージョン以上である必要があります。

- PHP8.0以上

## 導入方法

アプリケーションからMackerelへトレースを送信するために、以下をおこないます。

1. 拡張ライブラリ（PECL）の追加
2. パッケージのインストール
3. 環境変数の設定

### 1. 拡張ライブラリ（PECL）の追加

PECLを使用してPHPにOpenTelemetry用の拡張ライブラリをインストールします。

```bash
pecl install opentelemetry
```

その後、インストールした拡張を `php.ini` に追加します。

```ini
[opentelemetry]
extension=opentelemetry.so
```

### 2. パッケージのインストール

composerを使用してアプリケーションにOpenTelemetryの導入に必要なパッケージをインストールします。

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

`open-telemetry/opentelemetry-auto-laravel`がLaravel向けのパッケージです。たとえばslim向けには`open-telemetry/opentelemetry-auto-slim`というパッケージが提供されています。

提供されているパッケージは以下のページで検索できます。

[https://opentelemetry.io/ecosystem/registry/?language=php&component=instrumentation:embed:cite]

<div class="note">
  <p>📝 補足</p>
  <p>お使いのフレームワークやアプリケーション向けの計測ライブラリが存在しない場合は、独自に計装することもできます。詳しくはOpenTelemetry公式ドキュメントの <a href="https://opentelemetry.io/ja/docs/zero-code/php/#how-it-works">PHP zero-code instrumentation | How it works</a> をご確認ください。</p>
</div>

### 3. 環境変数の設定

パッケージのインストール後、Mackerelにトレースを送信するための環境変数を設定します。以下はLaravelプロジェクト内の`.env`ファイルを利用して設定する例です。

```
OTEL_PHP_AUTOLOAD_ENABLED=true
OTEL_TRACES_EXPORTER=otlp
OTEL_SERVICE_NAME=my-sample-app
# 標準出力に出力する場合
# OTEL_TRACES_EXPORTER=console
OTEL_EXPORTER_OTLP_PROTOCOL=http/protobuf
OTEL_EXPORTER_OTLP_TRACES_ENDPOINT=https://otlp-vaxila.mackerelio.com/v1/traces
OTEL_EXPORTER_OTLP_TRACES_HEADERS="Accept=*/*,Mackerel-Api-Key=${MACKEREL_APIKEY}"
OTEL_METRICS_EXPORTER=none
OTEL_LOGS_EXPORTER=none
OTEL_PROPAGATORS=baggage,tracecontext
```

- `OTEL_TRACES_EXPORTER`を`console`にするとトレースが標準出力に出力されます。
- `OTEL_SERVICE_NAME`はトレースのサービス名（`service.name`属性の値）になります。
- `OTEL_EXPORTER_OTLP_TRACES_ENDPOINT`はトレースの送信先の指定です。
  - Mackerelに直接送信する場合は`https://otlp-vaxila.mackerelio.com/v1/traces`を指定します。
  - Collectorを利用して送信する場合は`http://<Collectorのアドレス:ポート>/v1/traces`を指定します。
- `${MACKEREL_APIKEY}`はMackerelのAPIキーの指定です。[APIキーの一覧](https://mackerel.io/my?tab=apikeys)から、Write権限のあるAPIキーをアプリケーションが動作するシステム内の環境変数に定義してください。
  - 環境変数ではなくAPIキーを直接記述しても動作します。

環境変数の設定後、アプリケーションを起動するとトレースが送信されます。

## トレースを確認する

送信されたトレースは以下の手順で確認できます。

1. メニューの「[APM](https://mackerel.io/my/apm)」を選択<br>
2. サービス名を選択
  [f:id:mackerelio:20251224180534j:plain]
3. 「トレース」タブを選択
  [f:id:mackerelio:20251224180530j:plain]
4. トレース一覧からトレースを選択すると詳細が確認できます
  [f:id:mackerelio:20251224180525p:plain]

以上、PHPで作成されたアプリケーションにゼロコード計装をおこなって、Mackerelへトレースを投稿する方法のご紹介でした。
