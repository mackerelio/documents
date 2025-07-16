---
Title: Tracing - Introducing OpenTelemetry to Go
Date: 2025-07-11T14:53:00+09:00
URL: https://mackerel.io/docs/entry/tracing/installations/go
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398507809161
---

Mackerel uses the OpenTelemetry mechanism (instrumentation) to collect data.

This page explains how to send data from Go to Mackerel.

[:contents]

## OpenTelemetry for Go

OpenTelemetry provides an SDK for Go.

In addition to this SDK, you can use SDKs for Gin and Gorm to instrument various areas.

[https://opentelemetry.io/docs/languages/go/instrumentation/:embed:cite]

### Is it appropriate to use a Collector?

When sending data to Mackerel, you can either send it directly from the SDK or use a Collector.

Refer to the following page when deciding whether to use a Collector.

[Deciding whether to use Collector](https://mackerel.io/docs/entry/tracing/guide/what-is-opentelemetry#using-collector-or-not)

## Implementation

There are several web frameworks available for Go, but this page explains how to implement the most basic `net/http`. If you are using another framework, you can implement instrumentation in much the same way as described below.

You can implement Mackerel's tracing feature by following the steps below.

1. go get
2. Initial setup
3. Inserting middleware
4. Adding error handling
5. Adding custom instrumentation (optional)

### 1. go get

Install the following modules.

```sh
go get go.opentelemetry.io/otel \
  go.opentelemetry.io/otel/trace \
  go.opentelemetry.io/otel/exporters/otlp/otlptrace
```

This command installs only the basic modules.

However, OpenTelemetry has modules for various purposes, such as Gin, Gorm, and AWS, and you can add them according to your application to enable more detailed instrumentation.

Each module can be found on the OpenTelemetry page.

[https://opentelemetry.io/ecosystem/registry/?language=go&component=instrumentation:embed:cite]

### 2. Initial setup

To send OpenTelemetry data to Mackerel, you need to set the following items in TracerProvider.

* Exporter
* Resource

For example, the following will allow you to send data directly from the SDK to Mackerel.

```go
import (
  ...
  "go.opentelemetry.io/otel/exporters/otlp/otlptrace"
  "go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracehttp"
  "go.opentelemetry.io/otel/exporters/stdout/stdouttrace"
  "go.opentelemetry.io/otel/sdk/trace"
  "go.opentelemetry.io/otel/attribute"
  semconv "go.opentelemetry.io/otel/semconv/v1.20.0"
)

func initTracerProvider(ctx context.Context) (func(context.Context) error, error) {
  client := otlptracehttp.NewClient(
    otlptracehttp.WithEndpoint("otlp-vaxila.mackerelio.com"),
    otlptracehttp.WithHeaders(map[string]string{
      "Accept":         "*/*",
      "Mackerel-Api-Key": os.Getenv("MACKEREL_API_KEY"),
    }),
    otlptracehttp.WithCompression(otlptracehttp.GzipCompression),
  )
  exporter, err := otlptrace.New(ctx, client)
  // stdouttrace is useful for debugging
  // exporter, err := stdouttrace.New()
  if err != nil {
    return nil, err
  }
  
  resources, err := resource.New(
    ctx,
    resource.WithProcessPID(),
    resource.WithHost(),
    resource.WithAttributes(
      semconv.ServiceName("acme_service"),
      semconv.ServiceVersion("vX.Y.Z"),
      semconv.DeploymentEnvironment("production"),
    ),
  )
  if err != nil {
    return nil, err
  }
  
  tp := trace.NewTracerProvider(
    trace.WithBatcher(exporter),
    trace.WithResource(resources),
  )
  otel.SetTracerProvider(tp)
  otel.SetTextMapPropagator(propagation.TraceContext{})

  return tp.Shutdown, nil
}
```

This example sets the following items.

* Exporter
  * Endpoint & Path
    * Setting the endpoint to `otlp-vaxila.mackerelio.com` sends data to Mackerel.
    * If using a Collector, set the Collector endpoint (such as localhost:4318).
  * Headers
    * Setting the `Mackerel-Api-Key` and `Accept` headers enables communication with Mackerel.
    * Set the `Mackerel-Api-Key` to the write-enabled API key issued by Mackerel. It may take about a minute for changes to take effect after modifying the API key permissions.
    * When using Collector, headers are not necessary.
  * Compression
    * You can specify the data compression method when sending data. If not specified, data will not be compressed.
* Resource
  * By setting the Resource of TracerProvider, you can identify the source of the data.

### 3. Inserting middleware

If you are running an HTTP server, you need to insert dedicated middleware to collect information for each request.

If you are using `net/http`, you can use `otelhttp`.

For example, using the `initTracerProvider` function created in the previous section, you can do the following.

```go
import (
  ...
  "go.opentelemetry.io/contrib/instrumentation/net/http/otelhttp"
)

func main() {
  ctx := context.Background()
  shutdown, err := initTracerProvider(ctx)
  if err != nil {
    panic(err)
  }
  defer shutdown(ctx)

  otelHandler := otelhttp.NewHandler(
    http.HandlerFunc(awesomeActionHandler),
    "awesome_span_name",
  )
  http.Handle("/awesome_path/", otelHandler)
  http.ListenAndServe(":80", nil)
}

func awesomeActionHandler(w http.ResponseWriter, r *http.Request) {
  ...
}
```

#### ⚠️ If you are using another framework

This page explains how to instrument `net/http`, but even if you are using Gin or Echo, the interface is similar, so it is basically the same as `net/http`.

In other words, you can instrument it with the following steps.

1. Get the module corresponding to your framework using go get
2. Perform initial setup
3. Insert middleware and start tracing

In addition, since routing is well established, instrumentation is generally easier than with `net/http`.

You can search for modules corresponding to each framework on the following page.

[https://opentelemetry.io/ecosystem/registry/?language=go&component=instrumentation:embed:cite]

### 4. Catch error information

Due to Go's language constraints, errors cannot be automatically caught, so you need to write code to catch errors and record them to the Span when they occur.



Use the `Span.RecordError()` function to add errors. You can also use `WithStackTrace()` to record stack traces.

For example, to catch errors that occur in the handler (`awesomeActionHandler`) created in the previous section, use the following code.

```go
import (
  ...
  "go.opentelemetry.io/otel/trace"
  semconv "go.opentelemetry.io/otel/semconv/v1.17.0"
)

func awesomeActionHandler(w http.ResponseWriter, r *http.Request) {
  err := someAction()
  if err != nil {
    recordError(r.Context(), err)
    w.WriteHeader(500)
    return
  }

  ...
}

func recordError(ctx context.Context, err error) {
  span := trace.SpanFromContext(ctx)
  span.RecordError(err, trace.WithStackTrace(true))
}
```

### 5. Adding custom instrumentation (optional)

You can add custom spans to instrument any range.

Instrumentation makes it possible to record variable values and processing times.

You can add instrumentation by enclosing it with `Tarcer.Start()` and `Span.End()`, as shown below.

```go
const (
  instrumentationName    = "my-instrumentron"
  instrumentationVersion = "v0.1.0"
)

var (
  tracer = otel.GetTracerProvider().Tracer(
  instrumentationName,
    trace.WithInstrumentationVersion(instrumentationVersion),
    trace.WithSchemaURL(semconv.SchemaURL),
  )
)

func awesomeActionHandler(w http.ResponseWriter, r *http.Request) {
  ctx, span := tracer.Start(r.Context(), "awesome_action")
  defer span.End()

  ...
}
```

Other instrumentation approaches are also available. For details, refer to the OpenTelemetry documentation.

[https://opentelemetry.io/docs/languages/go/instrumentation/:embed:cite]
