---
Title: JavaのアプリケーションからMackerelにトレースを送信する
Date: 2025-03-13T16:38:43+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/installations/java
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17179246901371634721
---

このページでは、JavaのアプリケーションからMackerelにトレースを送信する方法を解説します。

[:contents]

## 概要

MackerelはOpenTelemetryの仕組み（計装）を利用してトレースを取得します。OpenTelemetryに対応したトレースはさまざまな方法で取得できますが、今回はゼロコード計装と呼ばれる、アプリケーションの実装を変更せずにトレースを送信する方法を解説します。

<div class="note">
  <p>📝 補足</p>
  <p>Javaのゼロコード計装は、テレメトリーデータを取得するためのコードを自動挿入するエージェントを使用して、アプリケーションを実行することにより実現されています。詳しくは<a href="https://opentelemetry.io/ja/docs/zero-code/java/agent/">Javaエージェント</a>をご確認ください。</p>
</div>

## 動作要件

Javaのゼロコード計装にはOpenTelemetry Javaエージェント（以降、Javaエージェント）を使用します。

### Javaエージェントの動作要件

- Java 8以降

<div class="note">
  <p>📝 補足</p>
  <p>互換性要件の詳細については、<a href="https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/main/VERSIONING.md">OpenTelemetry Java Instrumentation Versioning</a>をご確認ください。</p>
</div>

### サポートされているライブラリ

Javaエージェントがサポートするライブラリについては、以下のページを参照してください。

- [Supported Libraries](https://opentelemetry.io/ja/docs/zero-code/java/agent/supported-libraries/)

## 導入方法

アプリケーションからMackerelへトレースを送信するために、以下をおこないます。

1. Javaエージェントのダウンロード
2. Javaエージェントを指定してアプリケーションを起動

### 1. Javaエージェントのダウンロード

以下のコマンドでJavaエージェント（opentelemetry-javaagent.jar）の最新版をダウンロードし、アプリケーションから参照できる場所に配置します。

```bash
curl -L -O https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/latest/download/opentelemetry-javaagent.jar
```

[opentelemetry-java-instrumentationのリリースページ](https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases)からもダウンロード可能です。


### 2. Javaエージェントを指定してアプリケーションを起動

起動オプションに `-javaagent` でJavaエージェント（opentelemetry-javaagent.jar）を指定し、その他の設定を追加してアプリケーションを起動します。

環境変数 `MACKEREL_APIKEY` にMackerelのWrite権限を持つAPIキーを設定した状態で以下のように起動することで、`my-sample-app` というサービス名でMackerelにトレースが送信されます。

```bash
java -javaagent:opentelemetry-javaagent.jar \
  -Dotel.traces.exporter=otlp \
  -Dotel.service.name=my-sample-app \
  -Dotel.exporter.otlp.protocol=http/protobuf \
  -Dotel.exporter.otlp.traces.endpoint=https://otlp-vaxila.mackerelio.com/v1/traces \
  -Dotel.exporter.otlp.traces.headers="Accept=*/*,Mackerel-Api-Key=${MACKEREL_APIKEY}" \
  -Dotel.metrics.exporter=none \
  -Dotel.logs.exporter=none \
  -jar build/libs/demo-0.0.1-SNAPSHOT.jar # この行は起動したいアプリケーションに置き換えてください
```

- `-Dotel.traces.exporter` を `console` にするとトレースが標準出力に出力されます。カンマ区切りで同時指定も可能です。
- `-Dotel.service.name` はトレースのサービス名（`service.name` 属性の値）になります。
- `-Dotel.exporter.otlp.traces.endpoint` はトレースの送信先の指定です。
  - Mackerelに直接送信する場合は `https://otlp-vaxila.mackerelio.com/v1/traces` を指定します。
  - Collectorを利用して送信する場合は `http://<Collectorのアドレス:ポート>/v1/traces` を指定します。
- `${MACKEREL_APIKEY}` はMackerelのAPIキーの指定です。[APIキーの一覧](https://mackerel.io/my?tab=apikeys)から、Write権限のあるAPIキーをアプリケーションが動作するシステム内の環境変数に定義してください。
  - 環境変数ではなくAPIキーを直接記述しても動作します。
- トレースの送信に関係のないメトリックとログは無効にします。
  - `-Dotel.metrics.exporter=none`
  - `-Dotel.logs.exporter=none`

## トレースを確認する

送信されたトレースは以下の手順で確認できます。

1. メニューの「[APM](https://mackerel.io/my/apm)」を選択<br>
2. サービス名を選択
  [f:id:mackerelio:20251224180534j:plain]
3. 「トレース」タブを選択
  [f:id:mackerelio:20251224180530j:plain]
4. トレース一覧からトレースを選択すると詳細が確認できます
  [f:id:mackerelio:20251224180525p:plain]

以上、Javaで作成されたアプリケーションにゼロコード計装をおこなって、Mackerelへトレースを投稿する方法のご紹介でした。
