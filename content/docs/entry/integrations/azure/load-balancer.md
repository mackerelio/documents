---
Title: Azure Integration - Load Balancer
Date: 2020-04-01T14:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/azure/load-balancer
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613563395820
---

Mackerel supports obtaining and monitoring metrics for <a href="https://azure.microsoft.com/en-us/services/load-balancer/" target="_blank">Load Balancer</a> in Azure Integration. When integrating with Azure Integration, billable targets are determined using the conversion 1 resource = 1 Micro Host. In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for Azure Integration configuration methods and a list of supported Azure services.<br>
<a href="https://mackerel.io/docs/entry/integrations/azure">Azure Integration</a>


## Obtaining metrics

The metrics obtainable with Azure Integration’s Load Balancer support are as follows. For `Metric` explanations, refer to the <a href="https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-standard-diagnostics" target="_blank">Azure help page</a>.

The maximum number of metrics obtainable is `4 x (number of front-end IP addresses) + 2 x (number of back-end IP addresses)`.

|Graph name|Metric|Metric name in Mackerel|Unit|Aggregation Type|
|:---|:---|:---|:---|:---|
|Byte Count|ByteCount|azure.load_balancer.byte_count.#.byte_count|bytes|Total|
|Data Path Availability|VipAvailability|azure.load_balancer.data_path_availability.#.vip_availability|percentage|Average|
|Health Probe Status|DipAvailability|azure.load_balancer.health_probe_status.#.dip_availability|percentage|Average|
|Packet Count|PacketCount|azure.load_balancer.packet_count.#.packet_count|integer|Total|
|SNAT Connection Count|SnatConnectionCount|azure.load_balancer.snat_connection_count.#.snat_connection_count|integer|Total|
|SYN Count|SYNCount|azure.load_balancer.syn_count.#.syn_count|integer|Total|


Enter either the front-end or the back-end IP address for # in "Metric name in Mackerel".<br>
Corresponding metrics are as follows.

- Front-end IP address
     - Byte Count
     - Data Path Availability
     - Packet Count
     - SYN Count

- Back-end IP address
     - Health Probe Status
     - SNAT Connection Count

Each IP address is stored in a hyphen demarcated format.<br>
For example, if the IP address is `127.0.0.1`, it will look like this `127-0-0-1`.
