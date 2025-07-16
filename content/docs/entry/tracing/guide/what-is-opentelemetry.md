---
Title: Tracing - What is OpenTelemetry
Date: 2025-07-11T16:52:08+09:00
URL: https://mackerel.io/docs/entry/tracing/guide/what-is-opentelemetry
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398507902842
---

This page provides an overview of OpenTelemetry.

[:contents]

## About OpenTelemetry

OpenTelemetry is a collective term for a set of tools that collect and send information of metrics, logs, and traces.

It includes SDKs and protocols.

For example, to use Mackerel's tracing feature, you need the following:

* **SDK** (creates data for each language and framework)
* **Collector** (sends data externally, e.g., to Mackerel)

## About SDK

OpenTelemetry can collect information from within applications.

Therefore, SDKs tailored to each language and framework have been created.

By using SDKs, you can see what operations were performed on requests, as shown in the figure below.

<figure class="figure-image figure-image-fotolife" title="https://opentelemetry.io/docs/concepts/observability-primer/">[f:id:mackerelio:20250305172837p:plain]<figcaption>From https://opentelemetry.io/docs/concepts/observability-primer/</figcaption></figure>

In addition to official SDKs for major languages, there are also SDKs created by volunteers. 
For example, the following frameworks are supported:

* [Ruby on Rails](https://rubygems.org/gems/opentelemetry-instrumentation-rails) (ruby)
* [Django](https://pypi.org/project/opentelemetry-instrumentation-django/) (Python)
* [Gin](https://pkg.go.dev/go.opentelemetry.io/contrib/instrumentation/github.com/gin-gonic/gin/otelgin) (Go)
* [Express](https://www.npmjs.com/package/@opentelemetry/instrumentation-express) (Node.js)

There are many tools that support OpenTelemetry, so you can record not only framework-specific information but also the following operations:

* Executed SQL
* Communication with AWS
* Jobs processed in a queue

You can search for supported tools from the page below. You will be surprised by the number of tools available!

[https://opentelemetry.io/ecosystem/registry/?s=&component=instrumentation&language=all:embed:cite]

### Instrumentation with SDK

For information on how to instrument with SDK, refer to the following page.

[Installation and Implementation](https://mackerel.io/docs/entry/tracing/installations)


## About Collector

Collector (OpenTelemetry Collector) is a tool that aggregates data sent from the SDK and sends it externally. It can be used as an agent or gateway.

By setting the Collector's data transmission destination to the endpoint provided by Mackerel, you can send data to Mackerel.

For detailed information about Collector, please refer to the following page.

* [Official documentation](https://opentelemetry.io/docs/collector/)
* [GitHub](https://github.com/open-telemetry/opentelemetry-collector)

### How to install Collector

For instructions on how to install Collector, please refer to the following page.

[Introducing OpenTelemetry Collector](https://mackerel.io/docs/entry/tracing/installations/opentelemetry-collector)

<h3 id="using-collector-or-not">Deciding whether to use Collector</h3>

When you use Mackerel's tracing feature, you can send data directly from the SDK to Mackerel without using the Collector.

Which option is more suitable depends on the load conditions and language, but in general, there are the following trade-offs.

* Via Collector: The communication between the SDK and Collector, and between the Collector and Mackerel, happens asynchronously and independently, which improves stability.
* Sending data directly from the SDK: Implementation is simple since only the SDK is used.

If you decide to use Collector, please also refer to the following page.

[Introducing OpenTelemetry Collector](https://mackerel.io/docs/entry/tracing/installations/opentelemetry-collector)
