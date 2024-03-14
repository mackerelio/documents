---
Title: Using Value Widgets
Date: 2018-11-13T15:22:28+09:00
URL: https://mackerel.io/docs/entry/howto/dashboard/value
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10257846132669080239
---

# Value Widgets

Display the values of arbitrary metrics in numbers on your custom dashboard with Value Widgets.

[f:id:mackerelio:20231128213947p:plain]

Highlighting values according to conditions by Format rule.

[f:id:mackerelio:20231128213951p:plain]

## Creating a Value Widget

Drag &amp; drop the value widget icon to determine where to place the widget.

[f:id:mackerelio:20231211203940p:plain]

Next, select the type of metric to display in the widget. The following metrics are available.

- Host metrics
- Service metrics
- Expression[^1]
- Query[^2]

Other settings that can be made are listed below.

| Item name                     | Contents that can be set                                                                                                                                                                                                                                         |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Size of the fractional part   | How many digits to display when the metric to be displayed is a decimal (specified from 0 to 16, rounded to the nearest specified number of digits)                                                                                                              |
| Unit                          | Unit to be appended to the end of the displayed value (specified by up to 16 characters)                                                                                                                                                                         |
| Format Rule - Rule Name       | Rule name for value highlighting (specify up to 32 characters)                                                                                                                                                                                                   |
| Format Rule - Reference Value | Criterion value for highlighting or not highlighting a value                                                                                                                                                                                                     |
| Value to display              | If enabled, the value of the endpoint in the display period of the dashboard (calculated based on this endpoint in the case of moving average or linear regression) is displayed. If disabled, displays the latest posted value regardless of the display period |
| Metric trend                  | If enabled, displays the average of the relevant metric for the display period and the difference between the average and the latest value (available only if the "Value to display" setting is enabled [^3])                                                    |

[f:id:mackerelio:20231211203945p:plain]

Once you've finished selecting, complete the process by clicking the create button.

[^1]: Specifying metrics using expressions is an [experimental feature](https://mackerel.io/docs/entry/advanced/experimental-features).
[^2]: Query is currently a public beta feature.
[^3]: The Metric trend function is an experimental feature.
