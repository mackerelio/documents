---
Title: Using Query Graph
Date: 2024-02-02T16:07:16+09:00
URL: https://mackerel.io/docs/entry/howto/labeled-metrics/query-graph
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6801883189090642213
---

The query graph is a feature that displays labeled metrics posted to Mackerel in a graph using the PromQL query language. Labeled metrics are a new feature of Mackerel that conforms to the OpenTelemetry specification. OpenTelemetry is an open-source project that aims to standardize the collection of telemetry data such as metrics, logs, and traces and export them to any backend. For more information, see the OpenTelemetry documentation.

https://opentelemetry.io/docs/

For information on how to post labeled metrics to Mackerel, see [Posting labeled metrics to Mackerel](https://mackerel.io/docs/entry/howto/labeled-metrics/post-metrics).

For information on PromQL, see [Supported PromQL features](https://mackerel.io/docs/entry/howto/labeled-metrics/promql).

## Creating a query graph

You can use the query graph as a widget in a [custom dashboard](https://mackerel.io/docs/entry/howto/dashboard). In the screen for creating/editing a custom dashboard, drag and drop the graph widget icon to determine where to create the widget.

[f:id:mackerelio:20240206173545p:plain:alt=Mackerel web console screenshot. The graph widget icon is being dragged and dropped.]

When the dialog for adding a graph widget is displayed, select "Query Graph" for "Graph type". The PromQL editor will open, and you can display a list of metrics from the menu at the top.

[f:id:mackerelio:20240206170502p:plain:alt=Mackerel web console screenshot. The query graph configuration screen displays a list of metrics.]

For example, if you select `nginx.connections_current`, the graph will be displayed as follows.

[f:id:mackerelio:20240206170701p:plain:alt=Mackerel web console screenshot. The query graph configuration screen displays the selected metric `nginx.connections_current` and the graph.]

<h2 id="setting-the-legend-of-the-graph">Setting the legend of the graph</h2>

By default, the legend is set to the combination of the query and the label. You can see that the graph legend displayed by the query for `nginx.connections_current` looks like this.

- `nginx.connections_current{state="active"}`
- `nginx.connections_current{state="waiting"}`
- `nginx.connections_current{state="writing"}`
- `nginx.connections_current{state="reading"}`

[f:id:mackerelio:20240206170933p:plain:alt=Mackerel web console screenshot. The query graph configuration screen displays the selected metric `nginx.connections_current` and the graph. The mouse cursor is hovering over the graph line, and the graph legend is displayed. `nginx.connections_current{state="active"} - 8.40`, `nginx.connections_current{state="waiting"} - 6.80`, `nginx.connections_current{state="writing"} - 1.60`, `nginx.connections_current{state="reading"}` - 0.00]

If the number of labels is large, the length of the legend will also become very long, and it may be difficult to determine which label corresponds to which graph data point. By setting the legend of the graph, you can change the way the legend is displayed.

When you select "Value" from the "Legend" in the configuration screen, a list of labels corresponding to the current query will be displayed. Let's select `state` here.

[f:id:mackerelio:20240206171224p:plain:alt=Mackerel web console screenshot. The query graph configuration screen displays the graph legend configuration screen. The value select box displays `state`, and `state` is selected.]

The selected value in the select box is displayed as `{{state}}` in "Legend" "Display". This is a legend template syntax, and if the value enclosed in `{{}}` exists as a label key, it will be replaced with that value. In this case, since `state` exists as a label key, `{{state}}` will be replaced with a value such as `active`.

[f:id:mackerelio:20240206171951p:plain:alt=Mackerel web console screenshot. The query graph configuration screen displays the selected metric `nginx.connections_current` and the graph. The mouse cursor is hovering over the graph line, and the graph legend is displayed. `active - 8.40`, `waiting - 6.80`, `writing - 1.60`, `reading - 0.00`]

## Creating a value widget

You can also create a value widget using labeled metrics. Drag and drop the value widget icon to determine where to create the widget.

[f:id:mackerelio:20240206172344p:plain:alt=Mackerel web console screenshot. The value widget icon is being dragged and dropped.]

When the dialog for adding a value widget is displayed, select "Query" for "Metric type". In the value widget, you must narrow down the series to one. You can narrow down the series by using aggregation operators such as `sum` or `avg` in PromQL, or by filtering labels until the series becomes one.

In the following example, the series is narrowed down by filtering the value of the `state` label.

[f:id:mackerelio:20240206172616p:plain:alt=Mackerel web console screenshot. The value widget configuration screen displays the selected query and `nginx.connections_current{state="active"}` is written in the editor. The preview displays 7.00.]
