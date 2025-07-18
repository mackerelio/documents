---
Title: AWS Integration - Amazon Connect
Date: 2021-03-09T10:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/connect
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613703992830
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/connect/" target="_blank">Amazon Connect</a> metrics in AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 Instance = 1 Micro Host. In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services.<br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

## Obtaining metrics
The metrics obtainable with AWS Integration's Connect support are as follows. For `Metric` explanations, refer to the <a href="https://docs.aws.amazon.com/connect/latest/adminguide/monitoring-cloudwatch.html" target="_blank">AWS help page</a>.

The maximum number of metrics obtainable can be calculated using the formula `11 + 3 × (number of contact flows) + 5 × (number of queues) + 1 × (signing key)`.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:--|:--|:--|:--|:--|
|Concurrent Calls Service Quota|ConcurrentCallsPercentage|connect.concurrent_calls_service_quota.quota|percentage|Maximum|
|To Instance Packet Loss Rate|ToInstancePacketLossRate|connect.to_instance_packet_loss_rate.minimum<br>connect.to_instance_packet_loss_rate.average<br>connect.to_instance_packet_loss_rate.maximum|float|Minimum<br>Average<br>Maximum|
|Voice Calls|CallsBreachingConcurrencyQuota<br>CallsPerInterval<br>ConcurrentCalls<br>ThrottledCalls<br>MissedCalls<br>MisconfiguredPhoneNumbers|connect.voice_calls.breaching_concurrency_quota<br>connect.voice_calls.calls_per_interval<br>connect.voice_calls.concurrent_calls<br>connect.voice_calls.throttled<br>connect.voice_calls.missed<br>connect.voice_calls.misconfigured_phone_numbers|integer|Sum<br>Sum<br>Maximum<br>Sum<br>Sum<br>Sum|
|Call Recording Upload|CallRecordingUploadError|connect.call_recording_upload.error|integer|Sum|
|Contact Flows|ContactFlowErrors<br>ContactFlowFatalErrors<br>CallBackNotDialableNumber|connect.contact_flows.CONTACT_FLOW_NAME.errors<br>connect.contact_flows.CONTACT_FLOW_NAME.fatal_errors<br>connect.contact_flows.CONTACT_FLOW_NAME.call_back_not_dialable|integer|Sum|
|Queues|QueueSize<br>QueueCapacityExceededError|connect.queue.QUEUE_NAME.size<br>connect.queue.QUEUE_NAME.capacity_exceeded_error|integer|Maximum<br>Sum|
|Queue Wait Time|LongestQueueWaitTime|connect.queue_wait_time.QUEUE_NAME.minimum<br>connect.queue_wait_time.QUEUE_NAME.average<br>connect.queue_wait_time.QUEUE_NAME.maximum|float|Minimum<br>Average<br>Maximum|
|Public Signing Key|PublicSigningKeyUsage|connect.public_signing_key.SIGNING_KEY_ID|integer|Sum|

- Replace the following items in "Metric name in Mackerel" with the corresponding information.
    - `CONTACT_FLOW_NAME`：The name of your contact flow
    - `QUEUE_NAME`：The name of your queue
    - `SIGNING_KEY_ID`：The ID of your signing key

<h2 id="notes">Notes</h2>

- AWS Integration support for Connect does not include filtering hosts by tags.
- AWS Integration support for Connect does not include assigning roles by tags.
- Metrics will no generate in CloudWatch if non-ASCII characters are included in the contact flow name or queue name. This is a specification of AWS.
