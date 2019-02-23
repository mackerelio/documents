---
Title: FAQ・About "metric rate monitors"
Date: 2018-04-19T15:46:07+09:00
URL: https://mackerel.io/docs/entry/faq/monitoring/rate-metric-monitor
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/17391345971636462180
---

When creating a host metric monitor, you can select the following four items to be monitored.

- CPU %
- Memory %
- Swap %
- Filesystem %


[f:id:mackerelio:20180419154557p:plain]


These monitors fall under the category **metric rate monitor** and can be set to be monitored based on information of "metrics sent from mackerel-agent” (system metrics). For metrics in this category, thresholds are specified in "percentage" (with 100% as a whole).

Detailed specifications for the above four items are as follows.

- CPU %
    - Regardless of the number of cores, the `100% - idle%` percentage is specified as the threshold when the whole is 100%.
- Memory %
    - The `used` percentage is specified as a threshold for `total`.
- Swap %
    - The `swap used` percentage is specified as a threshold for `swap total`.
- Filesystem %
    - The `used` percentage is specified as a threshold for `size`.
    - When metrics of multiple file systems are posted, the file system with the highest utilization rate and the thresholds are used for comparison.

---

[< Return to FAQ](https://mackerel.io/docs/entry/faq)
