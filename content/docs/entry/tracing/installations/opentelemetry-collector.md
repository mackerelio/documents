---
Title: Tracing - Introducing OpenTelemetry Collector
Date: 2025-07-11T16:43:14+09:00
URL: https://mackerel.io/docs/entry/tracing/installations/opentelemetry-collector
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398507898568
---

Mackerel's tracing feature uses the OpenTelemetry mechanism (instrumentation) to send data.

This page explains how to send data using OpenTelemetry Collector.

[:contents]

## What is OpenTelemetry Collector?

OpenTelemetry Collector is a vendor-independent agent for OpenTelemetry.

It can be deployed as a sidecar using Docker or installed via APK or DEB.

This page explains how to install the OpenTelemetry Collector.

For detailed implementation, refer to the official documentation.

[https://opentelemetry.io/docs/collector/:embed:cite]

### Should you use Collector?

You can also send data directly from the SDK without using Collector.

Please refer to the following page when deciding which option to use.

[Deciding whether to use Collector](https://mackerel.io/docs/entry/tracing/guide/what-is-opentelemetry#using-collector-or-not)

## How to get started

To use OpenTelemetry Collector, you need to install and configure it.

### Installation

OpenTelemetry Collector is available as

* Docker
* Kubernetes
* Linux package

and more.

For example, to install using apk, use the following command:

```bash
wget https://github.com/open-telemetry/opentelemetry-collector-releases/releases/download/${VERSION}/otelcol_${VERSION}_linux_amd64.deb
sudo dpkg -i otelcol_${VERSION}_linux_amd64.deb
```

Commands for each platform are summarized below

[https://opentelemetry.io/docs/collector/quick-start/:embed:cite]

### Configuration

OpenTelemetry Collector can be configured using files and environment variables.

For example, if you are using systemd, you can configure `/etc/otelcol/config.yaml` as follows to send data to Mackerel.

```yaml
receivers:
  otlp:
    protocols:
      grpc:
      http:

processors:
  memory_limiter:
    check_interval: 1s
    limit_mib: 500
    spike_limit_mib: 100
  batch:
    # Mackerel does not accept requests larger than 6MB.
    # Therefore, set the maximum number of spans per request appropriately.
    # A span is a unit of work or operation in a trace.
    # You can express a portion of a database query execution or application processing as a span.
    # https://opentelemetry.io/docs/concepts/signals/traces/#spans
    # Mackerel allows up to 5 million spans per month per organization.
    send_batch_size: 5000
    send_batch_max_size: 5000

exporters:
  otlphttp/mackerel:
    endpoint: "https://otlp-vaxila.mackerelio.com"
    headers:
      Accept: "*/*"
      "Mackerel-Api-Key": ${env:MACKEREL_APIKEY}

extensions:
  health_check:

service:
  extensions: [health_check]
  pipelines:
    traces:
      receivers: [otlp]
      processors: [batch]
      exporters: [otlphttp/mackerel]
```

In this example, the following items are set.

* exporters
  * Use `otlphttp` for exporters.
  * Set the endpoint to Mackerel's endpoint (**`https://otlp-vaxila.mackerelio.com`**).
  * Set the headers to **`Mackerel-Api-Key`** and **`Accept`**.
    * **Mackerel-Api-Key:** Set the API key issued by Mackerel. *1
    * **Accept**: Always set to `*/*`. (This setting is required because Mackerel uses AWS Lambda internally.)
* service
  * Set the exporter you created in traces of pipelines.

With these settings, data will be sent from Collector to Mackerel.

*1: You can find the API key for sending telemetry data in the API key tab of the Mackerel dashboard. Set the API key with “Write” permission to the environment variable `MACKEREL_APIKEY`. It may take about a minute for changes to take effect after modifying the API key permissions.

<figure class="figure-image figure-image-fotolife" title="How to check the API key">[f:id:mackerelio:20250307170201p:plain]<figcaption>How to check the API key</figcaption></figure>
