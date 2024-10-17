---
Title: Azure Integration - Virtual Machines
Date: 2017-11-24T17:23:53+09:00
URL: https://mackerel.io/docs/entry/integrations/azure/virtual-machine
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812320734054
---

Mackerel supports obtaining and monitoring <a href="https://azure.microsoft.com/ja-jp/services/virtual-machines/" target="_blank">Virtual Machines</a> metrics in Azure Integration. When integrating with Azure Integration, billable targets are determined using the conversion 1 Instance = 1 Standard Host.

Please refer to the following page for Azure Integration configuration methods and a list of supported Azure services. <br>
<a href="https://mackerel.io/docs/entry/integrations/azure">Azure Integration</a>

## Obtaining metrics

The metrics obtainable with Azure Integration’s Virtual Machines support are as follows. For `metric` explanations, refer to the <a href="https://learn.microsoft.com/en-us/azure/virtual-machines/monitor-vm-reference" target="_blank">Azure help page</a>.

The maximum number of metrics obtainable is 11.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|CPU|Percentage CPU|azure.virtual_machine.cpu.percent|percentage|Average|
|CPU Credits Remaining/Consumed|CPU Credits Remaining<br>CPU Credits Consumed|azure.virtual_machine.cpu_credits.remaining<br>azure.virtual_machine.cpu_credits.consumed|float|Average|
|Disk IOPS|Disk Read Operations/Sec<br>Disk Write Operations/Sec|azure.virtual_machine.disk_iops.read<br>azure.virtual_machine.disk_iops.write|iops|Average|
|VM Availability Metric|VmAvailabilityMetric|azure.virtual_machine.availability|float|Average|
|Network In/Out|Network In<br>Network Out|azure.virtual_machine.network.in<br>azure.virtual_machine.network.out|bytes|Total|
|Network In/Out Total|Network In Total<br>Network Out Total|azure.virtual_machine.network_total.in<br>azure.virtual_machine.network_total.out|bytes|Total|
|Disk Read/Write Bytes|Disk Read Bytes<br>Disk Write Bytes|azure.virtual_machine.disk.read<br>azure.virtual_machine.disk.write|bytes|Total|

- For CPU Credits Remaining/Consumed metrics, only [burstable instance type](https://azure.microsoft.com/en-us/blog/introducing-b-series-our-new-burstable-vm-size/) are retrieved.
- VM Availability Metric is in preview on Microsoft Azure, so the behavior may change due to specification changes (as of October 2024).

## Using with mackerel-agent 

If mackerel-agent has already been installed on the target Virtual Machine instance, host information from Mackerel will automatically be integrated and registered as one host. It will not be counted twice as a billable host.

If you prefer a more detailed style of monitoring with AWS Integration, we recommend installing mackerel-agent for Virtual Machines.
