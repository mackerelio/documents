---
Title: AWS Integration - SES
Date: 2019-04-08T18:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/ses
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/17680117127020883280
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/jp/ses/" target="_blank">Amazon Simple Email Service</a> metrics in AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 Region = 1 Micro Host.
In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services.<br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>


## Obtaining metrics
The metrics obtainable with AWS Integration's support for SES are as follows. For `Metric` explanations, refer to the AWS help page.<br><a href="https://docs.aws.amazon.com/us_en/ses/latest/DeveloperGuide/monitor-sending-activity.html" target="_blank">Monitoring Your Amazon SES Sending Activity</a><br><a href="https://docs.aws.amazon.com/us_en/ses/latest/DeveloperGuide/receiving-email-metrics.html" target="_blank">Viewing Metrics for Amazon SES Email Receiving</a>

The maximum number of metrics obtainable is `10 + 2 × (number of rules)`.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:--|:--|:--|:--|:--|
|Email Sending Events|Send<br>Delivery<br>Bounce<br>Complaint<br>Reject<br>Open<br>Click|ses.email_sending_events.send<br>ses.email_sending_events.delivery<br>ses.email_sending_events.bounce<br>ses.email_sending_events.complaint<br>ses.email_sending_events.reject<br>ses.email_sending_events.open<br>ses.email_sending_events.click|integer|Sum|
|Reputation|Reputation.BounceRate<br>Reputation.ComplaintRate|ses.reputation.bounce_rate<br>ses.reputation.complaint_rate|percentage|Average|
|Email Receiving Failure|PublishFailure<br>PublishExpired|ses.email_receiving_failure.#.failure<br>ses.email_receiving_failure.#.expired|integer|Sum|
|Sent Last 24 Hours|-|ses.sent_last_24_hours.sent|integer|-|

- Enter the SES RuleName in place of the # in "Metric name in Mackerel".

<h2 id="notes">Notes</h2>
- The Email Sending Events "Open" and "Click" occur when using <a href="https://docs.aws.amazon.com/us_en/ses/latest/DeveloperGuide/using-configuration-sets.html" target="_blank">Configuration Sets</a> and Open/Click events have been captured. Refer to the <a href="https://docs.aws.amazon.com/us_en/ses/latest/DeveloperGuide/configure-custom-open-click-domains.html" target="_blank">AWS Help page</a> for more details.
- Tag filtering for hosts is not available with AWS Integration SES support.
