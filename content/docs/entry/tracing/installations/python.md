---
Title: Sending traces from Python applications to Mackerel
Date: 2025-07-11T16:34:11+09:00
URL: https://mackerel.io/docs/entry/tracing/installations/python
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398507896178
---

This page explains how to send traces from Python applications to Mackerel.

[:contents]

## Overview

Mackerel collects traces using the OpenTelemetry mechanism (instrumentation). While there are various ways to collect OpenTelemetry-compatible traces, this page explains zero-code instrumentation, a method that allows you to send traces without modifying your application's implementation.

<div class="note">
  <p>📝 Note</p>
  <p>Python zero-code instrumentation is achieved by using an agent that modifies library functions at runtime, allowing you to collect telemetry data such as traces without changing your code. For more details, see <a href="https://opentelemetry.io/docs/zero-code/python/">Python zero-code instrumentation</a>.</p>
</div>

## Requirements

According to the OpenTelemetry [Version support](https://opentelemetry.io/docs/languages/python/#version-support), Python must be the specified version or higher.

- Python 3.9 or higher

## Installation

To send traces from your application to Mackerel, do the following:

1. Install packages
2. Run your application through the agent

### 1. Install packages

Run the following command to install the necessary packages for using OpenTelemetry for Python.

```bash
pip install opentelemetry-distro opentelemetry-exporter-otlp
```

Next, use the `opentelemetry-bootstrap` command to install the instrumentation libraries that match the packages in your site-packages folder.

```bash
opentelemetry-bootstrap -a install
```

OpenTelemetry supports many Python packages and provides individual instrumentation libraries for each of them. Available packages can be searched on the following page.

[https://opentelemetry.io/ecosystem/registry/?language=python&component=instrumentation:embed:cite]


<div class="note">
  <p>📝 Note</p>
  <p>If there is no instrumentation library for your framework or application, you can implement manual instrumentation. For more details, see <a href="https://opentelemetry.io/docs/languages/python/instrumentation/">Instrumentation | OpenTelemetry</a> in the official OpenTelemetry documentation.</p>
</div>

### 2. Run your application through the agent

When using zero-code instrumentation, you can automatically send traces from your application by running it through `opentelemetry-instrument`.

By setting your Mackerel API key with write permissions in the `MACKEREL_APIKEY` environment variable and executing the command below, you can send your traces on Mackerel under the service name `my-sample-app`.

```sh
opentelemetry-instrument \
    --traces_exporter otlp \
    --metrics_exporter none \
    --logs_exporter none \
    --service_name my-sample-app \
    --exporter_otlp_traces_endpoint https://otlp-vaxila.mackerelio.com/v1/traces \
    --exporter_otlp_traces_headers "Mackerel-Api-Key=${MACKEREL_APIKEY},Accept='*/*'" \
    --exporter_otlp_traces_protocol http/protobuf \
    python myapp.py  # Replace this line with your application's startup command.
```

- Setting `--traces_exporter` to `console` will output traces to standard output.
- `--service_name` is the service name of the trace (the value of the `service.name` attribute).
- `--exporter_otlp_traces_endpoint` specifies the destination for sending traces.
  - To send directly to Mackerel, specify `https://otlp-vaxila.mackerelio.com/v1/traces`.
  - To send via a Collector, specify `http://<Collector address:port>/v1/traces`.
- `${MACKEREL_APIKEY}` specifies the Mackerel API key. Define an API key with Write permission from the [API key list](https://mackerel.io/my?tab=apikeys) as an environment variable in the system where the application runs.
  - It will work if you write the API key directly instead of using an environment variable.

## Viewing sent traces

Sent traces can be verified by following these steps:

1. Select "[APM](https://mackerel.io/my/apm)" from the menu<br>
2. Select the service name
  [f:id:mackerelio:20260109153548j:plain]
3. Select the "Traces" tab
  [f:id:mackerelio:20260109153629j:plain]
4. Select a trace from the trace list to view its details
  [f:id:mackerelio:20260109153349p:plain]

That’s a wrap on setting up zero-code instrumentation and sending traces of your Python applications to Mackerel.
