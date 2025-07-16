---
Title: Tracing - Introducing OpenTelemetry to Node.js
Date: 2025-07-11T16:15:38+09:00
URL: https://mackerel.io/docs/entry/tracing/installations/nodejs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398507884634
---

Mackerel uses the OpenTelemetry mechanism (instrumentation) to collect data.

This page explains how to send data from Node.js to Mackerel.

[:contents]

## OpenTelemetry for Node.js

OpenTelemetry provides an SDK for Node.js.

In addition to this SDK, you can use SDKs for Express and AWS to instrument a wide range of areas.

[https://opentelemetry.io/docs/languages/js/getting-started/nodejs/:embed:cite]

### Is it appropriate to use a Collector?

When sending data to Mackerel, you can either send it directly from the SDK or use a Collector.

Refer to the following page to decide whether to use a Collector.

[Deciding whether to use Collector](https://mackerel.io/docs/entry/tracing/guide/what-is-opentelemetry#using-collector-or-not)

## How to get started

There are multiple web frameworks for Node.js, but this page explains how to get started with Express. You can instrument your application in much the same way if you are using a different framework.

You can implement Mackerel's tracing feature by following the steps below.

1. Add packages
2. Initial setup
3. Add custom instrumentation (optional)

### 1. Add packages

Use the following packages.

```bash
npm install @opentelemetry/api \
  @opentelemetry/auto-instrumentations-node \
  @opentelemetry/exporter-trace-otlp-proto \
  @opentelemetry/resources \
  @opentelemetry/sdk-node \
  @opentelemetry/sdk-trace-node \
  @opentelemetry/semantic-conventions
```

The `auto-instrumentations-node` added by the above command contains many packages, so popular libraries such as Express and http are automatically instrumented.

For a list of included packages, refer to the [GitHub page](https://github.com/open-telemetry/opentelemetry-js-contrib/tree/main/packages/auto-instrumentations-node#supported-instrumentations).

If any are missing, you can search for them on the OpenTelemetry page.

[https://opentelemetry.io/ecosystem/registry/?language=js&component=instrumentation:embed:cite]

### 2. Initial setup

To send OpenTelemetry data to Mackerel, you need to configure the following items.

* Exporter
* resource

For example, the following code sends data directly from the SDK to Mackerel.

```javascript
const { resourceFromAttributes, processDetector, hostDetector } = require('@opentelemetry/resources');
const { OTLPTraceExporter } = require("@opentelemetry/exporter-trace-otlp-proto");
const { ConsoleSpanExporter } = require('@opentelemetry/sdk-trace-node');
const { NodeSDK } = require('@opentelemetry/sdk-node');
const { getNodeAutoInstrumentations } = require('@opentelemetry/auto-instrumentations-node');
const {
  ATTR_SERVICE_NAME,
  ATTR_SERVICE_VERSION,
} = require("@opentelemetry/semantic-conventions");

// ATTR_DEPLOYMENT_ENVIRONMENT_NAME is defined separately
ATTR_DEPLOYMENT_ENVIRONMENT_NAME = 'deployment.environment.name';

const exporter = new OTLPTraceExporter({
  maxQueueSize: 1000,
  url: "https://otlp-vaxila.mackerelio.com/v1/traces",
  headers: {
    "Accept": "*/*",
    "Mackerel-Api-Key": process.env.MACKEREL_API_KEY,
  }
})
// ConsoleSpanExporter is useful for debugging
// const exporter = new ConsoleSpanExporter()

const sdk = new NodeSDK({
  traceExporter: exporter,
  instrumentations: [
    getNodeAutoInstrumentations({
      // Instrumentation for fs creates a large number of traces at startup, so it is recommended to disable it if not necessary.
      '@opentelemetry/instrumentation-fs': {
        enabled: false,
      },
    }),
  ],
  resource: resourceFromAttributes({
    [ATTR_SERVICE_NAME]: "acme_service",
    [ATTR_SERVICE_VERSION]: "vX.Y.Z",
    [ATTR_DEPLOYMENT_ENVIRONMENT_NAME]: "production"
  }),
  resourceDetectors: [processDetector, hostDetector]
});

sdk.start()
```

This example sets the following items.

* Exporter
  * url
    * Setting `https://otlp-vaxila.mackerelio.com/v1/traces` sends data directly to Mackerel.
    * If using a Collector, set the Collector endpoint (e.g., `http://localhost:4318/v1/traces`).
  * headers
    * Setting the `Mackerel-Api-Key` and `Accept` headers enables communication with Mackerel.
    * Set the `Mackerel-Api-Key` to the write-enabled API key issued by Mackerel. It may take about a minute for changes to take effect after modifying the API key permissions.
    * When using Collector, headers are not necessary.
* resource & resourceDetectors
  * Setting the NodeSDK resource allows you to identify the source of the data.
  * `ATTR_DEPLOYMENT_ENVIRONMENT_NAME` is included in `@opentelemetry/semantic-conventions/incubating` as of May 2025, but since this package is unstable, it is defined individually here without importing it, as recommended in [Unstable SemConv](https://www.npmjs.com/package/@opentelemetry/semantic-conventions#unstable-semconv). It is expected to be available in `@opentelemetry/semantic-conventions` in the future.

### 3. Adding custom instrumentation (optional)

You can add custom spans to instrument any range.

Instrumentation makes it possible to record variable values and processing times.

You can add instrumentation by enclosing it with `startActiveSpan` as shown below.

```javascript
const tracer = opentelemetry.trace.getTracer(
  'my-service-tracer'
);
tracer.startActiveSpan('awesome_action', (span) => {
  // ... existing processing

  span.end();
});
```

Other instrumentation approaches are also available. For details, refer to the OpenTelemetry documentation.

[https://opentelemetry.io/docs/languages/js/instrumentation/:embed:cite]
