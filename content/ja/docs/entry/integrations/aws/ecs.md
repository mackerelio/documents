---
Title: AWSインテグレーション - ECS
Date: 2019-03-14T18:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/ecs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17680117126999837445
CustomPath: integrations/aws/ecs
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/ecs/" target="_blank">Amazon Elastic Container Service</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1クラスター = 1ホストと換算します。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのECS対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/AmazonECS/latest/developerguide/cloudwatch-metrics.html" target="_blank">AWSのヘルプ</a>をご確認ください。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|CPU Utilization|CPUUtilization|ecs.cpu_utilization.minimum<br>ecs.cpu_utilization.average<br>ecs.cpu_utilization.maximum|percentage|Minimum<br>Average<br>Maximum|
|Memory Utilization|MemoryUtilization|ecs.memory_utilization.minimum<br>ecs.memory_utilization.average<br>ecs.memory_utilization.maximum|percentage|Minimum<br>Average<br>Maximum|
|CPU Reservation|CPUReservation|ecs.cpu_reservation.minimum<br>ecs.cpu_reservation.average<br>ecs.cpu_reservation.maximum|percentage|Minimum<br>Average<br>Maximum|
|Memory Reservation|MemoryReservation|ecs.memory_reservation.minimum<br>ecs.memory_reservation.average<br>ecs.memory_reservation.maximum|percentage|Minimum<br>Average<br>Maximum|
|Running Task|CPUUtilization|ecs_running_task.#.count|integer|SampleCount|
|Service CPU Utilization|CPUUtilization|ecs.service_cpu_utilization.#.minimum<br>ecs.service_cpu_utilization.#.average<br>ecs.service_cpu_utilization.#.maximum|percentage|Minimum<br>Average<br>Maximum|
|Service Memory Utilization|MemoryUtilization|ecs.service_memory_utilization.#.minimum<br>ecs.service_memory_utilization.#.average<br>ecs.service_memory_utilization.#.maximum|percentage|Minimum<br>Average<br>Maximum|

- "Mackerel上のメトリック名"の#には、ECSのサービス名が入ります。
