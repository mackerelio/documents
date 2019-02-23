---
Title: Using Graph Widgets
Date: 2018-11-13T15:21:59+09:00
URL: https://mackerel.io/docs/entry/howto/dashboard/graph
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10257846132669079995
---

# Graph Widgets
Display graphs in custom dashboards with the use of Graph Widgets.

[f:id:mackerelio:20181113152347p:plain]

## Creating a Graph Widget
Drag &amp; drop the graph widget icon to determine where to place the widget.

[f:id:mackerelio:20181113152413p:plain]

Then select the type of graph to display in the widget. Choose from following graphs.

- Host graph
- Role graph
- Service graph
- Expression graph[^1]

[f:id:mackerelio:20181113152442p:plain]

Next, select the graph display range. Choose from the following.

<dl>
    <dt>Enable changes</dt>
    <dd>After editing the dashboard, changes can be made to the graph display range using the controller at the top of the screen or by selecting an area of the graph.</dd>
    <dt>Specify a range relative to the current time</dt>
    <dd>Specify the length and the end of the range relative to the current time. For example, use this when you want to display a graph of the same range one year ago.</dd>
    <dt>Specify a fixed range</dt>
    <dd>Always displays a graph of the specified range.</dd>
</dl>

After your selections are complete, create the widget by clicking the Create button.

[^1]: Specifying graphs using expressions is an [experimental feature](https://mackerel.io/docs/entry/advanced/experimental-features).
