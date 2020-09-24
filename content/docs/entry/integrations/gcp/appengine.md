---
Title: Google Cloud Integration - App Engine
Date: 2020-09-10T12:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/gcp/appengine
EditURL: 
---

Mackerel supports obtaining and monitoring <a href="https://cloud.google.com/appengine" target="_blank">App Engine</a> metrics with Google Cloud Integration. When integrating with Google Cloud Integration, billable targets are determined using the conversion 1 Version = 1 Micro Host. In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for Google Cloud Integration configuration methods and a list of supported services.<br>
<a href="https://mackerel.io/docs/entry/integrations/gcp">Google Cloud Integration</a>

## Required API
The following API must be enabled in order to integrate App Engine.

- App Engine Admin API(appengine.googleapis.com)

## Required role
The following role is required for the service account in order integrate App Engine.

- App Engine Viewer (roles/appengine.appViewer)

## Obtaining metrics
The metrics obtainable with Google Cloud Integration’s App Engine support are as follows. For `Metric` explanations, refer to the <a href="https://cloud.google.com/monitoring/api/metrics_gcp" target="_blank">Google Cloud documentation</a>.

### Standard environments

The maximum number of metrics obtainable is `13 + 4 × [Response code]`.

|Graph name|Index type|Metric name in Mackerel|Unit|
|:---|:---|:---|:---|
|CPU Usage (megacycles)|system/cpu/usage|appengine.system.cpu.usage.[CPU usage source]|integer|
|Memory usage|system/memory/usage|appengine.system.memory.usage|bytes|
|Network bytes|system/network/received_bytes_count<br>system/network/received_bytes_count<br>system/network/sent_bytes_count<br>system/network/sent_bytes_count|appengine.system.network.bytes.received<br>appengine.system.network.bytes.received_cached<br>appengine.system.network.bytes.sent<br>appengine.system.network.bytes.sent_cached|bytes|
|HTTP DoS intercept|http/server/dos_intercept_count|appengine.http.server.dos_intercept.count|integer|
|HTTP quota denial|http/server/quota_denial_count|appengine.http.server.quota_denial.count|integer|
|HTTP Response count|http/server/response_count<br>http/server/response_count|appengine.http.server.response_count.[response code].count<br>appengine.http.server.response_count.[response code].loading_count|integer|
|HTTP Response latencies|http/server/response_latencies<br>http/server/response_latencies|appengine.http.server.response_latencies.[response code].count<br>appengine.http.server.response_latencies.[response code].loading_count|float|
|HTTP Serve style|http/server/response_style_count<br>http/server/response_style_count|appengine.http.server.response_style.[dynamic request].count<br>appengine.http.server.response_style.[dynamic request].count_cached|integer|

* Enter either the `api` or `runtime` in place of [CPU usage source] in "Metric name in Mackerel".
* Enter the HTTP status code in place of [response code] in "Metric name on Mackerel".
* Enter either `dynamic` or` static` in place of [dynamic request] in "Metric name on Mackerel".

### Flexible environments

The maximum number of metrics obtainable is `13 + 4 × [Response code] + 4 × [Instance ID]`.

|Graph name|Index type|Metric name in Mackerel|Unit|
|:---|:---|:---|:---|
|Current active connections|flex/connections/current|appengine.flex.connections.current.count|float|
|Reserved cores|flex/cpu/reserved_cores|appengine.flex.cpu.reserved_cores.count|float|
|CPU Utilization|flex/cpu/utilization|appengine.flex.cpu.utilization.utilization|percentage|
|Disk I/O|flex/disk/read_bytes_count<br>flex/disk/write_bytes_count|appengine.flex.disk.bytes.read<br>appengine.flex.disk.bytes.write|bytes|
|Network bytes|flex/network/received_bytes_count<br>flex/network/sent_bytes_count|appengine.flex.network.bytes.received<br>appengine.flex.network.bytes.sent|bytes|
|Current active connections per instance|flex/instance/connections/current|appengine.flex.instance.[instance ID].connections.current|float|
|CPU Utilization per instance|flex/instance/cpu/utilization|appengine.flex.instance.[instance ID].cpu.utilization.utilization|percentage|
|Network bytes per instance|flex/instance/network/received_bytes_count<br>flex/instance/network/sent_bytes_count|appengine.flex.instance.[instance ID].network.bytes.received<br>appengine.flex.instance.[instance ID].network.bytes.sent|bytes|
|HTTP DoS intercept|http/server/dos_intercept_count|appengine.http.server.dos_intercept.count|integer|
|HTTP quota denial|http/server/quota_denial_count|appengine.http.server.quota_denial.count|integer|
|HTTP Response count|http/server/response_count<br>http/server/response_count|appengine.http.server.response_count.[response code].count<br>appengine.http.server.response_count.[instance ID].loading_count|integer|
|HTTP Response latencies|http/server/response_latencies<br>http/server/response_latencies|appengine.http.server.response_latencies.[response code].count<br>appengine.http.server.response_latencies.[response code].loading_count|float|
|HTTP Serve style|http/server/response_style_count<br>http/server/response_style_count|appengine.http.server.response_style.[dynamic request].count<br>appengine.http.server.response_style.[dynamic request].count_cached|integer|

* Enter the instance ID in place of [instance ID] in "Metric name in Mackerel".
* Enter the HTTP status code in place of [response code] in "Metric name on Mackerel".
* Enter either `dynamic` or` static` in place of [dynamic request] in "Metric name on Mackerel".

### Memcache
In addition to those above, the following metrics are acquired when using Memcache with the 1st generation App Engine.

The maximum number of obtainable metrics is `5 × [command name]`

|Graph name|Index type|Metric name in Mackerel|Unit|
|:---|:---|:---|:---|
|Memcache utilization|memcache/centi_mcu_count|appengine.memcache.[command name].centi_mcu_count.count|integer|
|Memcache operation count|memcache/operation_count|appengine.memcache.[command name].operation.count|integer|
|Memcache network received bytes|memcache/received_bytes_count|appengine.memcache.[command name].bytes.received.[status]|bytes|
|Memcache network transmitted bytes|memcache/sent_bytes_count|appengine.memcache.[command name].bytes.sent.sent|bytes|

* Enter the Memcace command name in place of [command name] in "Metric name on Mackerel".
* Enter either `HIT` or `MISS` in place of [status] in "Metric name on Mackerel".
