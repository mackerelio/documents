---
Title: トレース - .NETにOpenTelemetryを導入する
URL: https://mackerel.io/ja/docs/entry/tracing/installations/dotnet
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398337498206
---

MackerelはOpenTelemetryの仕組み (計装)を利用してデータを取得しています。

このページでは.NET のデータをMackerelに送信する方法を解説します。

[:contents]

## .NET向けOpenTelemetry

OpenTelemetry には .NET用のSDKが用意されています。

このSDKに加えて、ASP.NETやEntityFramework用のSDKを使用すると、色々な範囲を計装できます。

[https://opentelemetry.io/ja/docs/languages/dotnet/instrumentation/:embed:cite]

### Collectorを使用するべきか

データをMackerelに送信する際に、SDKから直接送信するだけではなく、Collectorを使うこともできます。

Collectorを使うかどうかを決める際は以下のページを参考にしてください。

[Collectorを使うかどうかの判断について](https://mackerel.io/ja/docs/entry/tracing/guide/what-is-opentelemetry#using-collector-or-not)

## 導入方法

.NETにはいくつかのWebフレームワークが存在しますが、このページでは `ASP.NET` への導入方法を説明します。 他のフレームワークを使っている場合もほぼ同じ方法で計装することができます。

以下のステップでMackerelトレーシング機能を導入できます。

1. NuGetパッケージの追加
2. 初期設定
3. 独自の計装の追加 (任意)

### 1. NuGetパッケージの追加

以下のパッケージを使用します。

```console
dotnet add package OpenTelemetry.Instrumentation.AspNetCore
dotnet add package OpenTelemetry.Exporter.OpenTelemetryProtocol
dotnet add package OpenTelemetry.Extensions.Hosting
```

このコマンドでは基本的なパッケージのみインストールしています。

後述するように、HTTPクライアントの計装やコンソールへの出力などをしたい場合は、以下のパッケージも追加します。

```console
dotnet add package OpenTelemetry.Instrumentation.Http
dotnet add package OpenTelemetry.Exporter.Console
```

OpenTelemetryには他にもEntityFrameworkやAzureなど色々な用途に対応したパッケージがあり、アプリケーションに合わせて追加することで、より詳細に計装できるようになります。

各パッケージは、OpenTelemetryのページから見つけることができます。

[https://opentelemetry.io/ecosystem/registry/?language=dotnet&component=instrumentation:embed:cite]

### 2. 初期設定

OpenTelemetryのデータをMackerelに送信するためには、以下の項目を設定する必要があります。

- Resource
- TracerProvider

例えばC#の場合は、ASP.NETサーバーの初期化の際に以下のようにすると、SDKから直接Mackerelへ送信することができます。

```cs
using OpenTelemetry.Resources;
using OpenTelemetry.Trace;

var apiKey = Environment.GetEnvironmentVariable("MACKEREL_APIKEY");

var builder = WebApplication.CreateBuilder(args);

builder.Services
  .AddOpenTelemetry()
  .ConfigureResource(resource =>
    resource
      .AddService(serviceName: "acme_service", serviceVersion: "vX.Y.Z")
      .AddAttributes([
        new("deployment.environment.name", "production"),
      ])
  )
  .WithTracing(tracing =>
    tracing
      .AddAspNetCoreInstrumentation()
      .AddHttpClientInstrumentation()
      .AddConsoleExporter()
      .AddOtlpExporter(otlp =>
      {
        otlp.Endpoint = new Uri("https://otlp-vaxila.mackerelio.com/v1/traces");
        otlp.Headers = $"Mackerel-Api-Key={apiKey},Accept=*/*";
        otlp.Protocol = OpenTelemetry.Exporter.OtlpExportProtocol.HttpProtobuf;
      })
  );

var app = builder.Build();

app.MapGet("/", () => "Hello World!");

app.Run();
```

この例では次の項目を設定しています。

- Resource (`ConfigureResource`)
  - Resourceを設定することで、データがどこから来たかわかるようになります。
- TracerProvider (`WithTracing`)
  - Instrumentation
    - 以下の例のように取得したいデータの計装を追加します。
    - ASP.NET (`AddAspNetCoreInstrumentation`)
    - HTTPクライアント (`AddHttpClientInstrumentation`)
  - Exporter
    - 以下の例のようにデータを出力したい先を追加します。
    - Console (`AddConsoleExporter`)
      - 任意の設定です、デバッグ時などに便利です。
    - OTLP (`AddOtlpExporter`)
      - Endpoint
        - https://otlp-vaxila.mackerelio.com/v1/traces と設定することで、データを直接Mackerelに送信するようになります。
        - Collectorを利用する場合は、Collectorのエンドポイント (http://localhost:4318/v1/traces など) を設定してください。
      - Headers
        - Mackerel-Api-Key と Accept のヘッダーを設定することでMackerelと通信することができます。
        - Mackerel-Api-Key には、Mackerelで発行された書き込み権限のあるAPIキーを設定してください。
        - Collectorを利用する場合、ヘッダーは必要ないでしょう。
      - Protocol
        - `OpenTelemetry.Exporter.OtlpExportProtocol.HttpProtobuf` を設定します。
        - Collectorを利用する場合は、Collectorに合わせて設定してください。規定値は `OpenTelemetry.Exporter.OtlpExportProtocol.Grpc` です。

### 3. 独自の計装の追加 (任意)

独自の計装を追加することで、任意の範囲を計装できます。

計装によって、変数の値や処理時間を記録することができるようになります。

具体的には、下記のように `ActivitySource` を使うと計装を追加できます。

ASP.NET の場合は依存性注入の仕組みを利用すると便利です。

```cs
using System.Diagnostics;

var myActivitySource = new ActivitySource("my-service-tracer");

builder.Services
  .AddSingleton(myActivitySource)
  .WithTracing(tracing =>
    tracing
      .AddSource(myActivitySource.Name)
      // ... 既存の設定
  );

app.MapGet("/{id}", (ActivitySource activitySource, string id) =>
{
  using var activity = activitySource.StartActivity("awesome_action");
  activity?.SetTag("id", id);

  // ... 既存の処理
});
```

計装の方法は他にも用意されています。詳細はOpenTelemetryのドキュメントを参照してください。

[https://opentelemetry.io/docs/languages/net/instrumentation/:embed:cite]
