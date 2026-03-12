---
Title: Azure Integration - Front Door
URL: https://mackerel.io/docs/entry/integrations/azure/front-door
---

Mackerel supports obtaining and monitoring <a href="https://azure.microsoft.com/en-us/products/frontdoor" target="_blank">Front Door</a> (Standard/Premium tier) metrics in Azure Integration. When integrating with Azure Integration, billable targets are determined using the conversion 1 CDN profile = 1 Micro Host. In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for Azure Integration configuration methods and a list of supported Azure services.<br>
<a href="https://mackerel.io/docs/entry/integrations/azure">Azure Integration</a>

## Obtaining metrics
The metrics obtainable with Azure Integration’s Front Door support are as follows. For `Metric` explanations, refer to the <a href="https://learn.microsoft.com/en-us/azure/azure-monitor/reference/supported-metrics/microsoft-cdn-profiles-metrics" target="_blank">Azure help page</a>.

The maximum number of metrics obtainable is 1 + (number of origins) + 19 × (number of endpoints) + (number of WAF policies) × (number of WAF actions)

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
| Origin Latency                               | OriginLatency                               | azure.frontdoor.origin_latency.#.milliseconds                               | float      | Average |
| Total Latency                                | TotalLatency                                | azure.frontdoor.total_latency.#.milliseconds                                | float      | Average |
| Origin Health                                | OriginHealthPercentage                       | azure.frontdoor.origin_health.#.percent                                     | percentage | Average |
| Byte Hit Ratio                               | ByteHitRatio                                | azure.frontdoor.byte_hit_ratio.#.percent                                    | percentage | Average |
| Percentage of 4XX                            | Percentage4XX                               | azure.frontdoor.percentage_4xx.#.percent                                    | percentage | Average |
| Percentage of 5XX                            | Percentage5XX                               | azure.frontdoor.percentage_5xx.#.percent                                    | percentage | Average |
| Average WebSocket Connection Duration        | AverageWebSocketConnectionDuration           | azure.frontdoor.average_websocket_connection_duration.#.milliseconds        | float      | Average |
| Active WebSocket Connections                 | ActiveWebSocketConnections                   | azure.frontdoor.active_websocket_connections.connections                    | integer    | Total   |
| Origin Request Count                         | OriginRequestCount                           | azure.frontdoor.origin_request_count.#.*                                    | integer    | Total   |
| Request Count                                | RequestCount                                | azure.frontdoor.request_count.#.*                                           | integer    | Total   |
| Request Size                                 | RequestSize                                 | azure.frontdoor.request_size.#.size                                         | bytes      | Total   |
| Response Size                                | ResponseSize                                | azure.frontdoor.response_size.#.size                                        | bytes      | Total   |
| Web Application Firewall CAPTCHA Request Count | WebApplicationFirewallCaptchaRequestCount    | azure.frontdoor.web_application_firewall_captcha_request_count.#.*          | integer    | Total   |
| Web Application Firewall JS Challenge Request Count | WebApplicationFirewallJsRequestCount           | azure.frontdoor.web_application_firewall_js_request_count.#.*               | integer    | Total   |
| Web Application Firewall Request Count       | WebApplicationFirewallRequestCount           | azure.frontdoor.web_application_firewall_request_count.#.*                  | integer    | Total   |
| WebSocket Connections                        | WebSocketConnections                        | azure.frontdoor.websocket_connections.#.connections                         | integer    | Total   |
