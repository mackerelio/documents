---
Title: Tracing - Introducing OpenTelemetry to .NET
Date: 2025-07-11T16:38:42+09:00
URL: https://mackerel.io/docs/entry/tracing/installations/dotnet
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398507897279
---

Mackerel uses the OpenTelemetry mechanism (instrumentation) to acquire data.

This page explains how to send data from .NET to Mackerel.

[:contents]

## OpenTelemetry for .NET

OpenTelemetry provides an SDK for .NET.

In addition to this SDK, you can use SDKs for ASP.NET and Entity Framework to instrument various areas.

[https://opentelemetry.io/docs/languages/dotnet/instrumentation/:embed:cite]

### Is it appropriate to use a Collector?

When sending data to Mackerel, you can use a Collector instead of sending it directly from the SDK.

Refer to the following page when deciding whether to use Collector.

[Deciding whether to use Collector](https://mackerel.io/docs/entry/tracing/guide/what-is-opentelemetry#using-collector-or-not)

## How to get started

There are several web frameworks for .NET, but this page explains how to get started with ASP.NET. If you are using another framework, you can instrument it in almost the same way.

You can implement Mackerel's tracing feature by following the steps below.

1. Add the NuGet packages
2. Initial setup
3. Add custom instrumentation (optional)

### 1. Add the NuGet packages

Use the following package.

```console
dotnet add package OpenTelemetry.Instrumentation.AspNetCore
dotnet add package OpenTelemetry.Exporter.OpenTelemetryProtocol
dotnet add package OpenTelemetry.Extensions.Hosting
```

This command installs only the basic packages.

As described later, if you want to configure an HTTP client or output to the console, add the following packages as well.

```console
dotnet add package OpenTelemetry.Instrumentation.Http
dotnet add package OpenTelemetry.Exporter.Console
```

OpenTelemetry also offers packages for various other uses, such as EntityFramework and Azure, which can be added to your application to enable more detailed monitoring.

Each package can be found on the OpenTelemetry page.

[https://opentelemetry.io/ecosystem/registry/?language=dotnet&component=instrumentation:embed:cite]

### 2. Initial setup

To send OpenTelemetry data to Mackerel, you need to configure the following items.

- Resource
- TracerProvider

For example, in C#, you can configure the following during ASP.NET server initialization to send data directly from the SDK to Mackerel.

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

In this example, the following items are configured.

- Resource (`ConfigureResource`)
  - By configuring the Resource, you can identify the source of the data.
- TracerProvider (`WithTracing`)
  - Instrumentation
    - Add the instrumentation for the data you want to collect, as shown in the following example.
    - ASP.NET (`AddAspNetCoreInstrumentation`)
    - HTTP client (`AddHttpClientInstrumentation`)
- Exporter
  - Add the destination where you want to output the data as shown in the following example.
  - Console (`AddConsoleExporter`)
    - Optional settings, useful for debugging, etc.
  - OTLP (`AddOtlpExporter`)
    - Endpoint
      - Setting this to `https://otlp-vaxila.mackerelio.com/v1/traces` will send the data directly to Mackerel.
      - If using a Collector, set the Collector endpoint (e.g., `http://localhost:4318/v1/traces`).
    - Headers
      - Setting the Mackerel-Api-Key and Accept headers enables communication with Mackerel.
      - Set the Mackerel-Api-Key to the API key with write permissions issued by Mackerel.
      - When using Collector, headers are not necessary.
    - Protocol
      - Set to `OpenTelemetry.Exporter.OtlpExportProtocol.HttpProtobuf`.
      - When using Collector, configure according to the Collector settings. The default value is `OpenTelemetry.Exporter.OtlpExportProtocol.Grpc`.

### 3. Adding custom instrumentation (optional)

By adding custom instrumentation, you can instrument any range.

Instrumentation makes it possible to record variable values and processing times.

You can add instrumentation using `ActivitySource` as shown below.

In ASP.NET, it is convenient to use dependency injection.

```cs
using System.Diagnostics;

var myActivitySource = new ActivitySource("my-service-tracer");

builder.Services
  .AddSingleton(myActivitySource)
  .WithTracing(tracing =>
    tracing
      .AddSource(myActivitySource.Name)
      // ... existing settings
  );

app.MapGet("/{id}", (ActivitySource activitySource, string id) =>
{
  using var activity = activitySource.StartActivity("awesome_action");
  activity?.SetTag("id", id);

  // ... existing processing
});
```

Other instrumentation approaches are available. For details, refer to the OpenTelemetry documentation.

[https://opentelemetry.io/docs/languages/net/instrumentation/:embed:cite]
