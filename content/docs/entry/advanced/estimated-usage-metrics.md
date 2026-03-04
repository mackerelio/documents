---
Title: Estimated Usage Metrics
Date: 2026-03-01T00:00:00+09:00
URL: https://mackerel.io/docs/entry/advanced/estimated-usage-metrics
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/17179246901360112334
---

Estimated usage metrics measure the volume of telemetry posted to Mackerel, and they are automatically recorded by the system as [labeled metrics](https://mackerel.io/docs/entry/glossary#Labeled-metrics).

Estimated usage metrics are measured at 1-minute granularity, allowing you to view graphs of telemetry posting volume in real-time through Metrics Explorer and [Custom Dashboards](https://mackerel.io/docs/entry/howto/dashboard). Since attributes that distinguish services and environments are attached, you can also filter and view by service/environment, and identify services/environments where anomalies have occurred.

Additionally, by configuring [query monitoring](https://mackerel.io/docs/entry/query-monitoring), you can become aware of unintended increases or decreases in posting volume.

Estimated usage metrics are provided free of charge to all organizations.

Disclaimer: The values in estimated usage metrics are not guaranteed to exactly match the billable usage volume in usage-based billing plans.

## List of Estimated Usage Metrics

Currently, estimated usage metrics are only provided for the [tracing feature](https://mackerel.io/docs/entry/tracing/guide/introduction).

### Common Resource Attributes for Estimated Usage Metrics

The following resource attributes are commonly attached to estimated usage metrics. They indicate that the source is Mackerel and that these are special metrics representing the state of Mackerel itself rather than user services.

| Key                 | Value Type | Value                         |
| :------------------ | :--------- | :---------------------------- |
| `service.name`      | string     | `__mackerelio.system` (fixed) |
| `service.namespace` | string     | `__mackerelio` (fixed)        |

### `__mackerelio.estimated_usage.monthly_spans`

Measures the number of trace spans posted to the [tracing feature](https://mackerel.io/docs/entry/tracing/guide/introduction). The value is recorded as a cumulative counter and is reset at the beginning of each month.

| Metric Name                                  | Instrument Type      | Unit     | Description                                                                                    |
| :------------------------------------------- | :------------------- | :------- | :--------------------------------------------------------------------------------------------- |
| `__mackerelio.estimated_usage.monthly_spans` | Asynchronous Counter | `{span}` | The cumulative number of trace spans posted to Mackerel. Reset at the beginning of each month. |

**Attributes:**

| Key                                  | Value Type | Description                                                                                      | Example Value |
| :----------------------------------- | :--------- | :----------------------------------------------------------------------------------------------- | :------------ |
| `source.service.name`                | string     | Value of your trace resource attribute `service.name`                                            | `http-server` |
| `source.service.namespace`           | string     | Value of your trace resource attribute `service.namespace`                                       | `blog-site`   |
| `source.deployment.environment.name` | string     | Value of your trace resource attribute `deployment.environment.name` or `deployment.environment` | `production`  |

## PromQL Examples

When drawing graphs of estimated usage metrics or setting up monitoring rules, you need to write PromQL queries using the [query graph feature](https://mackerel.io/docs/entry/howto/labeled-metrics/query-graph) or [query monitoring feature](https://mackerel.io/docs/entry/howto/labeled-metrics/query-graph).

Below are PromQL examples for various scenarios to serve as references for your operations.

### The cumulative monthly value of spans posted so far

```
sum(__mackerelio.estimated_usage.monthly_spans{service.name="__mackerelio.system"})
```

### The cumulative monthly value of spans posted so far, distinguished by OpenTelemetry service

```
sum by (source.service.name, source.service.namespace) (__mackerelio.estimated_usage.monthly_spans{service.name="__mackerelio.system"})
```

### The cumulative monthly value of spans posted so far, filtered by a specific OpenTelemetry service

To view the cumulative number of trace spans sent from a service called `http-server`:

```
__mackerelio.estimated_usage.monthly_spans{service.name="__mackerelio.system", source.service.name="http-server"}
```

### The number of spans posted per minute

```
irate(__mackerelio.estimated_usage.monthly_spans{service.name="__mackerelio.system"}[$__rate_interval]) * 60
```
