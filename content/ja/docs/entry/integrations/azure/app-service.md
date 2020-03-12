---
Title: Azureインテグレーション - App Service
Date: 2020-03-12T14:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/azure/app-service
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613533606132
CustomPath: integrations/azure/app-service
---

MackerelはAzureインテグレーションにて<a href="https://azure.microsoft.com/ja-jp/services/app-service/" target="_blank">App Service</a>のメトリック取得や監視に対応しています。Azureインテグレーションで連携をおこなった場合、課金対象として 1app = 1マイクロホスト と換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

Azureインテグレーションの設定方法や対応Azureサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/azure">Azureインテグレーション</a>

## 取得メトリック
AzureインテグレーションのApp Service対応で取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://docs.microsoft.com/ja-jp/azure/app-service/web-sites-monitor" target="_blank">Azureのドキュメント</a>をご確認ください。

最大で 29 個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
|:---|:---|:---|:---|:---|
| Total App Domains                         | TotalAppDomains<br/>TotalAppDomainsUnloaded| azure.app_service.total_app_domains.loaded<br/>azure.app_service.total_app_domains.unloaded| integer   | Average |
| Thread Count                              | Threads                    | azure.app_service.threads.threads                         | integer   | Average |
| Requests In Application Queue             | RequestsInApplicationQueue | azure.app_service.requests_in_application_queue.requests  | integer   | Average |
| Requests                                  | Requests                   | azure.app_service.requests.requests                       | integer   | Total   |
| Private Bytes                             | PrivateBytes               | azure.app_service.private_bytes.bytes                     | bytes     | Average |
| Memory working set                        | MemoryWorkingSet| azure.app_service.memory_working_set.current| bytes     | Average |
| IO Read/Write/Other Operations Per Second | IoOtherOperationsPerSecond<br/>IoReadOperationsPerSecond<br/>IoWriteOperationsPerSecond| azure.app_service.io_iops.other<br/>azure.app_service.io_iops.read<br/>azure.app_service.io_iops.write| iops      | Total   |
| IO Read/Write/Other Bytes Per Second      | IoOtherBytesPerSecond<br/>IoReadBytesPerSecond<br/> IoWriteBytesPerSecond| azure.app_service.io.other<br/>azure.app_service.io.read<br/>azure.app_service.io.write| bytes/sec | Total   |
| Http StatusCode                           | Http101<br/>Http2xx<br/>Http3xx<br/>Http4xx<br/>Http5xx|azure.app_service.http_statuscode.http_101<br/>azure.app_service.http_statuscode.http_2xx<br/>azure.app_service.http_statuscode.http_3xx<br/>azure.app_service.http_statuscode.http_4xx<br/>azure.app_service.http_statuscode.http_server_errors | integer   | Total   |
| Health Check Status                       | HealthCheckStatus          | azure.app_service.health_check_status.health_check_status | integer   | Average |
| Handle Count                              | Handles                    | azure.app_service.handles.handles                         | integer   | Average |
| Gen Garbage Collections                   | Gen0Collections<br/>Gen1Collections<br/>Gen2Collections| azure.app_service.gen_collections.gen_0_collections<br/>azure.app_service.gen_collections.gen_1_collections<br/>azure.app_service.gen_collections.gen_2_collections| integer   | Total   |
| Data In/Out                               | BytesReceived<br/>BytesSent | azure.app_service.data.in<br/>azure.app_service.data.out| bytes     | Total   |
| Current Assemblies                        | CurrentAssemblies          | azure.app_service.current_assemblies.current_assemblies   | integer   | Average |
| CPU Time                                  | CpuTime                    | azure.app_service.cpu_time.cpu_time                       | float     | Total   |
| Connections                               | AppConnections             | azure.app_service.connections.connections                 | integer   | Average |
| Average Response Time                     | AverageResponseTime        | azure.app_service.average_response_time.seconds           | float     | Average |
