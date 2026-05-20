---
Title: .NETのアプリケーションからMackerelにトレースを送信する
Date: 2025-03-18T17:13:48+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/installations/dotnet
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398337498206
---

このページでは、.NETのアプリケーションからMackerelにトレースを送信する方法を解説します。

[:contents]

## 概要

MackerelはOpenTelemetryの仕組み（計装）を利用してトレースを取得します。OpenTelemetryに対応したトレースはさまざまな方法で取得できますが、今回はゼロコード計装と呼ばれる、アプリケーションの実装を変更せずにトレースを送信する方法を解説します。

<div class="note">
  <p>📝 補足</p>
  <p>.NETのゼロコード計装は、プログラムが実行される直前（JITコンパイル時）に、CLRの機能を使ってメソッドのIL（中間言語）を書き換えることで実現されています。.NETのゼロコード計装について詳しく知りたい場合は、<a href="https://opentelemetry.io/ja/docs/zero-code/dotnet/">.NET zero-code instrumentation
</a>をご確認ください。</p>
</div>

## 動作要件

.NETのゼロコード計装は以下の[動作要件](https://opentelemetry.io/ja/docs/zero-code/dotnet/#compatibility)を満たす必要があります。

- .NET 8以上
- .NET Framework 4.6.2以上
- プロセッサー
  - x86
  - AMD64（x86-64）
  - ARM64（[Experimental](https://opentelemetry.io/docs/specs/otel/versioning-and-stability/)）

## 導入方法

アプリケーションからMackerelへトレースを送信するために、以下をおこないます。Windowsの場合はPowerShellでコマンドを実行します。実行には管理者権限が必要です。

1. インストールスクリプトのダウンロード
2. 計装用ツールのインストール
3. 計装用ツールの実行

### 1. インストールスクリプトのダウンロード

以下のコマンドで、OpenTelemetryの計装に必要なツールのインストールスクリプトをダウンロードします。

**Linuxの場合**

```
curl -L -O https://github.com/open-telemetry/opentelemetry-dotnet-instrumentation/releases/latest/download/otel-dotnet-auto-install.sh
```

**Windowsの場合**

```
$module_url = "https://github.com/open-telemetry/opentelemetry-dotnet-instrumentation/releases/latest/download/OpenTelemetry.DotNet.Auto.psm1"
$download_path = Join-Path $env:temp "OpenTelemetry.DotNet.Auto.psm1"
Invoke-WebRequest -Uri $module_url -OutFile $download_path -UseBasicParsing
```

### 2. 計装用ツールのインストール

以下のコマンドで、OpenTelemetryの計装に必要なツールをインストールします。

**Linuxの場合**

```
./otel-dotnet-auto-install.sh
```

**Windowsの場合**

```
Import-Module $download_path
Install-OpenTelemetryCore
```

### 3. 計装用ツールの実行

Mackerelへトレースを送信するために計装用ツールを実行します。以下のように環境変数の設定とともに計装用ツールを実行し、その後、計装用ツールを実行したセッションでアプリケーションを起動すると、`my-sample-app`というサービス名でMackerelにトレースが送信されます。

**Linuxの場合**

```
export OTEL_TRACES_EXPORTER=otlp \
  OTEL_METRICS_EXPORTER=none \
  OTEL_LOGS_EXPORTER=none \
  OTEL_EXPORTER_OTLP_PROTOCOL=http/protobuf \
  OTEL_EXPORTER_OTLP_TRACES_ENDPOINT=https://otlp-vaxila.mackerelio.com/v1/traces \
  OTEL_EXPORTER_OTLP_TRACES_HEADERS="Accept=*/*,Mackerel-Api-Key=${MACKEREL_APIKEY}" \
  OTEL_SERVICE_NAME=my-sample-app \
  $HOME/.otel-dotnet-auto/instrument.sh
```

**Windowsの場合**

```
$env:OTEL_TRACES_EXPORTER="otlp"
$env:OTEL_METRICS_EXPORTER="none"
$env:OTEL_LOGS_EXPORTER="none"
$env:OTEL_EXPORTER_OTLP_PROTOCOL="http/protobuf"
$env:OTEL_EXPORTER_OTLP_TRACES_ENDPOINT="https://otlp-vaxila.mackerelio.com/v1/traces"
$env:OTEL_EXPORTER_OTLP_TRACES_HEADERS="Accept=*/*,Mackerel-Api-Key=$env:MACKEREL_APIKEY"
Register-OpenTelemetryForCurrentSession -OTelServiceName "my-sample-app"
```

**環境変数の詳細**

- `OTEL_TRACES_EXPORTER`を`console`にするとトレースが標準出力に出力されます。
- トレースの送信に関係のないメトリックとログは無効にします。
  - `$env:OTEL_METRICS_EXPORTER="none"`
  - `$env:OTEL_LOGS_EXPORTER="none"`
- `OTEL_EXPORTER_OTLP_TRACES_ENDPOINT`はトレースの送信先の指定です。
  - Mackerelに直接送信する場合は`https://otlp-vaxila.mackerelio.com/v1/traces`を指定します。
  - Collectorを利用して送信する場合は`http://<Collectorのアドレス:ポート>/v1/traces`を指定します。
- `Mackerel-Api-Key`はMackerelのAPIキーの指定です。[APIキーの一覧](https://mackerel.io/my?tab=apikeys)から、Write権限のあるAPIキーを取得し、アプリケーションが動作するシステム内の環境変数に定義してください。
  - Linuxの場合：`${MACKEREL_APIKEY}`
  - Windowsの場合：`$env:MACKEREL_APIKEY`
  - 環境変数ではなくAPIキーを直接記述しても動作します。
- `my-sample-app`はトレースのサービス名（`service.name`属性の値）になります。任意の名前を指定してください。

## トレースを確認する

送信されたトレースは以下の手順で確認できます。

1. メニューの「[APM](https://mackerel.io/my/apm)」を選択<br>
2. サービス名を選択
  [f:id:mackerelio:20251224180534j:plain]
3. 「トレース」タブを選択
  [f:id:mackerelio:20251224180530j:plain]
4. トレース一覧からトレースを選択すると詳細が確認できます
  [f:id:mackerelio:20251224180525p:plain]

以上、.NETで作成されたアプリケーションにゼロコード計装をおこなって、Mackerelへトレースを投稿する方法のご紹介でした。
