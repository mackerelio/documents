---
Title: AWSインテグレーション - Athena
URL: https://mackerel.io/ja/docs/entry/integrations/aws/athena
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802340630909633707
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/athena/" target="_blank">Amazon Athena</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1ワークグループ = 1マイクロホストと換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック

AWSインテグレーションのAthena対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/athena/latest/ug/query-metrics-viewing.html" target="_blank">AWSのヘルプ</a>をご確認ください。

最大で63個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|Processed Bytes|ProcessedBytes|athena.processed_bytes.#.average<br>athena.processed_bytes.#.max<br>athena.processed_bytes.#.min|bytes|Average<br>Maximum<br>Minimum|
|Time of DML [ms]|EngineExecutionTime<br>QueryPlanningTime<br>QueryQueueTime<br>ServicePreProcessingTime<br>ServiceProcessingTime<br>TotalExecutionTime|athena.time.dml.#.engine_execution<br>athena.time.dml.#.query_planning<br>athena.time.dml.#.query_queue<br>athena.time.dml.#.service_pre_processing<br>athena.time.dml.#.service_processing<br>athena.time.dml.#.total_execution|float|Average|
|Time of DDL [ms]|EngineExecutionTime<br>QueryPlanningTime<br>QueryQueueTime<br>ServicePreProcessingTime<br>ServiceProcessingTime<br>TotalExecutionTime|athena.time.ddl.#.engine_execution<br>athena.time.ddl.#.query_planning<br>athena.time.ddl.#.query_queue<br>athena.time.ddl.#.service_pre_processing<br>athena.time.ddl.#.service_processing<br>athena.time.ddl.#.total_execution|float|Average|
|Time of UTILITY [ms]|EngineExecutionTime<br>QueryPlanningTime<br>QueryQueueTime<br>ServicePreProcessingTime<br>ServiceProcessingTime<br>TotalExecutionTime|athena.time.utility.#.engine_execution<br>athena.time.utility.#.query_planning<br>athena.time.utility.#.query_queue<br>athena.time.utility.#.service_pre_processing<br>athena.time.utility.#.service_processing<br>athena.time.utility.#.total_execution|float|Average|

- "Mackerel上のメトリック名"の`#`には、Athenaのクエリの実行結果である`SUCCEEDED`・`FAILED`・`CANCELED`のいずれかが入ります。
