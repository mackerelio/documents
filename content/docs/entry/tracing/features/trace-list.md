---
Title: Tracing - To check the traces that were sent
Date: 2025-07-11T14:54:38+09:00
URL: https://mackerel.io/docs/entry/tracing/features/trace-list
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398507809625
---

The Trace List screen displays a list of traces sent for each service within the application.

[:contents]

## Filter Conditions

The Trace List screen displays a list of traces that match the conditions specified in the “Service” filter and the ‘Conditions’ filter, such as the occurrence date and time. The trace list can be sorted by occurrence order or latency using the “Sort by” option.

<figure class="figure-image figure-image-fotolife" title="Filter">[f:id:mackerelio:20250410144820p:plain]<figcaption>Filter</figcaption></figure>

Filter conditions can be specified using simple time specifications such as “Occurred in the last 1 hour” or detailed conditions based on the following items.

| Item | Content |
|-------------------|-------------------------------------------------------------------------|
| Service | Value of `service.name` included in the trace span |
| Service namespace | Value of `service.namespace` included in the trace span |
| Deployment Environment | Value of `deployment.environment` or `deployment.environment.name` included in the trace span |
| Occurred | Date and time of trace occurrence |
| Issue | Issue ID. Alphanumeric characters at the end of the issue details URL (`tracing/issues/<id>`) |
| Trace ID | Trace ID displayed in the trace list and trace details screen |
| Span Name | `name` value for each span displayed in the trace details screen |
| Latency | Specify latency conditions as greater than, less than, or range |
| Attributes | Specify the condition for the attribute value |
| Resource | Specify the condition for the resource value |


## Latency Distribution

The latency distribution is displayed as a heat map. The horizontal axis represents the time elapsed (the further to the right, the more recent), and the vertical axis represents the latency (the higher, the longer it took).

The approximate number of traces present in the time period and latency on the heatmap is displayed as shown in the legend in the upper right corner. Hovering over the heatmap reveals the actual number. You can also drag to select a range and drill down to investigate that range.

<figure class="figure-image figure-image-fotolife" title="Latency Distribution">[f:id:mackerelio:20250410144835p:plain]<figcaption>Latency Distribution</figcaption></figure>


## Trace List

The trace list displays the following information.

| Item | Content |
|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Trace ID | Trace ID |
| Request | Value of `http.method` (request method) or `http.route` (request path). If the `deployment.environment` or `deployment.environment.name` attribute exists, its value (deployment environment) |
| Trace Occurred Time | Date and time when the trace occurred |
| Trace Latency | Total time of the trace |
| Service | Value of `service.name` (service name) or `service.namespace` (service namespace) |
| Service Occurred Time | Date and time when the span of the service used as a filter condition occurred |
| Service Latency | Total time of the span of the service used as a filter condition |

<figure class="figure-image figure-image-fotolife" title="Trace list">[f:id:mackerelio:20250410144857p:plain]<figcaption>Trace list</figcaption></figure>

### Trace Aggregation

Click the “Aggregate” button at the top right of the trace list to display the ratio of values for the span attributes in the trace list.

For example, the `http.route` attribute indicates the request path, and the `http.status_code` attribute indicates the response status code, allowing you to see which request paths are most common and the error rate of responses.

<figure class="figure-image figure-image-fotolife" title="Aggregation">[f:id:mackerelio:20250410144914p:plain]<figcaption>Aggregation</figcaption></figure>


## Trace Details Screen

Click a trace in the trace list to view its details.

The trace details screen visualizes the status of spans. The horizontal axis represents time (with newer spans to the right), and the vertical spans represent processes performed simultaneously, while the horizontal spans represent processes performed sequentially. Clicking on a span displays its attributes. You can also drag to select a range and drill down to investigate that range.

If the trace contains the `service.name` attribute or the `host.id` or `host.name` attributes, “View service metrics” link or “View host metrics” link will appear in the service or host section at the top of the screen (*). Clicking this link will take you to the Metric Explorer screen, where you can filter by attributes such as services or hosts included in the trace and narrow down the display range to the time around when the trace occurred.

※`host.id` takes precedence over `host.name`. Additionally, if the `service.namespace` attribute exists, it will also be included in the filtering conditions.

<figure class="figure-image figure-image-fotolife" title="Trace Details Screen">[f:id:mackerelio:20250410144942p:plain]<figcaption>Trace Details Screen</figcaption></figure>
