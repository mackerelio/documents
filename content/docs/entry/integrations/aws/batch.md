---
Title: AWS Integration - Batch
Date: 2020-01-10T10:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/batch
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613498448884
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/batch/" target="_blank">AWS Batch</a> metrics in AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 Region = 1 Micro Host. In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services.<br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

## Obtaining metrics
The metrics obtainable with AWS Integration's support for Batch are as follows. For `Metric` explanations, refer to the <a href="https://docs.aws.amazon.com/us_en/batch/latest/userguide/job_states.html" target="_blank">AWS help page</a>.

The maximum number of metrics obtainable is `7 × (number of job queues)`.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:--|:--|:--|:--|:--|
|Job Queue Status|-|batch.job_queue_status.#.succeeded<br>batch.job_queue_status.#.failed<br>batch.job_queue_status.#.running<br>batch.job_queue_status.#.starting<br>batch.job_queue_status.#.runnable<br>batch.job_queue_status.#.pending<br>batch.job_queue_status.#.submitted|integer|-|

- Enter the job queue name in place of the # in "Metric name in Mackerel".

<h2 id="notes">Notes</h2>
- Tag filtering for hosts is not available with AWS Integration Batch support.
- Assigning roles by tags is not available with AWS Integration Batch support.
