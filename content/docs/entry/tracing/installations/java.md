---
Title: Sending traces from Java applications to Mackerel
Date: 2025-03-13T16:38:43+09:00
URL: https://mackerel.io/docs/entry/tracing/installations/java
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/17179246901371634261
---

This page explains how to send traces from Java applications to Mackerel.

[:contents]

## Overview

Mackerel collects traces using the OpenTelemetry mechanism (instrumentation). While there are various ways to collect OpenTelemetry-compatible traces, this page explains zero-code instrumentation, a method that allows you to send traces without modifying your application's implementation.

<div class="note">
  <p>📝 Note</p>
  <p>Java zero-code instrumentation is achieved by running the application with an agent that automatically inserts code to collect telemetry data. For more details, see <a href="https://opentelemetry.io/docs/zero-code/java/agent/">Java Agent</a>.</p>
</div>

## Requirements

Zero-code instrumentation for Java uses the OpenTelemetry Java Agent (hereafter referred to as Java Agent).

### Java Agent Requirements

- Java 8 or later

<div class="note">
  <p>📝 Note</p>
  <p>For detailed compatibility requirements, see <a href="https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/main/VERSIONING.md">OpenTelemetry Java Instrumentation Versioning</a>.</p>
</div>

### Supported Libraries

For libraries supported by the Java Agent, please refer to the following page:

- [Supported Libraries](https://opentelemetry.io/docs/zero-code/java/agent/supported-libraries/)

## Installation

To send traces from your application to Mackerel, do the following:

1. Download the Java Agent
2. Run your application using the Java Agent

### 1. Download the Java Agent

Use the following command to download the latest version of the Java Agent (opentelemetry-javaagent.jar) and place it in a location accessible by your application:

```bash
curl -L -O https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/latest/download/opentelemetry-javaagent.jar
```

You can also download it from the [opentelemetry-java-instrumentation releases page](https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases).


### 2. Run your application using the Java Agent

Specify the Java Agent (opentelemetry-javaagent.jar) with `-javaagent` in the startup options, add other settings, and start the application.

By setting your Mackerel API key with write permissions in the `MACKEREL_APIKEY` environment variable and executing the command below, traces will be sent to Mackerel under the service name `my-sample-app`:

```bash
java -javaagent:opentelemetry-javaagent.jar \
  -Dotel.traces.exporter=otlp \
  -Dotel.service.name=my-sample-app \
  -Dotel.exporter.otlp.protocol=http/protobuf \
  -Dotel.exporter.otlp.traces.endpoint=https://otlp-vaxila.mackerelio.com/v1/traces \
  -Dotel.exporter.otlp.traces.headers="Accept=*/*,Mackerel-Api-Key=${MACKEREL_APIKEY}" \
  -Dotel.metrics.exporter=none \
  -Dotel.logs.exporter=none \
  -jar build/libs/demo-0.0.1-SNAPSHOT.jar # Replace this line with your application.
```

- Setting `-Dotel.traces.exporter` to `console` will output traces to standard output. Multiple values can be specified separated by commas.
- `-Dotel.service.name` is the service name of the trace (the value of the `service.name` attribute).
- `-Dotel.exporter.otlp.traces.endpoint` specifies the destination for sending traces.
  - To send directly to Mackerel, specify `https://otlp-vaxila.mackerelio.com/v1/traces`.
  - To send via a Collector, specify `http://<Collector address:port>/v1/traces`.
- `${MACKEREL_APIKEY}` specifies the Mackerel API key. Define an API key with Write permission from the [API key list](https://mackerel.io/my?tab=apikeys) as an environment variable in the system where the application runs.
  - It will work if you write the API key directly instead of using an environment variable.
- Disable metrics and logs that are not related to trace transmission.
  - `-Dotel.metrics.exporter=none`
  - `-Dotel.logs.exporter=none`

## Viewing sent traces

Sent traces can be verified by following these steps:

1. Select "[APM](https://mackerel.io/my/apm)" from the menu<br>
2. Select the service name
  [f:id:mackerelio:20260109153548j:plain]
3. Select the "Traces" tab
  [f:id:mackerelio:20260109153629j:plain]
4. Select a trace from the trace list to view its details
  [f:id:mackerelio:20260109153349p:plain]

That's a wrap on setting up zero-code instrumentation and sending traces of your Java applications to Mackerel.
