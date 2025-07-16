---
Title: Tracing - To resolve issues quickly
Date: 2025-07-11T14:56:17+09:00
URL: https://mackerel.io/docs/entry/tracing/features/solve-issues
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398507809956
---

Using Mackerel's tracing feature, summarize the issues that have occurred and analyze their causes.

[:contents]

### Identify the cause of the issue

By using distributed tracing with OpenTelemetry, you can visualize the state when an error occurs.

<figure class="figure-image figure-image-fotolife" title="Issues screen / Trace">[f:id:mackerelio:20250305173457p:plain]<figcaption>Issues screen / Trace</figcaption></figure>

You can also identify other issues that occurred at the same time, allowing you to investigate the cause of cross-service issues.

<figure class="figure-image figure-image-fotolife" title="Issues screen / Concurrent issues">[f:id:mackerelio:20250305173511p:plain]<figcaption>Issues screen / Concurrent issues</figcaption></figure>

### Resolving issues

You can get automatic suggestions for the cause by comparing the trace of the issue with other traces.

<figure class="figure-image figure-image-fotolife" title="Issues screen / Estimated reasons">[f:id:mackerelio:20250305173522p:plain]<figcaption>Issues screen / Estimated reasons</figcaption></figure>

For example, it investigates the following issues.

* “The problem occurs only when user_id is 1.”
* “The database is accessed multiple times (N+1 problem occurs).”



You can also view the distribution of parameters set in Span as shown in the following screen.

<figure class="figure-image figure-image-fotolife" title="Issues screen / Aggregate">[f:id:mackerelio:20250305173540p:plain]<figcaption>Issues screen / Aggregate</figcaption></figure>
