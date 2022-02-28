---
Title: AWS Integration - Billing
Date: 2020-11-02T10:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/billing
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613653429133
CustomPath: integrations/aws/billing
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/aws-cost-management/" target="_blank">AWS Billing</a> metrics with AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 management account = 1 Micro Host. In addition to this, charges may be incurred if the maximum number of retrievable metrics per micro host is exceeded.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services.<br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

<b><u>Billing alerts must be enabled in order to integrate metrics for estimated charges. Refer to following page for configuration methods.</u></b><br>
<a href="https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/monitor_estimated_charges_with_cloudwatch.html#turning_on_billing_metrics">Enable billing alerts</a>

## Obtaining metrics
The metrics obtainable with AWS Integration's Billing support are as follows. For `Metric` explanations, refer to the <a href="https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/monitor_estimated_charges_with_cloudwatch.html" target="_blank">AWS help page</a>.

The maximum number of metrics obtainable is `1 + 1 × (number of member accounts) + 3 × (number of budgets)`.

Enter the member account ID in place of `ACCOUNT_ID` in the metric name . (Example: 217452466226, etc.)

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:---|:---|:---|:---|:---|
|Estimated Charges|EstimatedCharges|billing.estimated_charges.total|float|Maximum|
|Estimated Charges By Account|EstimatedCharges|billing.estimated_charges_by_account.ACCOUNT_ID|float|Maximum|
|Budgets|-|billing.budgets.#.actual<br>billing.budgets.#.forecasted<br>billing.budgets.#.limit|float|-|

- Enter the budget name in place of # in "Metric name in Mackerel". If the budget name contains characters that cannot be used in metric names, those characters will be encoded.


<h2 id="notes">Notes</h2>
- Assigning roles by tags is not available with AWS Integration Billing support.
- The estimated charge is updated several times a day and the budget is updated at least once a day. This is a specification of AWS.
