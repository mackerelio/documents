---
Title: Azureインテグレーション - Functions
Date: 2020-03-12T14:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/azure/functions
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613534153943
CustomPath: integrations/azure/functions
---

MackerelはAzureインテグレーションにて<a href="https://azure.microsoft.com/ja-jp/services/functions/" target="_blank">Functions</a>のメトリック取得や監視に対応しています。Azureインテグレーションで連携をおこなった場合、課金対象として 1function = 1マイクロホスト と換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

Azureインテグレーションの設定方法や対応Azureサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/azure">Azureインテグレーション</a>

## 取得メトリック
AzureインテグレーションのFunctions対応で取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://docs.microsoft.com/ja-jp/azure/app-service/web-sites-monitor" target="_blank">Azureのドキュメント</a>をご確認ください。

最大で 30 個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
|:---|:---|:---|:---|:---|
| Total App Domains                         | TotalAppDomains<br/>TotalAppDomainsUnloaded| azure.functions.total_app_domains.loaded<br/>azure.functions.total_app_domains.unloaded| integer   | Average |
| Thread Count                              | Threads                    | azure.functions.threads.threads                         | integer   | Average |
| Requests In Application Queue             | RequestsInApplicationQueue | azure.functions.requests_in_application_queue.requests  | integer   | Average |
| Requests                                  | Requests                   | azure.functions.requests.requests                       | integer   | Total   |
| Private Bytes                             | PrivateBytes               | azure.functions.private_bytes.bytes                     | bytes     | Average |
| Memory working set                        | MemoryWorkingSet| azure.functions.memory_working_set.current| bytes     | Average |
| IO Read/Write/Other Operations Per Second | IoOtherOperationsPerSecond<br/>IoReadOperationsPerSecond<br/>IoWriteOperationsPerSecond| azure.functions.io_iops.other<br/>azure.functions.io_iops.read<br/>azure.functions.io_iops.write| iops      | Total   |
| IO Read/Write/Other Bytes Per Second      | IoOtherBytesPerSecond<br/>IoReadBytesPerSecond<br/> IoWriteBytesPerSecond| azure.functions.io.other<br/>azure.functions.io.read<br/>azure.functions.io.write| bytes/sec | Total   |
| Http StatusCode                           | Http101<br/>Http2xx<br/>Http3xx<br/>Http4xx<br/>Http5xx|azure.functions.http_statuscode.http_101<br/>azure.functions.http_statuscode.http_2xx<br/>azure.functions.http_statuscode.http_3xx<br/>azure.functions.http_statuscode.http_4xx<br/>azure.functions.http_statuscode.http_server_errors | integer   | Total   |
| Health Check Status                       | HealthCheckStatus          | azure.functions.health_check_status.health_check_status | integer   | Average |
| Handle Count                              | Handles                    | azure.functions.handles.handles                         | integer   | Average |
| Gen Garbage Collections                   | Gen0Collections<br/>Gen1Collections<br/>Gen2Collections| azure.functions.gen_collections.gen_0_collections<br/>azure.functions.gen_collections.gen_1_collections<br/>azure.functions.gen_collections.gen_2_collections| integer   | Total   |
| Data In/Out                               | BytesReceived<br/>BytesSent | azure.functions.data.in<br/>azure.functions.data.out| bytes     | Total   |
| Current Assemblies                        | CurrentAssemblies          | azure.functions.current_assemblies.current_assemblies   | integer   | Average |
| Connections                               | AppConnections             | azure.functions.connections.connections                 | integer   | Average |
| Average Response Time                     | AverageResponseTime        | azure.functions.average_response_time.seconds           | float     | Average |
| Function Execution Count                  | FunctionExecutionCount     | azure.functions.function_execution_count.count      | interger  | Total   |
| Function Execution Units                  | FunctionExecutionUnits     | azure.functions.function_execution_units.units      | float     | Total   |
