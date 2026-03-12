---
Title: Azureインテグレーション - Front Door
Date: 2026-03-12T17:10:26+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/azure/front-door
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17179246901364099106
CustomPath: integrations/azure/front-door
---

MackerelはAzureインテグレーションにて<a href="https://azure.microsoft.com/ja-jp/products/frontdoor" target="_blank">Front Door</a>（Standard/Premiumレベル）のメトリック取得や監視に対応しています。Azureインテグレーションで連携をおこなった場合、課金対象として 1プロファイル = 1マイクロホスト と換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

Azureインテグレーションの設定方法や対応Azureサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/azure">Azureインテグレーション</a>

## 取得メトリック
AzureインテグレーションのFront Door対応で取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://learn.microsoft.com/ja-jp/azure/azure-monitor/reference/supported-metrics/microsoft-cdn-profiles-metrics" target="_blank">Azureのドキュメント</a>をご確認ください。

最大で 1 + (オリジン数) + 19 × (エンドポイント数) + (WAFポリシー数) × (WAFアクション数) 個メトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
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
| Web Application Firewall Request Count       | WebApplicationFirewallRequestCount           | azure.frontdoor.web_application_firewall_request_count.#.count              | integer    | Total   |
| WebSocket Connections                        | WebSocketConnections                        | azure.frontdoor.websocket_connections.#.connections                         | integer    | Total   |
