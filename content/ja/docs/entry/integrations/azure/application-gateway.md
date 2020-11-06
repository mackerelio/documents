---
Title: Azureインテグレーション - Application Gateway
Date: 2020-08-21T09:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/azure/application-gateway
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613649525332
CustomPath: integrations/azure/application-gateway
---

MackerelはAzureインテグレーションにて<a href="https://azure.microsoft.com/ja-jp/services/application-gateway/" target="_blank">Application Gateway</a>のメトリック取得や監視に対応しています。課金対象として 1リソース = 1マイクロホスト と換算します。

Azureインテグレーションの設定方法や対応Azureサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/azure">Azureインテグレーション</a>

## 取得メトリック
AzureインテグレーションのApplication Gateway対応で取得できるメトリックはレベルによって異なります。
各レベルで取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://docs.microsoft.com/ja-jp/azure/application-gateway/application-gateway-metrics" target="_blank">Azureのドキュメント</a>をご確認ください。

### Standard
最大で 13 個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
|:---|:---|:---|:---|:---|
|CPU Utilization|CpuUtilization|azure.application_gateway.cpu_utilization.percent|percentage|Average|
|Current Connections|CurrentConnections|azure.application_gateway.current_connections.count|integer|Total|
|Failed Requests|FailedRequests|azure.application_gateway.failed_requests.#.count|integer|Total|
|Healthy/Unhealthy Host Count|HealthyHostCount<br>UnhealthyHostCount|azure.application_gateway.healthy_unhealthy_host_count.#.healthy<br>azure.application_gateway.healthy_unhealthy_host_count.#.unhealthy|float|Average|
|Requests per minute per Healthy Host|AvgRequestCountPerHealthyHost|azure.application_gateway.avg_request_count_per_healthy_host.#.count|float|Average|
|Response Status|ResponseStatus|azure.application_gateway.response_status.1xx<br>azure.application_gateway.response_status.2xx<br>azure.application_gateway.response_status.3xx<br>azure.application_gateway.response_status.4xx<br>azure.application_gateway.response_status.5xx|integer|Total|
|Throughput|Throughput|azure.application_gateway.throughput.bytespersecond|bytes/sec|Average|
|Total Requests|TotalRequests|azure.application_gateway.total_requests.#.count|integer|Total|

### WAF
最大で 16 個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
|:---|:---|:---|:---|:---|
|CPU Utilization|CpuUtilization|azure.application_gateway.cpu_utilization.percent|percentage|Average|
|Current Connections|CurrentConnections|azure.application_gateway.current_connections.count|integer|Total|
|Failed Requests|FailedRequests|azure.application_gateway.failed_requests.#.count|integer|Total|
|Healthy/Unhealthy Host Count|HealthyHostCount<br>UnhealthyHostCount|azure.application_gateway.healthy_unhealthy_host_count.#.healthy<br>azure.application_gateway.healthy_unhealthy_host_count.#.unhealthy|float|Average|
|Requests per minute per Healthy Host|AvgRequestCountPerHealthyHost|azure.application_gateway.avg_request_count_per_healthy_host.#.count|float|Average|
|Response Status|ResponseStatus|azure.application_gateway.response_status.1xx<br>azure.application_gateway.response_status.2xx<br>azure.application_gateway.response_status.3xx<br>azure.application_gateway.response_status.4xx<br>azure.application_gateway.response_status.5xx|integer|Total|
|Throughput|Throughput|azure.application_gateway.throughput.bytespersecond|bytes/sec|Average|
|Total Requests|TotalRequests|azure.application_gateway.total_requests.#.count|integer|Total|
|Web Application Firewall v1 Blocked Requests Count|BlockedReqCount|azure.application_gateway.blocked_req_count.count|integer|Total|
|Web Application Firewall v1 Blocked Requests Rule Distribution|BlockedCount|azure.application_gateway.blocked_count.#.count|integer|Total|
|Web Application Firewall v1 Total Rule Distribution|MatchedCount|azure.application_gateway.matched_count.#.count|integer|Total|

### Standard V2
最大で 25 個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
|:---|:---|:---|:---|:---|
|Backend Connect Time|BackendConnectTime|azure.application_gateway.backend_connect_time.#.milliseconds|float|Average|
|Backend First Byte Response Time|BackendFirstByteResponseTime|azure.application_gateway.backend_first_byte_response_time.#.milliseconds|float|Average|
|Backend Last Byte Response Time|BackendLastByteResponseTime|azure.application_gateway.backend_last_byte_response_time.#.milliseconds|float|Average|
|Backend Response Status|BackendResponseStatus|azure.application_gateway.backend_response_status.#.1xx<br>azure.application_gateway.backend_response_status.#.2xx<br>azure.application_gateway.backend_response_status.#.3xx<br>azure.application_gateway.backend_response_status.#.4xx<br>azure.application_gateway.backend_response_status.#.5xx|integer|Total|
|Current Capacity Units|CapacityUnits|azure.application_gateway.capacity_units.count|float|Average|
|Current Compute Units|ComputeUnits|azure.application_gateway.compute_units.count|float|Average|
|Current Connections|CurrentConnections|azure.application_gateway.current_connections.count|integer|Total|
|Estimated Billed Capacity Units|EstimatedBilledCapacityUnits|azure.application_gateway.estimated_billed_capacity_units.count|float|Average|
|Failed Requests|FailedRequests|azure.application_gateway.failed_requests.#.count|integer|Total|
|Fixed Billable Capacity Units|FixedBillableCapacityUnits|azure.application_gateway.fixed_billable_capacity_units.count|float|Average|
|Healthy/Unhealthy Host Count|HealthyHostCount<br>UnhealthyHostCount|azure.application_gateway.healthy_unhealthy_host_count.#.healthy<br>azure.application_gateway.healthy_unhealthy_host_count.#.unhealthy|float|Average|
|New connections per second|NewConnectionsPerSecond|azure.application_gateway.new_connections_per_second.countpersecond|float|Average|
|Requests per minute per Healthy Host|AvgRequestCountPerHealthyHost|azure.application_gateway.avg_request_count_per_healthy_host.#.count|float|Average|
|Response Status|ResponseStatus|azure.application_gateway.response_status.1xx<br>azure.application_gateway.response_status.2xx<br>azure.application_gateway.response_status.3xx<br>azure.application_gateway.response_status.4xx<br>azure.application_gateway.response_status.5xx|integer|Total|
|Throughput|Throughput|azure.application_gateway.throughput.bytespersecond|bytes/sec|Average|
|Total Requests|TotalRequests|azure.application_gateway.total_requests.#.count|integer|Total|

### WAF V2
最大で 25 個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
|:---|:---|:---|:---|:---|
|Backend Connect Time|BackendConnectTime|azure.application_gateway.backend_connect_time.#.milliseconds|float|Average|
|Backend First Byte Response Time|BackendFirstByteResponseTime|azure.application_gateway.backend_first_byte_response_time.#.milliseconds|float|Average|
|Backend Last Byte Response Time|BackendLastByteResponseTime|azure.application_gateway.backend_last_byte_response_time.#.milliseconds|float|Average|
|Backend Response Status|BackendResponseStatus|azure.application_gateway.backend_response_status.#.1xx<br>azure.application_gateway.backend_response_status.#.2xx<br>azure.application_gateway.backend_response_status.#.3xx<br>azure.application_gateway.backend_response_status.#.4xx<br>azure.application_gateway.backend_response_status.#.5xx|integer|Total|
|Current Capacity Units|CapacityUnits|azure.application_gateway.capacity_units.count|float|Average|
|Current Compute Units|ComputeUnits|azure.application_gateway.compute_units.count|float|Average|
|Current Connections|CurrentConnections|azure.application_gateway.current_connections.count|integer|Total|
|Estimated Billed Capacity Units|EstimatedBilledCapacityUnits|azure.application_gateway.estimated_billed_capacity_units.count|float|Average|
|Failed Requests|FailedRequests|azure.application_gateway.failed_requests.#.count|integer|Total|
|Fixed Billable Capacity Units|FixedBillableCapacityUnits|azure.application_gateway.fixed_billable_capacity_units.count|float|Average|
|Healthy/Unhealthy Host Count|HealthyHostCount<br>UnhealthyHostCount|azure.application_gateway.healthy_unhealthy_host_count.#.healthy<br>azure.application_gateway.healthy_unhealthy_host_count.#.unhealthy|float|Average|
|New connections per second|NewConnectionsPerSecond|azure.application_gateway.new_connections_per_second.countpersecond|float|Average|
|Requests per minute per Healthy Host|AvgRequestCountPerHealthyHost|azure.application_gateway.avg_request_count_per_healthy_host.#.count|float|Average|
|Response Status|ResponseStatus|azure.application_gateway.response_status.1xx<br>azure.application_gateway.response_status.2xx<br>azure.application_gateway.response_status.3xx<br>azure.application_gateway.response_status.4xx<br>azure.application_gateway.response_status.5xx|integer|Total|
|Throughput|Throughput|azure.application_gateway.throughput.bytespersecond|bytes/sec|Average|
|Total Requests|TotalRequests|azure.application_gateway.total_requests.#.count|integer|Total|

Mackerel上のメトリック名の「#」には、以下のいずれかが格納されます。

  - BackendServer<br>
  バックエンドサーバのIPアドレスとポート番号を示します。<br>
  IPアドレスとポート番号はハイフン区切りで表示されます。<br>
  例えば、`127.0.0.1:8080` の場合は、`127-0-0-1-8080` となります。
    - 該当グラフ
      - Backend Connect Time
      - Backend First Byte Response Time
      - Backend Last Byte Response Time
      - Backend Response Status

  - BackendSettingsPool<br>
  バックエンドターゲットのバックエンドプールとHTTP設定を示します。<br>
  バックエンドプールとHTTP設定はハイフン区切りで表示されます。<br>
  例えば、バックエンドプールが `backendpool` で、HTTP設定が `httpsetting` の場合は、`backendpool-httpsetting` となります。
    - 該当グラフ
      - Failed Requests
      - Healthy/Unhealthy Host Count
      - Requests per minute per Healthy Host
      - Total Requests

  - RuleGroup<br>
  WAFで適用されているCRS(Core Rule Set)の規則グループを示します。
    - 該当グラフ
      - Web Application Firewall v1 Blocked Requests Rule Distribution
      - Web Application Firewall v1 Total Rule Distribution
