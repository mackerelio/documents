---
Title: Tracing - Introducing OpenTelemetry to other languages
Date: 2025-07-11T16:40:58+09:00
URL: https://mackerel.io/docs/entry/tracing/installations/other-language
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398507897725
---

Mackerel uses the OpenTelemetry mechanism (instrumentation) to collect data.

This page explains how to send data to Mackerel using languages not covered in the documentation.

[:contents]

## OpenTelemetry SDK

OpenTelemetry has a number of SDKs that support many languages and frameworks. We cannot document all of them, but the configuration is common across them.

This page explains the language-independent configuration.

Additionally, since the OpenTelemetry SDKs follow a common architecture, pages explaining instrumentation for other languages may also be helpful.

For example, refer the following pages:

* [Introducing OpenTelemetry to Go](https://mackerel.io/docs/entry/tracing/installations/go)
* [Introducing OpenTelemetry to Ruby](https://mackerel.io/docs/entry/tracing/installations/ruby)

### Is it appropriate to use a Collector?

When sending data to Mackerel, you can use a Collector instead of sending directly from the SDK.

Refer to the following page when deciding whether to use a Collector.

[Deciding whether to use Collector](https://mackerel.io/docs/entry/tracing/guide/what-is-opentelemetry#using-collector-or-not)

## Implementation

The SDK can be implemented in the following steps.

1. Search for SDK
2. Configure settings within the application
3. Catch error information
4. Add custom instrumentation (optional)

### 1. Search for the SDK

Search for the language you want to use in the official OpenTelemetry Registry.

Set `Language` to the target language and `Type` to Instrumentation on the following page to search for the SDK for the target language.

[https://opentelemetry.io/ecosystem/registry:embed:cite]

### 2. Settings within the application

When executing the SDK within the application, you need to configure settings such as the endpoint. Configure the following items.

1. Endpoint
  * If you want to send data directly from the SDK to Mackerel, use the Mackerel endpoint (`https://otlp-vaxila.mackerelio.com/v1/traces`)
  * If using Collector, use the Collector endpoint (`http://localhost:4318/v1/traces`, etc.)
2. Headers when sending
  * When sending data directly from the SDK to Mackerel, the following headers are required.
  * `Accept`: `*/*`
  * Set the `Mackerel-Api-Key` header to the API key with write permissions issued by Mackerel. It may take about a minute for changes to take effect after modifying the API key permissions.
  * When using Collector, headers are not required.
3. Resource
  * At a minimum, we recommend setting the following.
    * Service name (`service.name`)
    * Service version (`service.version`)
    * Environment name (`deployment.environment.name`)

### 3. Catch error information

Unfortunately, some SDKs do not automatically catch error information when an error occurs.

Therefore, you may need to modify your code to add error messages and stack traces to Span when an error occurs.

You can determine whether the SDK you are using catches errors by checking whether the error details are written in the `Events` of the trace where the error occurred.

If errors are caught, the error details and stack traces will be added to `events` as follows.

```json
{
  "name": "GET awesome/path",
  "context": {
    "trace_id": "0xb999451d4eba88ae77c77f829c2d6350",
    "span_id": "0x5fca4b11492b6856",
  },
  "attributes": {...},
  "events": [
    {
      "name": "exception",
      "timestamp": "2001-02-03T04:05:06.000000Z",
      "attributes": {
        "exception.type": "AwesomeException",
        "exception.message": "Error happened",
        "exception.stacktrace": "Traceback (most recent call last):\n  File \"/usr/local/lib/python3.11/site-packages/opentelemetry/trace/__init__.py\", line 573, in use_span\n...",
      }
    }
  ],
  ...
}
```

If errors are not caught, you need to add OpenTelemetry events (Events) at locations where errors are caught, such as the error handling part of the framework.

To do so, call functions such as `recordException` or `recordError` provided by the SDK.

This will add an Event with the following attributes to the Span.

<table><thead><tr><th width="211.33333333333331">Attribute name</th><th width="185">Attribute meaning</th><th>Example</th></tr></thead><tbody><tr><td>exception.type</td><td>Error type</td><td>java.net.ConnectException</td></tr><tr><td>exception.message</td><td>Error message</td><td>Can't convert ‘int’ object to str implicitly…</td></tr><tr><td>exception.stacktrace</td><td>Stack trace</td><td>Exception in thread “main” java.lang.RuntimeException: Test exception\n at <br>...</td></tr></tbody></table>

Mackerel uses these attributes to understand the error details and group them.

### 4. Adding custom instrumentation (optional)

You can add custom Spans to instrument any range.

Instrumentation makes it possible to record variable values and processing times.

This should be available in all languages.
