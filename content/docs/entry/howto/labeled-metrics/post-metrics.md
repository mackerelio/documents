---
Title: Posting labeled metrics to Mackerel
Date: 2024-02-02T16:07:16+09:00
URL: https://mackerel.io/docs/entry/howto/labeled-metrics/post-metrics
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6801883189090642139
---

Mackerel has the capability to receive a metric specified in [OpenTelemetry](https://opentelemetry.io/), which we refer to as a "labeled metric". You can post metrics to Mackerel using the OpenTelemetry ecosystem.**This feature is a paid option. It is not available with the Free plan.**

Because OpenTelemetry provides a vendor-agnostic framework, you can switch the monitoring backend to Mackerel without changing the code if the OpenTelemetry library is already incorporated into your application or service.

Of the three telemetry data (metric, log, and trace) listed by OpenTelemetry, Mackerel supports only metric currently.

## Supported metric types in Mackerel

Mackerel supports the following types of metrics:

- Counter
- Gauge
- Histogram (partially supported)

### About histogram

Mackerel does not store all the buckets of a histogram. Instead, it stores only the following representative values:

- `count`: The number of samples in the histogram
- `sum`: The sum of the values
- `min`: The minimum value
- `max`: The maximum value
- `p99`: The 99th percentile value
- `p95`: The 95th percentile value
- `p90`: The 90th percentile value

You can obtain these representative values by adding a suffix to the metric name, such as `.p99`. For example, if you have a histogram metric named `http.server.request.duration`, you can obtain the following metrics:

- `http.server.request.duration.p99`
- `http.server.request.duration.p95`
- `http.server.request.duration.p90`
- `http.server.request.duration.max`
- `http.server.request.duration.min`
- `http.server.request.duration.sum`
- `http.server.request.duration.count`

If the result of the representative value calculation is one of the following, the metric is not obtained:

- `null`
- `inf`
- `-inf`
- `NaN`

For example, if the 99th percentile value of `http.server.request.duration` is `inf`, the metric `http.server.request.duration.p99` does not exist.

In addition, depending on the configuration of the instrumentation, you may not be able to obtain the following representative values:

- `max`
- `min`

Because only the representative values are stored, you cannot use the `histogram_` series functions in PromQL. For example, you cannot use the `histogram_quantile` function to calculate an arbitrary percentile.

OpenTelemetry has two specifications for histograms: Explicit Histogram and Exponential Histogram. However, there is no difference in how they appear in Mackerel.

## Instrumenting metrics

First, install the OpenTelemetry SDKs into your application or service and instrument the metrics. OpenTelemetry provides SDKs for many languages. For details, see [OpenTelemetry Language APIs & SDKs](https://opentelemetry.io/docs/languages/).

OpenTelemetry provides automatic instrumentation for some programming languages. The automatic instrumentation enable to instrument your application without touching its source code.

In addition to using the SDK, you can collect metrics using the [receiver](https://opentelemetry.io/docs/collector/configuration/#receivers) of the [OpenTelemetry Collector](https://opentelemetry.io/docs/collector/). By using the receiver, you can actively collect host metrics or receive metrics sent by the SDK.

## Posting labeled metrics

To send metrics to Mackerel, you must use the [OpenTelemetry Protocol (OTLP)/gRPC](https://opentelemetry.io/docs/specs/otlp/). The endpoint is as follows:

```
otlp.mackerelio.com:4317
```

You also need to include the API key in the `Mackerel-Api-Key` header. You can obtain the API key from the [API Keys tab of the dashboard](https://mackerel.io/my?tab=apikeys) in Mackerel. Be sure to use an API key with Write permission.

## Example configuration of the OpenTelemetry Collector

The following is an example configuration of the OpenTelemetry Collector. Set the API key in the environment variable `MACKEREL_APIKEY`.

```yaml
receivers:
  hostmetrics:
    scrapers:
      cpu:
      disk:
      filesystem:
      load:
      memory:
      network:
      paging:

processors:
  batch:
    timeout: 1m

# Exporter to send metrics to Mackerel
exporters:
  otlp/mackerel:
    endpoint: otlp.mackerelio.com:4317
    headers:
      Mackerel-Api-Key: ${env:MACKEREL_APIKEY}

service:
  pipelines:
    metrics:
      receivers: [hostmetrics]
      processors: [batch]
      exporters: [otlp/mackerel]
```

In the example provided, metrics similar to those collected by the traditional [mackerel-agent](https://mackerel.io/docs/entry/howto/install-agent) are gathered and then sent to Mackerel. For more detailed information, see the following blog:

https://mackerel.io/blog/entry/using-open-telemetry-labeled-metrics-with-mackerel
