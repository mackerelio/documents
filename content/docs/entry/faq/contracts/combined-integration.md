---
Title: FAQ・How does the billing system work when using mackerel-agent and AWS / Azure
  Integration together?
Date: 2018-01-17T13:11:41+09:00
URL: https://mackerel.io/docs/entry/faq/contracts/combined-integration
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812338374890
---

When using mackerel-agent and AWS/Azure Integration together, the metrics obtained from the two functions are aggregated into one host with a billable rate of either a standard host or a micro host.

- If mackerel-agent is installed on an Amazon EC2/Azure Virtual Machine instance while also using the Integration function: 1 Standard host
    - Information that can be obtained from the instance metadata API provided by each platform is used when linking to hosts registered with Integration.
    - For AWS, both v1 and v2 of the EC2 instance metadata service are supported by Mackerel. Only v1 of the instance metadata service is supported for mackerel-agent v0.66.0 and earlier.
    - Please note that if you disable the instance metadata service, each will be registered as a different Standard host in Mackerel.
- When [using the custom_identifier to aggregate monitors with the plugin](https://mackerel.io/docs/entry/integrations/aws#plugin-custom-identifier) for Integration registered hosts in other services: 1 Micro host

Please note that the maximum number of metrics per host does not change. For other important points regarding exceeding metrics etc., refer to [Handling of host conversion when plan limits are exceeded](https://mackerel.io/docs/entry/faq/contracts/limit-exceeded-conversion).

---

[< Return to FAQ](https://mackerel.io/docs/entry/faq)
