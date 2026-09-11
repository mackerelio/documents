---
Title: Mackerel APM導入ガイド ― .NET におけるゼロコード計装
Date: 2026-09-11T11:02:23+09:00
URL: https://mackerel.io/ja/docs/entry/apm/getting-started/dotnet-zero-code-instrumentation
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/14945776032076293613
---

.NETでは、CLRプロファイラーを利用したゼロコード計装が可能です。インストールスクリプトを実行し、環境変数を設定するだけで、アプリケーションコードを変更せずにトレースの収集を始められます。ASP.NET Core、HttpClient、SqlClient、Entity Frameworkなど主要なライブラリが自動的に計装されます。

なお、.NETエコシステムでは、.NET Aspireのようにプロジェクトの生成時点でOpenTelemetryが組み込まれているフレームワークや、Azure Monitor OpenTelemetry Distroのようにプラットフォーム側で計装を提供する仕組みも広がっています。Microsoftはこうしたフレームワーク統合型のアプローチを推進しており、新規プロジェクトであればそちらのほうが手軽です。既存のアプリケーションにコード変更なしで計装を追加したい場合には、以下のゼロコード計装が引き続き有効です。

[:contents]

## 前提条件

* .NET 6.0以降
* Linux、Windows、またはmacOS環境
* Windows環境では管理者権限のPowerShell（Windows PowerShell 5.1）が必要
* Mackerel OpenTelemetryコレクターがセットアップ済みであること（[ゼロコード計装による最短データ取得](./zero-code-instrumentation)の「Mackerel OpenTelemetryコレクターの準備」を参照）

## セットアップ手順

### Linux / macOSの場合

##### 1. インストールスクリプトの実行

```bash
curl -sSfL https://github.com/open-telemetry/opentelemetry-dotnet-instrumentation/releases/latest/download/otel-dotnet-auto-install.sh -O
sh ./otel-dotnet-auto-install.sh
```

インストールスクリプトにより、`$HOME/.otel-dotnet-auto/` にCLRプロファイラーと計装ライブラリが配置されます。Linux/macOSでは管理者権限は不要で、設定は現在のシェルセッションにのみ影響します。

##### 2. 環境変数の設定と起動

インストールスクリプトが生成する `instrument.sh` を読み込んでから、アプリケーションを起動します。

```bash
. $HOME/.otel-dotnet-auto/instrument.sh
export OTEL_SERVICE_NAME="my-dotnet-app"
export OTEL_EXPORTER_OTLP_ENDPOINT="http://localhost:4318"
./MyApp
```

`instrument.sh` は、CLRプロファイラーの有効化に必要な環境変数（`CORECLR_ENABLE_PROFILING`、`CORECLR_PROFILER` など）をまとめて設定します。

### Windowsの場合

##### 1. インストールスクリプトの実行（PowerShell、管理者権限）

PowerShellを管理者として実行し、以下のコマンドを実行します。

```powershell
$module_url = "https://github.com/open-telemetry/opentelemetry-dotnet-instrumentation/releases/latest/download/OpenTelemetry.DotNet.Auto.psm1"
$download_path = Join-Path $env:temp "OpenTelemetry.DotNet.Auto.psm1"
Invoke-WebRequest -Uri $module_url -OutFile $download_path
Import-Module $download_path
Install-OpenTelemetryCore
```

##### 2. 環境変数の設定と起動

```powershell
Register-OpenTelemetryForCurrentSession -OTelServiceName "my-dotnet-app"
$env:OTEL_EXPORTER_OTLP_ENDPOINT = "http://localhost:4318"
dotnet run
```

### 動作確認

アプリケーションにリクエストを送信し、Mackerel APMの画面でトレースが表示されることを確認します。確認方法は[ゼロコード計装による最短データ取得](./zero-code-instrumentation)の「最初のトレースを確認してみよう」を参照してください。

## 自動計装の対象

.NETのゼロコード計装は、以下のライブラリを自動的に計装します。

* **Webフレームワーク**：ASP.NET Core
* **HTTPクライアント**：HttpClient、WebClient
* **データアクセス**：SqlClient、Entity Framework Core
* **gRPC**：Grpc.Net.Client
* **メッセージング**：Azure SDK、RabbitMQ

特定のライブラリの計装を無効にしたい場合は、環境変数 `OTEL_DOTNET_AUTO_TRACES_{NAME}_INSTRUMENTATION_ENABLED=false` を設定します。`{NAME}` にはライブラリ名を大文字で指定します（例：`OTEL_DOTNET_AUTO_TRACES_ASPNETCORE_INSTRUMENTATION_ENABLED=false`）。不要な計装を無効にすることでスパン数の削減とパフォーマンスの向上が期待できます。

## よくある問題と対処法

### トレースが表示されない

* `instrument.sh`（Linux/macOS）または `Register-OpenTelemetryForCurrentSession`（Windows）を実行してからアプリケーションを起動しているか確認してください。これらを実行しないと、CLRプロファイラーが有効にならず計装が動作しません
* `OTEL_EXPORTER_OTLP_ENDPOINT` がコレクターのアドレスを正しく指しているか確認してください

### self-contained デプロイメントの場合

self-contained（自己完結型）でデプロイした.NETアプリケーションの場合、ランタイムのバージョンとCLRプロファイラーのバージョンに互換性があるか確認してください。

## 次のステップ

* **取得したデータを分析したい** → [短期検証による可視化分析](./verification-analysis)で、トレースやREDメトリックから何を読み取れるかを解説しています
* **本番運用のコストを見積もりたい** → [コスト・データ量の見極め](./cost-estimation)で、検証データから月間コストを算出する方法を扱っています

<nav>
<ul style="display:flex;justify-content:space-between;padding:0;margin:0">
<li style="list-style:none">前の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/python-zero-code-instrumentation" rel="prev">Python におけるゼロコード計装</a></li>
<li style="list-style:none;margin-left:auto">次の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/rails-instrumentation" rel="next">Ruby on Rails における OpenTelemetry 計装</a></li>
</ul>
</nav>
