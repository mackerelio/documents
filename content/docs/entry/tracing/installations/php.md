---
Title: Tracing - Introducing OpenTelemetry to PHP
Date: 2025-07-11T16:27:38+09:00
URL: https://mackerel.io/docs/entry/tracing/installations/php
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398507892445
---

Mackerel uses the OpenTelemetry mechanism (instrumentation) to collect data.

This page explains how to send data from PHP to Mackerel.

[:contents]

## OpenTelemetry for PHP

OpenTelemetry provides an SDK for PHP.

In addition to this SDK, you can use SDKs for Laravel and PDO to instrument your application without modifying your existing implementation.

[https://opentelemetry.io/docs/languages/php/instrumentation/:embed:cite]

### Is it appropriate to use a Collector?

When sending data to Mackerel, you can use Collector instead of sending it directly from the SDK.

However, many PHP runtimes block requests until all processing is complete, so sending data directly to Mackerel will delay requests by the amount of time it takes to send the data to Mackerel.

Therefore, we recommend using Collector to respond to users as quickly as possible.

Alternatively, if you use `fastcgi_finish_request()` in `fastcgi`, you can return a response to the user without waiting for the data to be sent to Mackerel. In that case, sending data directly to Mackerel should not cause any issues.

For instructions on how to use Collector, please refer to the following page.

[https://mackerel.io/docs/entry/tracing/installations/opentelemetry-collector:embed:cite]

## How to get started

There are several web frameworks for PHP, but this page explains how to get started with Laravel.

You can use almost the same approaches to instrument other frameworks.

You can implement the Mackerel's tracing feature by following the steps below.

1. Add the extension library (PECL)
2. Add the package
3. Set the environment variables
4. Custom instrumentation (optional)

### 1. Add the extension library (PECL)

Install the extension library for OpenTelemetry using PECL.

```bash
pecl install opentelemetry
```

Then, add the installed extension to `php.ini`.

```ini
[opentelemetry]
extension=opentelemetry.so
```

### 2. Add packages

Add packages for Laravel and PDO.

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

#### ⚠️ If you are using another framework

This page uses `opentelemetry-auto-laravel` as an example for Laravel. If you are using a different framework such as Slim or Symfony, you can search for the appropriate package from the following page.

[https://opentelemetry.io/ecosystem/registry/?language=php&component=instrumentation:embed:cite]

### 3. Set environment variables

After installing the package, set the environment variables to send data to Mackerel. The following is an example of setting the variables in the `.env` file in the project.

```
OTEL_PHP_AUTOLOAD_ENABLED=true
OTEL_SERVICE_NAME=your-service-name
OTEL_TRACES_EXPORTER=otlp
# Set to console for debugging
# OTEL_TRACES_EXPORTER=console
OTEL_EXPORTER_OTLP_PROTOCOL=http/protobuf
OTEL_EXPORTER_OTLP_TRACES_ENDPOINT=https://otlp-vaxila.mackerelio.com/v1/traces
OTEL_EXPORTER_OTLP_TRACES_HEADERS="Accept=*/*,Mackerel-Api-Key=${MACKEREL_APIKEY}"
OTEL_METRICS_EXPORTER=none
OTEL_LOGS_EXPORTER=none
OTEL_PROPAGATORS=baggage,tracecontext
```

Set these environment variables before autoloading starts.

** Change the values of `OTEL_SERVICE_NAME` and `OTEL_EXPORTER_OTLP_TRACES_ENDPOINT` to values appropriate for your environment. Also, setting `OTEL_TRACES_EXPORTER` to `console` will output data to the log, which is useful for debugging when transmission does not work. **

That completes the installation, and data will now be sent to Mackerel through the Collector.

### 4. Custom instrumentation (optional)

You can add custom spans to instrument any range you want.

Instrumentation makes it possible to record variable values and processing times.

You can add instrumentation using `startSpan`, as shown below.

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

There are other ways to instrument. For details, refer to the OpenTelemetry documentation.

[https://opentelemetry.io/docs/languages/php/instrumentation/:embed:cite]
