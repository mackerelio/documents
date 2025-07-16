---
Title: Tracing - Pricing
Date: 2025-07-11T14:51:03+09:00
URL: https://mackerel.io/docs/entry/tracing/guide/pricing-mackerel
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398507808579
---

Fees are charged based on the number of spans posted during the month from the first day to the last day of the month. For more details, please refer to [Pricing](https://en.mackerel.io/pricing) or [How usage fees are calculated
 ](https://support.mackerel.io/hc/en-us/articles/31304727432729).

[:contents]

### About Span

Span is a unit of data used in distributed tracing sent by OpenTelemetry.

A single Span represents one operation. For example, each SQL execution or incoming request results in the creation of a Span.

As shown in the figure below, each line represents one Span.

<figure class="figure-image figure-image-fotolife" title="Issues screen / Trace">[f:id:mackerelio:20250305173125p:plain]<figcaption>Issues screen / Trace</figcaption></figure>

### Estimating the number of Spans

The number of Spans varies depending on the application's architecture, so it is not easy to estimate precisely. However, for general web applications, the following estimates are possible:

* When using a monolithic REST API server, approximately 10 to 20 Spans are created per request.
* Assuming an average of 20 Spans per request, 1 million Spans are created every 50,000 requests.
* You can estimate the number of Spans by multiplying this value by the number of requests.
