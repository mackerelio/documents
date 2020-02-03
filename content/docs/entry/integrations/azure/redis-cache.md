---
Title: Azure Integration - Redis Cache
Date: 2017-10-02T18:21:32+09:00
URL: https://mackerel.io/docs/entry/integrations/azure/redis-cache
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812303841204
---

Mackerel supports obtaining and monitoring <a href="https://azure.microsoft.com/en-us/services/cache/" target="_blank">Redis Cache</a> metrics in Azure Integration. When integrating with Azure Integration, billable targets are determined using the conversion 1 Cache = 1 Micro Host.
In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for Azure Integration configuration methods and a list of supported Azure services. 

<a href="https://mackerel.io/docs/entry/integrations/azure">Azure Integration</a>

## Obtaining metrics

The metrics obtainable with Redis Cache SQL Database support are as follows. For `Metric` explanations, refer to the <a href="https://docs.microsoft.com/en-us/azure/redis-cache/cache-how-to-monitor#available-metrics-and-reporting-intervals" target="_blank">Azure help page</a>.

The maximum number of metrics obtainable is `16 + 16 × (number of Shards)`.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
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

Mackerel also supports metrics for each Shard of Redis Cache. For each of the above graphs, shard-level graphs are generated and the metrics of each shard can be seen all in one place.
