---
Title: Azure Integration - Functions
Date: 2020-03-12T14:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/azure/functions
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613534574809
CustomPath: integrations/azure/functions
---

Mackerel supports obtaining and monitoring metrics for <a href="https://azure.microsoft.com/en-us/services/functions/" target="_blank">Functions</a> in Azure Integration. When integrating with Azure Integration, billable targets are determined using the conversion 1 function = 1 Micro Host. In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for Azure Integration configuration methods and a list of supported Azure services.<br>
<a href="https://mackerel.io/docs/entry/integrations/azure">Azure Integration</a>

## Obtaining metrics
The metrics obtainable with Azure Integration’s Functions support are as follows. For `Metric` explanations, refer to the <a href="https://docs.microsoft.com/en-us/azure/app-service/web-sites-monitor#understand-metrics" target="_blank">Azure help page</a>.

The maximum number of metrics obtainable is 30.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
| Total App Domains                         | TotalAppDomains<br>TotalAppDomainsUnloaded| azure.functions.total_app_domains.loaded<br>azure.functions.total_app_domains.unloaded| integer   | Average |
| Thread Count                              | Threads                    | azure.functions.threads.threads                         | integer   | Average |
| Requests In Application Queue             | RequestsInApplicationQueue | azure.functions.requests_in_application_queue.requests  | integer   | Average |
| Requests                                  | Requests                   | azure.functions.requests.requests                       | integer   | Total   |
| Private Bytes                             | PrivateBytes               | azure.functions.private_bytes.bytes                     | bytes     | Average |
| Memory working set                        | MemoryWorkingSet| azure.functions.memory_working_set.current| bytes     | Average |
| IO Read/Write/Other Operations Per Second | IoOtherOperationsPerSecond<br>IoReadOperationsPerSecond<br>IoWriteOperationsPerSecond| azure.functions.io_iops.other<br>azure.functions.io_iops.read<br>azure.functions.io_iops.write| iops      | Total   |
| IO Read/Write/Other Bytes Per Second      | IoOtherBytesPerSecond<br>IoReadBytesPerSecond<br> IoWriteBytesPerSecond| azure.functions.io.other<br>azure.functions.io.read<br>azure.functions.io.write| bytes/sec | Total   |
| Http StatusCode                           | Http101<br>Http2xx<br>Http3xx<br>Http4xx<br>Http5xx|azure.functions.http_statuscode.http_101<br>azure.functions.http_statuscode.http_2xx<br>azure.functions.http_statuscode.http_3xx<br>azure.functions.http_statuscode.http_4xx<br>azure.functions.http_statuscode.http_server_errors | integer   | Total   |
| Health Check Status                       | HealthCheckStatus          | azure.functions.health_check_status.health_check_status | integer   | Average |
| Handle Count                              | Handles                    | azure.functions.handles.handles                         | integer   | Average |
| Gen Garbage Collections                   | Gen0Collections<br>Gen1Collections<br>Gen2Collections| azure.functions.gen_collections.gen_0_collections<br>azure.functions.gen_collections.gen_1_collections<br>azure.functions.gen_collections.gen_2_collections| integer   | Total   |
| Data In/Out                               | BytesReceived<br>BytesSent | azure.functions.data.in<br>azure.functions.data.out| bytes     | Total   |
| Current Assemblies                        | CurrentAssemblies          | azure.functions.current_assemblies.current_assemblies   | integer   | Average |
| Connections                               | AppConnections             | azure.functions.connections.connections                 | integer   | Average |
| Average Response Time                     | AverageResponseTime        | azure.functions.average_response_time.seconds           | float     | Average |
| Function Execution Count                  | FunctionExecutionCount     | azure.functions.function_execution_count.count      | interger  | Total   |
| Function Execution Units                  | FunctionExecutionUnits     | azure.functions.function_execution_units.units      | float     | Total   |
