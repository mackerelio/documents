---
Title: Google Cloudインテグレーション - App Engine
Date: 2020-09-10T12:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/gcp/appengine
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613631816745
---

MackerelはGoogle Cloudインテグレーションにて<a href="https://cloud.google.com/appengine" target="_blank">App Engine</a>のメトリック取得や監視に対応しています。
Google Cloudインテグレーションで連携をおこなった場合、課金対象として 1バージョン = 1マイクロホスト と換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

Google Cloudインテグレーションの設定方法や対応済みGoogle Cloudサービスの一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/gcp">Google Cloudインテグレーション</a>

## 必要なAPI
App Engine連携を利用するためには、以下のAPIが有効になっている必要があります。

- App Engine Admin API(appengine.googleapis.com)

## 必要なロール
App Engine連携を利用するためには、サービスアカウントに以下のロールが必要です。

- App Engine 閲覧者(roles/appengine.appViewer)

## 取得メトリック
Google CloudインテグレーションのApp Engine対応で取得できるメトリックは以下の通りです。`メトリック` の説明に関しては<a href="https://cloud.google.com/monitoring/api/metrics_gcp" target="_blank">Google Cloudのドキュメント</a>をご確認ください。

### スタンダード環境

最大で `13 + 4 × [レスポンスコード]` 個のメトリックが取得されます。

|グラフ名|指標タイプ|Mackerel上のメトリック名|単位|
|:---|:---|:---|:---|
|CPU Usage (megacycles)|system/cpu/usage|appengine.system.cpu.usage.[CPU利用ソース]|integer|
|Memory usage|system/memory/usage|appengine.system.memory.usage|bytes|
|Network bytes|system/network/received_bytes_count<br>system/network/received_bytes_count<br>system/network/sent_bytes_count<br>system/network/sent_bytes_count|appengine.system.network.bytes.received<br>appengine.system.network.bytes.received_cached<br>appengine.system.network.bytes.sent<br>appengine.system.network.bytes.sent_cached|bytes|
|HTTP DoS intercept|http/server/dos_intercept_count|appengine.http.server.dos_intercept.count|integer|
|HTTP quota denial|http/server/quota_denial_count|appengine.http.server.quota_denial.count|integer|
|HTTP Response count|http/server/response_count<br>http/server/response_count|appengine.http.server.response_count.[レスポンスコード].count<br>appengine.http.server.response_count.[レスポンスコード].loading_count|integer|
|HTTP Response latencies|http/server/response_latencies<br>http/server/response_latencies|appengine.http.server.response_latencies.[レスポンスコード].count<br>appengine.http.server.response_latencies.[レスポンスコード].loading_count|float|
|HTTP Serve style|http/server/response_style_count<br>http/server/response_style_count|appengine.http.server.response_style.[動的リクエスト].count<br>appengine.http.server.response_style.[動的リクエスト].count_cached|integer|

* "Mackerel上のメトリック名"の[CPU利用ソース]には`api`または`runtime`が入ります。
* "Mackerel上のメトリック名"の[レスポンスコード]にはHTTPステータスコードが入ります。
* "Mackerel上のメトリック名"の[動的リクエスト]には`dynamic`または`static`が入ります。

### フレキシブル環境

最大で `13 + 4 × [レスポンスコード] + 4 × [インスタンスID]` 個のメトリックが取得されます。

|グラフ名|指標タイプ|Mackerel上のメトリック名|単位|
|:---|:---|:---|:---|
|Current active connections|flex/connections/current|appengine.flex.connections.current.count|float|
|Reserved cores|flex/cpu/reserved_cores|appengine.flex.cpu.reserved_cores.count|float|
|CPU Utilization|flex/cpu/utilization|appengine.flex.cpu.utilization.utilization|percentage|
|Disk I/O|flex/disk/read_bytes_count<br>flex/disk/write_bytes_count|appengine.flex.disk.bytes.read<br>appengine.flex.disk.bytes.write|bytes|
|Network bytes|flex/network/received_bytes_count<br>flex/network/sent_bytes_count|appengine.flex.network.bytes.received<br>appengine.flex.network.bytes.sent|bytes|
|Current active connections per instance|flex/instance/connections/current|appengine.flex.instance.[インスタンスID].connections.current|float|
|CPU Utilization per instance|flex/instance/cpu/utilization|appengine.flex.instance.[インスタンスID].cpu.utilization.utilization|percentage|
|Network bytes per instance|flex/instance/network/received_bytes_count<br>flex/instance/network/sent_bytes_count|appengine.flex.instance.[インスタンスID].network.bytes.received<br>appengine.flex.instance.[インスタンスID].network.bytes.sent|bytes|
|HTTP DoS intercept|http/server/dos_intercept_count|appengine.http.server.dos_intercept.count|integer|
|HTTP quota denial|http/server/quota_denial_count|appengine.http.server.quota_denial.count|integer|
|HTTP Response count|http/server/response_count<br>http/server/response_count|appengine.http.server.response_count.[レスポンスコード].count<br>appengine.http.server.response_count.[レスポンスコード].loading_count|integer|
|HTTP Response latencies|http/server/response_latencies<br>http/server/response_latencies|appengine.http.server.response_latencies.[レスポンスコード].count<br>appengine.http.server.response_latencies.[レスポンスコード].loading_count|float|
|HTTP Serve style|http/server/response_style_count<br>http/server/response_style_count|appengine.http.server.response_style.[動的リクエスト].count<br>appengine.http.server.response_style.[動的リクエスト].count_cached|integer|

* "Mackerel上のメトリック名"の[インスタンスID]にはインスタンスIDが入ります。
* "Mackerel上のメトリック名"の[レスポンスコード]にはHTTPステータスコードが入ります。
* "Mackerel上のメトリック名"の[動的リクエスト]には`dynamic`または`static`が入ります。

### Memcache

第1世代のApp EngineでMemcacheを利用する場合、上記に加えて以下のメトリックが取得されます。

最大で `5 × [コマンド名]` 個のメトリックが取得されます。

|グラフ名|指標タイプ|Mackerel上のメトリック名|単位|
|:---|:---|:---|:---|
|Memcache utilization|memcache/centi_mcu_count|appengine.memcache.[コマンド名].centi_mcu_count.count|integer|
|Memcache operation count|memcache/operation_count|appengine.memcache.[コマンド名].operation.count|integer|
|Memcache network received bytes|memcache/received_bytes_count|appengine.memcache.[コマンド名].bytes.received.[ステータス]|bytes|
|Memcache network transmitted bytes|memcache/sent_bytes_count|appengine.memcache.[コマンド名].bytes.sent.sent|bytes|

* "Mackerel上のメトリック名"の[コマンド名]にはMemcacheコマンド名が入ります。
* "Mackerel上のメトリック名"の[ステータス]には`HIT`または`MISS`が入ります。

<h2 id="notes">注意事項</h2>
- Google CloudインテグレーションではApp Engine側の仕様により、ラベルでApp Engineのホストを絞り込むことができません。
