---
Title: GoのアプリケーションからMackerelにトレースを送信する
Date: 2025-03-13T16:34:45+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/installations/go
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333960398
---

このページでは、GoのアプリケーションからトレースをMackerelに送信する方法を解説します。

[:contents]

## 概要

MackerelはOpenTelemetryの仕組み（計装）を利用してトレースを取得します。OpenTelemetryに対応したトレースはさまざまな方法で取得できますが、今回はゼロコード計装と呼ばれる、アプリケーションの実装を変更せずにトレースを送信する方法を解説します。

<div class="note">
<p>📝 補足</p>
<p>Goのゼロコード計装は、起動中のアプリケーションとは別にeBPFを利用する自動計装エージェントを起動し、トレースといったテレメトリーデータを取得できるようにすることで実現されています。</p>
<p>詳しくは <a href="https://github.com/open-telemetry/opentelemetry-go-instrumentation/blob/main/docs/how-it-works.md">opentelemetry-go-instrumentation/docs/how-it-works.md</a> をご確認ください。</p>
</div>

## 動作要件

OpenTelemetryの[動作要件](https://github.com/open-telemetry/opentelemetry-go-instrumentation/#compatibility)として、動作環境のLinux Kernelが指定されたバージョン以上である必要があります。

- Linux Kernel 4.4以上

## 導入方法

アプリケーションからMackerelへトレースを送信するために、以下をおこないます。

1. 自動計装エージェントの用意
2. アプリケーションを起動
3. 自動計装エージェントを起動

### 1. 自動計装エージェントの用意

[open-telemetry/opentelemetry-go-instrumentation: OpenTelemetry Auto Instrumentation using eBPF](https://github.com/open-telemetry/opentelemetry-go-instrumentation)リポジトリをクローンし、自動計装エージェントをビルドします。

```bash
git clone https://github.com/open-telemetry/opentelemetry-go-instrumentation.git
cd opentelemetry-go-instrumentation
make build
```

### 2. アプリケーションを起動

計装をおこないたいアプリケーションを通常通り起動してください。

### 3. 自動計装エージェントを起動

「1.」でビルドして用意した自動計装エージェントを、設定用の環境変数を指定のうえroot権限で起動します。

以下は、対象アプリケーションが `/usr/local/bin/app` に配置されている場合に、`my-sample-app` というサービス名でMackerelにトレースを送信する例です。

```sh
sudo OTEL_GO_AUTO_TARGET_EXE=/usr/local/bin/app \
     OTEL_SERVICE_NAME=my-sample-app \
     OTEL_EXPORTER_OTLP_TRACES_ENDPOINT=https://otlp-vaxila.mackerelio.com/v1/traces \
     OTEL_EXPORTER_OTLP_TRACES_HEADERS="Accept=*/*,Mackerel-Api-Key=${MACKEREL_APIKEY}" \
     ./otel-go-instrumentation
```

- `OTEL_GO_AUTO_TARGET_EXE`は計装対象とするアプリケーションのバイナリが配置されているパスを指定します。
- `OTEL_SERVICE_NAME`はトレースのサービス名（`service.name`属性の値）になります。
- `OTEL_EXPORTER_OTLP_TRACES_ENDPOINT`はトレースの送信先の指定です。
  - Mackerelに直接送信する場合は`https://otlp-vaxila.mackerelio.com/v1/traces`を指定します。
  - Collectorを利用して送信する場合は`http://<Collectorのアドレス:ポート>/v1/traces`を指定します。
- `${MACKEREL_APIKEY}`はMackerelのAPIキーの指定です。[APIキーの一覧](https://mackerel.io/my?tab=apikeys)から、Write権限のあるAPIキーを自動計装エージェントが動作するシステム内の環境変数に定義してください。
  - 環境変数ではなくAPIキーを直接記述しても動作します。
- `OTEL_TRACES_EXPORTER=console`を追加付与することで、トレースが標準出力に出力されます。

## トレースを確認する

送信されたトレースは以下の手順で確認できます。

1. メニューの「[APM](https://mackerel.io/my/apm)」を選択<br>
2. サービス名を選択
  [f:id:mackerelio:20251224180534j:plain]
3. 「トレース」タブを選択
  [f:id:mackerelio:20251224180530j:plain]
4. トレース一覧からトレースを選択すると詳細が確認できます
  [f:id:mackerelio:20251224180525p:plain]

以上、Goで作成されたアプリケーションにゼロコード計装をおこなって、Mackerelへトレースを投稿する方法のご紹介でした。
