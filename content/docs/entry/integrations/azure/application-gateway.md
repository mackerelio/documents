---
Title: Azure Integration - Application Gateway
Date: 2020-08-21T09:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/azure/application-gateway
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613649526420
CustomPath: integrations/azure/application-gateway
---

Mackerel supports obtaining and monitoring <a href="https://azure.microsoft.com/en-us/services/application-gateway/" target="_blank">Application Gateway</a> metrics in Azure Integration. When integrating with Azure Integration, billable targets are determined using the conversion 1 Resource = 2 Micro Hosts.

Please refer to the following page for Azure Integration configuration methods and a list of supported Azure services.<br>
<a href="https://mackerel.io/docs/entry/integrations/azure">Azure Integration</a>

## Obtaining metrics
The metrics available with Azure Integration Application Gateway support vary by pricing tier. The metrics that can be obtained at each tier are as follows. For `Metric` explanations, please refer to the <a href="https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-metrics" target="_blank">Azure documentation</a>.

### Standard
The maximum number of metrics obtainable is 13.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
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
The maximum number of metrics obtainable is 16.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
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
The maximum number of metrics obtainable is 33.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|Application Gateway Total Time|ApplicationGatewayTotalTime|azure.application_gateway.application_gateway_total_time.#.milliseconds|float|Average|
|Backend Connect Time|BackendConnectTime|azure.application_gateway.backend_connect_time.#.milliseconds|float|Average|
|Backend First Byte Response Time|BackendFirstByteResponseTime|azure.application_gateway.backend_first_byte_response_time.#.milliseconds|float|Average|
|Backend Last Byte Response Time|BackendLastByteResponseTime|azure.application_gateway.backend_last_byte_response_time.#.milliseconds|float|Average|
|Backend Response Status|BackendResponseStatus|azure.application_gateway.backend_response_status.#.1xx<br>azure.application_gateway.backend_response_status.#.2xx<br>azure.application_gateway.backend_response_status.#.3xx<br>azure.application_gateway.backend_response_status.#.4xx<br>azure.application_gateway.backend_response_status.#.5xx|integer|Total|
|Bytes Sent/Received|BytesSent<br>BytesReceived|azure.application_gateway.bytes_sent_received.#.sent<br>azure.application_gateway.bytes_sent_received.#.received|bytes|Total|
|Client RTT|ClientRtt|azure.application_gateway.client_rtt.#.milliseconds|float|Average|
|Client TLS Protocol|TlsProtocol|azure.application_gateway.tls_protocol.#.nontls<br>azure.application_gateway.tls_protocol.#.tlsv1<br>azure.application_gateway.tls_protocol.#.tlsv1_1<br>azure.application_gateway.tls_protocol.#.tlsv1_2|integer|Total|
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
The maximum number of metrics obtainable is 33.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|Application Gateway Total Time|ApplicationGatewayTotalTime|azure.application_gateway.application_gateway_total_time.#.milliseconds|float|Average|
|Backend Connect Time|BackendConnectTime|azure.application_gateway.backend_connect_time.#.milliseconds|float|Average|
|Backend First Byte Response Time|BackendFirstByteResponseTime|azure.application_gateway.backend_first_byte_response_time.#.milliseconds|float|Average|
|Backend Last Byte Response Time|BackendLastByteResponseTime|azure.application_gateway.backend_last_byte_response_time.#.milliseconds|float|Average|
|Backend Response Status|BackendResponseStatus|azure.application_gateway.backend_response_status.#.1xx<br>azure.application_gateway.backend_response_status.#.2xx<br>azure.application_gateway.backend_response_status.#.3xx<br>azure.application_gateway.backend_response_status.#.4xx<br>azure.application_gateway.backend_response_status.#.5xx|integer|Total|
|Bytes Sent/Received|BytesSent<br>BytesReceived|azure.application_gateway.bytes_sent_received.#.sent<br>azure.application_gateway.bytes_sent_received.#.received|bytes|Total|
|Client RTT|ClientRtt|azure.application_gateway.client_rtt.#.milliseconds|float|Average|
|Client TLS Protocol|TlsProtocol|azure.application_gateway.tls_protocol.#.nontls<br>azure.application_gateway.tls_protocol.#.tlsv1<br>azure.application_gateway.tls_protocol.#.tlsv1_1<br>azure.application_gateway.tls_protocol.#.tlsv1_2|integer|Total|
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

The "#" in 'Metric name in Mackerel' holds one of the following.

  - BackendServer<br>
  Indicates the IP address and port number for the backend server.<br>
  IP addresses and port numbers are displayed separated by hyphens.<br>
  For example, `127.0.0.1:8080` would be displayed as `127-0-0-1-8080`.
    - Corresponding graphs
      - Backend Connect Time
      - Backend First Byte Response Time
      - Backend Last Byte Response Time
      - Backend Response Status

  - BackendSettingsPool<br>
  Indicates the backend pool and HTTP setting for the backend target.<br>
  The backend pool and HTTP setting are displayed separated by a hyphen.<br>
  For example, if the backend pool is `backendpool` and the HTTP setting is `httpsetting`, then it will be displayed as `backendpool-httpsetting`.
    - Corresponding graphs
      - Failed Requests
      - Healthy/Unhealthy Host Count
      - Requests per minute per Healthy Host
      - Total Requests

  - Listener<br>
  Indicates the listener in the target Application Gateway.
    - Corresponding graphs
      - Application Gateway Total Time
      - Bytes Sent/Received
      - Client RTT
      - Client TLS Protocol

  - RuleGroup<br>
  Indicates the CRS (Core Rule Set) rule group applied by WAF.
    - Corresponding graphs
      - Web Application Firewall v1 Blocked Requests Rule Distribution
      - Web Application Firewall v1 Total Rule Distribution
