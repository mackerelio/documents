---
Title: AWS Integration - SQS
Date: 2017-10-25T15:01:02+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/sqs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812311196822
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/sqs/" target="_blank">Amazon Simple Queue Service (SQS)</a> metrics in AWS Integration.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services. <br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

## Obtaining metrics
The metrics obtainable with AWS Integration’s SQS support are as follows. For `Metric` explanations, refer to the <a href="https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-available-cloudwatch-metrics.html" target="_blank">AWS help page</a>.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|Oldest Age|ApproximateAgeOfOldestMessage|sqs.oldest_message.age|integer|Maximum|
|Message State Count|ApproximateNumberOfMessagesDelayed<br>ApproximateNumberOfMessagesNotVisible<br>ApproximateNumberOfMessagesVisible|sqs.message_state.delayed<br>sqs.message_state.not_visible<br>sqs.message_state.visible|integer|Average|
|Receive Count|NumberOfEmptyReceives<br>NumberOfMessagesReceived|sqs.receive_count.empty<br>sqs.receive_count.received|integer|Sum|
|Message Count|NumberOfMessagesDeleted<br>NumberOfMessagesSent|sqs.message_count.deleted<br>sqs.message_count.sent|integer|Sum|
|Sent Message Size|SentMessageSize<br>SentMessageSize<br>SentMessageSize|sqs.message_size.average<br>sqs.message_size.max<br>sqs.message_size.min|bytes|Average<br>Maximum<br>Minimum|

## Notes

Due to restrictions on the AWS API, if there are more than 1000 queues in the same region, all queues may not integrate.
