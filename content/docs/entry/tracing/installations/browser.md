---
Title: Tracing - Introducing OpenTelemetry to the Browser
Date: 2026-03-11T00:00:00+09:00
URL: https://mackerel.io/docs/entry/tracing/installations/browser
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/17179246901365859353
---

Mackerel uses the OpenTelemetry mechanism (instrumentation) to collect data.

This page explains how to send data from browser-based web applications to Mackerel.

[:contents]

## OpenTelemetry for the Browser

OpenTelemetry provides an SDK for browsers (`@opentelemetry/sdk-trace-web`).

You can use this SDK to measure performance data such as page load times and user interactions.

[https://opentelemetry.io/docs/languages/js/getting-started/browser/:embed:cite]

## Prerequisites

To post traces from the browser to Mackerel, you need a client token. Please create a client token from the dashboard in advance.

For more details about client tokens, refer to the following page.

[Client Tokens](https://mackerel.io/docs/entry/tracing/guide/client-token)

## Implementation

You can implement Mackerel's tracing feature by following the steps below.

1. Add packages
2. Initial setup
3. Configure sampling

### 1. Add packages

Install the following packages.

```bash
npm install @opentelemetry/api \
  @opentelemetry/sdk-trace-web \
  @opentelemetry/sdk-trace-base \
  @opentelemetry/exporter-trace-otlp-proto \
  @opentelemetry/instrumentation \
  @opentelemetry/instrumentation-document-load \
  @opentelemetry/instrumentation-fetch \
  @opentelemetry/context-zone \
  @opentelemetry/resources \
  @opentelemetry/semantic-conventions
```

### 2. Initial setup

To send OpenTelemetry data to Mackerel, you need to set the following items.

* Exporter
* resource

For example, the following will allow you to send data from the SDK to Mackerel.

```javascript
import { WebTracerProvider } from '@opentelemetry/sdk-trace-web';
import { OTLPTraceExporter } from '@opentelemetry/exporter-trace-otlp-proto';
import { SimpleSpanProcessor } from '@opentelemetry/sdk-trace-base';
import { DocumentLoadInstrumentation } from '@opentelemetry/instrumentation-document-load';
import { FetchInstrumentation } from '@opentelemetry/instrumentation-fetch';
import { ZoneContextManager } from '@opentelemetry/context-zone';
import { resourceFromAttributes } from '@opentelemetry/resources';
import { registerInstrumentations } from '@opentelemetry/instrumentation';
import {
  ATTR_SERVICE_NAME,
  ATTR_SERVICE_VERSION,
} from '@opentelemetry/semantic-conventions';

// Define ATTR_DEPLOYMENT_ENVIRONMENT_NAME separately
const ATTR_DEPLOYMENT_ENVIRONMENT_NAME = 'deployment.environment.name';

// Exporter configuration using Client Token
const exporter = new OTLPTraceExporter({
  url: 'https://otlp-vaxila.mackerelio.com/v1/traces',
  headers: {
    'X-Mackerel-Client-Token': 'YOUR_CLIENT_TOKEN_HERE',
  },
});

// TracerProvider configuration
const provider = new WebTracerProvider({
  resource: resourceFromAttributes({
    [ATTR_SERVICE_NAME]: 'my-web-app',
    [ATTR_SERVICE_VERSION]: 'vX.Y.Z',
    [ATTR_DEPLOYMENT_ENVIRONMENT_NAME]: 'production',
  }),
  spanProcessors: [new SimpleSpanProcessor(exporter)],
  // Consider using BatchSpanProcessor in production
  // spanProcessors: [new BatchSpanProcessor(exporter)],
});

provider.register({
  // Using ZoneContextManager supports asynchronous operations
  contextManager: new ZoneContextManager(),
});

registerInstrumentations({
    instrumentations: [
    // Using DocumentLoadInstrumentation to measure page load performance
    new DocumentLoadInstrumentation(),
    // Using FetchInstrumentation to measure the performance of HTTP requests made with the Fetch API
    new FetchInstrumentation()
],
});
```

This example sets the following items.

* Exporter
  * url
    * Setting the url to `https://otlp.mackerelio.com/v1/traces` sends data to Mackerel.
  * headers
    * Set the client token in the `X-Mackerel-Client-Token` header.
    * For how to create a client token, refer to [Client Tokens](https://mackerel.io/docs/entry/tracing/guide/client-token).
* resource
  * By setting `ATTR_SERVICE_NAME` to your service name, you can identify the source of the data.
  * As of May 2025, `ATTR_DEPLOYMENT_ENVIRONMENT_NAME` is included in `@opentelemetry/semantic-conventions/incubating`, but since this package is unstable, it is defined separately instead of importing it, as recommended by [Unstable SemConv](https://www.npmjs.com/package/@opentelemetry/semantic-conventions#unstable-semconv).
 
[f:id:mackerelio:20260317141509p:plain]

### 3. Configure sampling

Traces from browsers can generate a large volume proportional to the number of users. To prevent exceeding rate limits and increased costs, configuring sampling is recommended.

By using `TraceIdRatioBasedSampler`, you can configure it to send only a certain percentage of traces. Add the `sampler` option to the initial setup code described above.

```javascript
import { TraceIdRatioBasedSampler } from '@opentelemetry/sdk-trace-base';

const provider = new WebTracerProvider({
  sampler: new TraceIdRatioBasedSampler(0.1), // Record only 10% of traces
  // ... resource and spanProcessors configuration from above
});
```

Adjust the sampling rate according to the scale of your application and the volume of traffic.

Other instrumentation approaches are also available. For details, refer to the OpenTelemetry documentation.

[https://opentelemetry.io/docs/languages/js/getting-started/browser/:embed:cite]
