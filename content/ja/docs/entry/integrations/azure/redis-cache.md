---
Title: Azureインテグレーション - Cache for Redis
Date: 2017-06-29T16:42:19+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/azure/redis-cache
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8599973812275094045
---

MackerelはAzureインテグレーションにて<a href="https://azure.microsoft.com/ja-jp/services/cache/" target="_blank">Cache for Redis</a>のメトリック取得や監視に対応しています。Azureインテグレーションで連携をおこなった場合、課金対象として 1キャッシュ = 1マイクロホスト と換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

Azureインテグレーションの設定方法や対応Azureサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/azure">Azureインテグレーション</a>

## 取得メトリック
AzureインテグレーションのCache for Redis対応で取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://learn.microsoft.com/ja-jp/azure/azure-monitor/reference/supported-metrics/microsoft-cache-redis-metrics" target="_blank">Azureのドキュメント</a>をご確認ください。

最大で `23 + 23 × (シャード数)` 個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
|:---|:---|:---|:---|:---|
|CPU|percentProcessorTime|azure.redis_cache.cpu.percent|percentage|Maximum|
|Connected Clients|connectedclients|azure.redis_cache.connected_clients.count|integer|Maximum|
|Total Keys|totalkeys|azure.redis_cache.total_keys.count|integer|Maximum|
|Used Memory|used<br>used_rss|azure.redis_cache.memory.used<br>azure.redis_cache.memory.used_rss|bytes|Maximum|
|Server Load|serverLoad|azure.redis_cache.server.load|percentage|Maximum|
|Cache Read/Write|cacheRead<br>cacheWrite|azure.redis_cache.cache_io.read<br>azure.redis_cache.cache_io.write|bytes/sec|Maximum|
|Cache Hits/Misses|cachehits<br>cachemisses|azure.redis_cache.cache.hits<br>azure.redis_cache.cache.misses|integer|Total|
|Commands|totalcommandsprocessed<br>getcommands<br>setcommands|azure.redis_cache.command.processed<br>azure.redis_cache.command.get<br>azure.redis_cache.command.set|integer|Total|
|Evicted/Expired Keys|evictedkeys<br>expiredkeys|azure.redis_cache.keys.evicted<br>azure.redis_cache.keys.expired|integer|Total|
|Operations Per Second|operationsPerSecond|azure.redis_cache.operations.iops|iops|Maximum|
|Errors|errors|azure.redis_cache.errors.aof<br>azure.redis_cache.errors.dataloss<br>azure.redis_cache.errors.export<br>azure.redis_cache.errors.failover<br>azure.redis_cache.errors.import<br>azure.redis_cache.errors.rdb<br>azure.redis_cache.errors.unresponsiveclients|integer|Maximum|

MackerelはCache for Redisのシャードごとのメトリックにも対応しています。上記のグラフのPer Shardなグラフが作られ、シャードごとのメトリックの様子を一括でみられます。
Errorsについては、以下のエラー種別ごとにPer Shardなグラフが作られます。

- AOF
- Dataloss
- Export
- Failover
- Import
- RDB
- UnresponsiveClients
