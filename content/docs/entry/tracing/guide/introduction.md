---
Title: Tracing - Introduction
Date: 2025-07-07T10:27:53+09:00
URL: https://mackerel.io/docs/entry/tracing/guide/introduction
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398502974490
---

[:contents]

## What is Mackerel's tracing feature?

Mackerel's tracing feature allows you to discover and resolve issues that cause degraded user experience, such as errors and slow performance.

## What can you do with Mackerel's tracing feature?

Here is an overview of Mackerel's tracing feature.

### Issue management

[f:id:mackerelio:20250305172220p:plain]<figcaption>Issue List screen</figcaption></figure></p></figcaption></figure>

* Mackerel's tracing feature automatically detect errors and slow requests.

<figure class="figure-image figure-image-fotolife" title="Issue screen">[f:id:mackerelio:20250305172249p:plain]<figcaption>Issue screen</figcaption></figure>

* On the details screen of an issue, you can view stack traces, suspected causes, and other information.

### Trace Management

<figure class="figure-image figure-image-fotolife" title="Trace List Screen">[f:id:mackerelio:20250305172303p:plain]<figcaption>Trace List Screen</figcaption></figure>

* Mackerel stores traces for 14 days.

<figure class="figure-image figure-image-fotolife" title="Trace List screen">[f:id:mackerelio:20250305172333p:plain]<figcaption>Trace List screen</figcaption></figure>

* Traces are visualized by latency and time distribution.

<figure class="figure-image figure-image-fotolife" title="Trace screen">[f:id:mackerelio:20250305172406p:plain]<figcaption>Trace screen</figcaption></figure>

* Each trace contains the time it occurred and the contents of its Spans.

For more detailed information about the specific functionalities, please see below:

[https://mackerel.io/docs/entry/tracing/features:embed:cite]
