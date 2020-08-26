---
Title: Azure Integration - App Service
Date: 2020-03-12T14:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/azure/app-service
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613534574510
CustomPath: integrations/azure/app-service
---

Mackerel supports obtaining and monitoring <a href="https://azure.microsoft.com/en-us/services/app-service/" target="_blank">App Service</a> metrics in Azure Integration. When integrating with Azure Integration, billable targets are determined using the conversion 1 app = 1 Micro Host. In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for Azure Integration configuration methods and a list of supported Azure services.<br>
<a href="https://mackerel.io/docs/entry/integrations/azure">Azure Integration</a>

## Obtaining metrics
The metrics obtainable with Azure Integration’s App Service support are as follows. For `Metric` explanations, refer to the <a href="https://docs.microsoft.com/en-us/azure/app-service/web-sites-monitor#understand-metrics" target="_blank">Azure help page</a>.

The maximum number of metrics obtainable is 29.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
| Total App Domains                         | TotalAppDomains<br>TotalAppDomainsUnloaded| azure.app_service.total_app_domains.loaded<br>azure.app_service.total_app_domains.unloaded| integer   | Average |
| Thread Count                              | Threads                    | azure.app_service.threads.threads                         | integer   | Average |
| Requests In Application Queue             | RequestsInApplicationQueue | azure.app_service.requests_in_application_queue.requests  | integer   | Average |
| Requests                                  | Requests                   | azure.app_service.requests.requests                       | integer   | Total   |
| Private Bytes                             | PrivateBytes               | azure.app_service.private_bytes.bytes                     | bytes     | Average |
| Memory working set                        | MemoryWorkingSet| azure.app_service.memory_working_set.current| bytes     | Average |
| IO Read/Write/Other Operations Per Second | IoOtherOperationsPerSecond<br>IoReadOperationsPerSecond<br>IoWriteOperationsPerSecond| azure.app_service.io_iops.other<br>azure.app_service.io_iops.read<br>azure.app_service.io_iops.write| iops      | Total   |
| IO Read/Write/Other Bytes Per Second      | IoOtherBytesPerSecond<br>IoReadBytesPerSecond<br> IoWriteBytesPerSecond| azure.app_service.io.other<br>azure.app_service.io.read<br>azure.app_service.io.write| bytes/sec | Total   |
| Http StatusCode                           | Http101<br>Http2xx<br>Http3xx<br>Http4xx<br>Http5xx|azure.app_service.http_statuscode.http_101<br>azure.app_service.http_statuscode.http_2xx<br>azure.app_service.http_statuscode.http_3xx<br>azure.app_service.http_statuscode.http_4xx<br>azure.app_service.http_statuscode.http_server_errors | integer   | Total   |
| Health Check Status                       | HealthCheckStatus          | azure.app_service.health_check_status.health_check_status | integer   | Average |
| Handle Count                              | Handles                    | azure.app_service.handles.handles                         | integer   | Average |
| Gen Garbage Collections                   | Gen0Collections<br>Gen1Collections<br>Gen2Collections| azure.app_service.gen_collections.gen_0_collections<br>azure.app_service.gen_collections.gen_1_collections<br>azure.app_service.gen_collections.gen_2_collections| integer   | Total   |
| Data In/Out                               | BytesReceived<br>BytesSent | azure.app_service.data.in<br>azure.app_service.data.out| bytes     | Total   |
| Current Assemblies                        | CurrentAssemblies          | azure.app_service.current_assemblies.current_assemblies   | integer   | Average |
| CPU Time                                  | CpuTime                    | azure.app_service.cpu_time.cpu_time                       | float     | Total   |
| Connections                               | AppConnections             | azure.app_service.connections.connections                 | integer   | Average |
| Average Response Time                     | AverageResponseTime        | azure.app_service.average_response_time.seconds           | float     | Average |
