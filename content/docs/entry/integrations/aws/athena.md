---
Title: AWS Integration - Athena
URL: https://mackerel.io/docs/entry/integrations/aws/athena
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802340630909633981
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/athena/" target="_blank">Amazon Athena</a> metrics in AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 Workgroup = 1 Micro Host. In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services.<br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

## Obtaining metrics

The metrics obtainable with AWS Integration's Athena support are as follows. For `Metric` explanations, refer to the <a href="https://docs.aws.amazon.com/athena/latest/ug/query-metrics-viewing.html" target="_blank">AWS help page</a>.

The maximum number of metrics obtainable is 63.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:--|:--|:--|:--|:--|
|Processed Bytes|ProcessedBytes|athena.processed_bytes.#.average<br>athena.processed_bytes.#.max<br>athena.processed_bytes.#.min|bytes|Average<br>Maximum<br>Minimum|
|Time of DML [ms]|EngineExecutionTime<br>QueryPlanningTime<br>QueryQueueTime<br>ServicePreProcessingTime<br>ServiceProcessingTime<br>TotalExecutionTime|athena.time.dml.#.engine_execution<br>athena.time.dml.#.query_planning<br>athena.time.dml.#.query_queue<br>athena.time.dml.#.service_pre_processing<br>athena.time.dml.#.service_processing<br>athena.time.dml.#.total_execution|float|Average|
|Time of DDL [ms]|EngineExecutionTime<br>QueryPlanningTime<br>QueryQueueTime<br>ServicePreProcessingTime<br>ServiceProcessingTime<br>TotalExecutionTime|athena.time.ddl.#.engine_execution<br>athena.time.ddl.#.query_planning<br>athena.time.ddl.#.query_queue<br>athena.time.ddl.#.service_pre_processing<br>athena.time.ddl.#.service_processing<br>athena.time.ddl.#.total_execution|float|Average|
|Time of UTILITY [ms]|EngineExecutionTime<br>QueryPlanningTime<br>QueryQueueTime<br>ServicePreProcessingTime<br>ServiceProcessingTime<br>TotalExecutionTime|athena.time.utility.#.engine_execution<br>athena.time.utility.#.query_planning<br>athena.time.utility.#.query_queue<br>athena.time.utility.#.service_pre_processing<br>athena.time.utility.#.service_processing<br>athena.time.utility.#.total_execution|float|Average|

- The `#` in "Metric name in Mackerel" will be filled with either `SUCCEEDED`, `FAILED`, or `CANCELED` which are the results of Athena queries.
