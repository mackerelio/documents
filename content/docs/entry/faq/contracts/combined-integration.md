---
Title: FAQ・How does the billing system work when using mackerel-agent and AWS / Azure
  Integration together?
Date: 2018-01-17T13:11:41+09:00
URL: https://mackerel.io/docs/entry/faq/contracts/combined-integration
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812338374890
---

When using mackerel-agent and AWS/Azure Integration together, the metrics obtained from the two functions are aggregated into one host with a billable rate of either a standard host or a micro host.

- When mackerel-agent is installed on an EC2/Azure VM instance while also using the Integration function: 1 Standard host
- When [using the custom_identifier to aggregate monitors with the plugin](https://mackerel.io/docs/entry/integrations/aws#plugin-custom-identifier) for Integration registered hosts in other services: 1 Micro host

However, please note that the upper limit for the number of metrics per host does not change. For other precautions regarding exceeding metrics etc., refer to [Handling of host conversion when plan limits are exceeded](https://mackerel.io/docs/entry/faq/contracts/limit-exceeded-conversion).

---

[< Return to FAQ](https://mackerel.io/docs/entry/faq)
