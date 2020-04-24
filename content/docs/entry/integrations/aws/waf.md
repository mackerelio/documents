---
Title: AWS Integration - WAF
Date: 2020-03-06T10:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/waf
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613555445888
CustomPath: integrations/aws/waf
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/waf/" target="_blank">AWS WAF</a> metrics in AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 Web Access Control List = 1 Micro Host. In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services.<br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>


## Obtaining metrics
The metrics obtainable with AWS Integration's WAF support are as follows. For `Metric` explanations, refer to the <a href="https://docs.aws.amazon.com/en_us/waf/latest/developerguide/monitoring-cloudwatch.html" target="_blank">AWS help page</a>.

The maximum number of metrics obtainable is `4 × (number of rules) + 4<"ALL">`.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|WebACL Requests|AllowedRequests<br>BlockedRequests<br>CountedRequests<br>PassedRequests|waf.web_acl_requests.#.allowed<br>waf.web_acl_requests.#.blocked<br>waf.web_acl_requests.#.counted<br>waf.web_acl_requests.#.passed|integer|Sum|

- Enter the rule name in place of the # in "Metric name in Mackerel".

<h2 id="notes">Notes</h2>
- Tag filtering for hosts is not available with AWS Integration WAF support.

