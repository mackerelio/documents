---
Title: Google Cloud Integration - Compute Engine
Date: 2020-09-10T12:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/gcp/gce
EditURL: 
---

Mackerel supports obtaining and monitoring <a href="https://cloud.google.com/compute" target="_blank">Compute Engine</a> metrics with Google Cloud Integration. When integrating with Google Cloud Integration, billable targets are determined using the conversion 1 Instance = 1 Standard Host. Google Cloud Integration can also be used together with [mackerel-agent](https://mackerel.io/docs/entry/howto/install-agent). In such cases, system metrics and custom metrics are integrated into one host (with no redundant charges).

Please refer to the following page for Google Cloud Integration configuration methods and a list of supported services.<br>
<a href="https://mackerel.io/docs/entry/integrations/gcp">Google Cloud Integration</a>

## Required API
The following API must be enabled in order to integrate Compute Engine.

- Compute Engine API(compute.googleapis.com)

## Required role
The following role is required for the service account in order to integrate Compute Engine.

- Compute Viewer(roles/compute.viewer)

## Obtaining metrics
The metrics obtainable with Google Cloud Integration’s Compute Engine support are as follows. For `Metric` explanations, refer to the <a href="https://cloud.google.com/monitoring/api/metrics_gcp" target="_blank">Google Cloud documentation</a>.

The maximum number of obtainable metrics is `10 + 11 × [disk name] + [packet drop source]`

|Graph name|Index type|Metric name in Mackerel|Unit|
|:---|:---|:---|:---|
|CPU|instance/cpu/utilization|gce.instance.cpu.used|percentage|
|Disk bytes|instance/disk/read_bytes_count<br>instance/disk/write_bytes_count|gce.instance.disk_io.[disk name].read<br>gce.instance.disk_io.[disk name].write|bytes|
|Disk ops count|instance/disk/read_ops_count<br>instance/disk/write_ops_count|gce.instance.disk_ops.[disk name].read<br>gce.instance.disk_ops.[disk name].write|integer|
|Disk throttled bytes|instance/disk/throttled_read_bytes_count<br>instance/disk/throttled_write_bytes_count|gce.instance.disk-throttled_io.[disk name].[throttling source].read<br>gce.instance.disk-throttled_io.[disk name].[throttling origin].write|bytes|
|Disk throttled ops|instance/disk/throttled_read_ops_count<br>instance/disk/throttled_write_ops_count|gce.instance.disk-throttled_ops.[disk name].[throttling source].read<br>gce.instance.disk-throttled_ops.[disk name].[throttling origin].write|integer|
|Network bytes|instance/network/received_bytes_count<br>instance/network/sent_bytes_count|gce.instance.network.received<br>gce.instance.network.sent|bytes|
|Network packets count|instance/network/received_packets_count<br>instance/network/sent_packets_count|gce.instance.network_packets.received<br>gce.instance.network_packets.sent|integer|
|Uptime|instance/uptime|gce.instance.uptime.uptime|integer|
|Firewall dropped bytes|firewall/dropped_bytes_count|gce.firewall.dropped|bytes|
|Firewall dropped packets count|firewall/dropped_packets_count|gce.firewall_packets.dropped|integer|
|Mirroring bytes|mirroring/mirrored_bytes_count|gce.mirroring.mirroed|bytes|
|Mirroring packets|mirroring/mirrored_packets_count|gce.mirroring_packets.mirrored|integer|
|Mirroring packets dropped|mirroring/dropped_packets_count|gce.mirroring_packets_dropped.[packet drop source]|integer|

* Enter the disk device name linked to the Compute Engine instance in place of [disk name] in "Metric name in Mackerel".
* Enter an identifier for the packet drop source in place of [packet drop source] in "Metric name in Mackerel".
* Enter an identifier for the throttling source such as `PER_GB` or `PER_VM` in place of [throttling source] in "Metric name in Mackerel".
