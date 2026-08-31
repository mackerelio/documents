---
Title: Tracing - Introduction
Date: 2025-07-07T10:27:53+09:00
URL: https://mackerel.io/docs/entry/tracing/guide/introduction
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398502974490
---

[:contents]

## What is Mackerel's tracing feature?

Mackerel's tracing feature allows you to discover and resolve issues that cause a degraded user experience, such as errors and slow performance.

Mackerel's tracing feature retains traces for 14 days.

## What can you do with Mackerel's tracing feature?

Here is an overview of the main features of Mackerel's tracing feature.

### Trace management

On the trace list screen, you can view the latency distribution over time and the number of traces.

<figure class="figure-image figure-image-fotolife" title="Trace list screen">[f:id:mackerelio:20260828164425p:plain]<figcaption>Trace list screen</figcaption></figure>

On the trace details screen, you can view the time of occurrence and span details.

<figure class="figure-image figure-image-fotolife" title="Trace details screen">[f:id:mackerelio:20260828164402p:plain]<figcaption>Trace details screen</figcaption></figure>

### Issue management

Mackerel's tracing feature automatically detects requests where errors occurred and registers them as issues.

<figure class="figure-image figure-image-fotolife" title="Issue list screen">[f:id:mackerelio:20260828164349p:plain]<figcaption>Issue list screen</figcaption></figure>

On the issue details screen, you can view stack traces and span details.

<figure class="figure-image figure-image-fotolife" title="Issue details screen">[f:id:mackerelio:20260828164339p:plain]<figcaption>Issue details screen</figcaption></figure>

For more details on specific features, please refer to the following page:

[https://mackerel.io/docs/entry/tracing/features:embed:cite]
