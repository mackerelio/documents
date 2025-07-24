---
Title: Tracing - Investigate HTTP server performance
Date: 2025-07-11T16:32:29+09:00
URL: https://mackerel.io/docs/entry/tracing/features/http-performance
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398507895652
---

The HTTP Server tab of the APM service screen displays a list of statistical information about the performance of HTTP requests included in the application trace spans.

<figure class="figure-image figure-image-fotolife" title="HTTP Server Tab">[f:id:mackerelio:20250724141857p:plain]<figcaption>HTTP Server Tab</figcaption></figure>


## Viewing the list of requests

The HTTP Server screen displays a list of trace spans information for received requests. Trace spans at the time of transmission (client spans) are not included in the aggregation.

The following information is displayed in each route row. Clicking on a field name other than the route allows you to switch between ascending and descending order based on that field.

| Column       | Description                                                  |
|--------------|--------------------------------------------------------------|
| Route        | Request destination                                          |
| Total        | Total response time for each route                           |
| Average      | Average response time per route                              |
| Approx P95   | 95th percentile value of response time (approximate)         |
| Error Rate   | Error occurrence rate per route                              |
| Request Count | Access count per route                                       |
| Trace        | Navigates to the list screen of traces containing this query |

- The following attributes are displayed for each route.
  - URL portion
    - One of `http.route` / `url.path` / `http.target` / `http.url`
      - Prioritized in the order listed above. The value of `http.route` has the highest priority.
      - Trace spans that do not contain any of these attributes are not displayed in the list.
  - Method section
    - Either `http.request.method` or `http.method`
      - The value of `http.request.method` takes precedence.
      - Trace spans that do not contain any of these attributes will not be displayed in the list.
- Approx P95 is an approximate value calculated by Mackerel and is not the exact 95th percentile value.
- The error rate is calculated as follows:
  - Status codes in the 500 range are treated as errors, and the error rate is calculated as `error count / total request count`.
  - Either the value of `http.response.status_code` or `http.status_code` is used for calculating the error rate.
    - The value of `http.response.status_code` takes precedence.
    - Trace spans that do not include either attribute will not be displayed in the list.
