---
Title: Sending traces from .NET applications to Mackerel
Date: 2025-07-11T16:38:42+09:00
URL: https://mackerel.io/docs/entry/tracing/installations/dotnet
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398507897279
---

This page explains how to send traces from .NET applications to Mackerel.

[:contents]

## Overview

Mackerel collects traces using the OpenTelemetry mechanism (instrumentation). While there are various ways to collect OpenTelemetry-compatible traces, this page explains zero-code instrumentation, a method that allows you to send traces without modifying your application's implementation.

<div class="note">
  <p>📝 Note</p>
  <p>.NET zero-code instrumentation is implemented by rewriting method IL (Intermediate Language) using CLR features just before the program is executed (during JIT compilation). For more details, see <a href="https://opentelemetry.io/docs/zero-code/dotnet/">.NET zero-code instrumentation</a>.</p>
</div>

## Requirements

.NET zero-code instrumentation must meet the following [requirements](https://opentelemetry.io/docs/zero-code/dotnet/#compatibility):

- .NET 8 or higher
- .NET Framework 4.6.2 or higher
- Processor
  - x86
  - AMD64 (x86-64)
  - ARM64 ([Experimental](https://opentelemetry.io/docs/specs/otel/versioning-and-stability/))

## Installation

To send traces from your application to Mackerel, follow the steps below. For Windows, execute the commands in PowerShell. Administrator privileges are required for execution.

1. Download the installation script
2. Install the instrumentation tools
3. Run the instrumentation tools

### 1. Download the installation script

Download the installation script for the tools needed for OpenTelemetry instrumentation with the following command.

**For Linux**

```
curl -L -O https://github.com/open-telemetry/opentelemetry-dotnet-instrumentation/releases/latest/download/otel-dotnet-auto-install.sh
```

**For Windows**

```
$module_url = "https://github.com/open-telemetry/opentelemetry-dotnet-instrumentation/releases/latest/download/OpenTelemetry.DotNet.Auto.psm1"
$download_path = Join-Path $env:temp "OpenTelemetry.DotNet.Auto.psm1"
Invoke-WebRequest -Uri $module_url -OutFile $download_path -UseBasicParsing
```

### 2. Install the instrumentation tools

Install the tools needed for OpenTelemetry instrumentation with the following command.

**For Linux**

```
./otel-dotnet-auto-install.sh
```

**For Windows**

```
Import-Module $download_path
Install-OpenTelemetryCore
```

### 3. Run the instrumentation tools

Execute the instrumentation tools along with the environment variable settings as shown below, and then start your application within that same session. This will send traces to Mackerel under the service name my-sample-app.

**For Linux**

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

**For Windows**

```
$env:OTEL_TRACES_EXPORTER="otlp"
$env:OTEL_METRICS_EXPORTER="none"
$env:OTEL_LOGS_EXPORTER="none"
$env:OTEL_EXPORTER_OTLP_PROTOCOL="http/protobuf"
$env:OTEL_EXPORTER_OTLP_TRACES_ENDPOINT="https://otlp-vaxila.mackerelio.com/v1/traces"
$env:OTEL_EXPORTER_OTLP_TRACES_HEADERS="Accept=*/*,Mackerel-Api-Key=$env:MACKEREL_APIKEY"
Register-OpenTelemetryForCurrentSession -OTelServiceName "my-sample-app"
```

**Environment Variable Details**

- Setting `OTEL_TRACES_EXPORTER` to `console` will output traces to standard output.
- Disable metrics and logs that are not related to trace transmission.
  - `$env:OTEL_METRICS_EXPORTER="none"`
  - `$env:OTEL_LOGS_EXPORTER="none"`
- `OTEL_EXPORTER_OTLP_TRACES_ENDPOINT` specifies the destination for sending traces.
  - To send directly to Mackerel, specify `https://otlp-vaxila.mackerelio.com/v1/traces`.
  - To send via a Collector, specify `http://<Collector address:port>/v1/traces`.
- `Mackerel-Api-Key` specifies the Mackerel API key. Define an API key with Write permission from the [API key list](https://mackerel.io/my?tab=apikeys) as an environment variable in the system where the application runs.
  - For Linux: `${MACKEREL_APIKEY}`
  - For Windows: `$env:MACKEREL_APIKEY`
  - It will work if you write the API key directly instead of using an environment variable.
- `my-sample-app` is the service name of the trace (the value of the `service.name` attribute). Specify any name you prefer.

## Viewing sent traces

Sent traces can be verified by following these steps:

1. Select "[APM](https://mackerel.io/my/apm)" from the menu<br>
2. Select the service name
  [f:id:mackerelio:20260109153548j:plain]
3. Select the "Traces" tab
  [f:id:mackerelio:20260109153629j:plain]
4. Select a trace from the trace list to view its details
  [f:id:mackerelio:20260109153349p:plain]

That's a wrap on setting up zero-code instrumentation and sending traces of your .NET applications to Mackerel.
