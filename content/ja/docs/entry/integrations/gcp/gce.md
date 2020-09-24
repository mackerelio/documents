---
Title: Google Cloudインテグレーション - Compute Engine
Date: 2020-09-10T12:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/gcp/gce
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613631814925
---

MackerelはGoogle Cloudインテグレーションにて<a href="https://cloud.google.com/compute" target="_blank">Compute Engine</a>のメトリック取得や監視に対応しています。
Google Cloudインテグレーションで連携をおこなった場合、課金対象として 1インスタンス = 1スタンダードホスト と換算します。Google Cloudインテグレーションと[mackerel-agent](https://mackerel.io/ja/docs/entry/howto/install-agent)の併用も可能です。その場合、システムメトリックとカスタムメトリックはひとつのホストに統合されます（重複課金はされません）。

Google Cloudインテグレーションの設定方法や、対応済みGoogle Cloudサービスの一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/gcp">Google Cloudインテグレーション</a>

## 必要なAPI
Compute Engine連携を利用するためには、以下のAPIが有効になっている必要があります。

- Compute Engine API(compute.googleapis.com)

## 必要なロール
Compute Engine連携を利用するためには、サービスアカウントに以下のロールが必要です。

- Compute 閲覧者(roles/compute.viewer)

## 取得メトリック
Google CloudインテグレーションのCompute Engine対応で取得できるメトリックは以下の通りです。`メトリック` の説明に関しては<a href="https://cloud.google.com/monitoring/api/metrics_gcp" target="_blank">Google Cloudのドキュメント</a>をご確認ください。

最大で `10 + 11 × [ディスク名] + [パケットドロップの原因]` 個のメトリックが取得されます。

|グラフ名|指標タイプ|Mackerel上のメトリック名|単位|
|:---|:---|:---|:---|
|CPU|instance/cpu/utilization|gce.instance.cpu.used|percentage|
|Disk bytes|instance/disk/read_bytes_count<br>instance/disk/write_bytes_count|gce.instance.disk_io.[ディスク名].read<br>gce.instance.disk_io.[ディスク名].write|bytes|
|Disk ops count|instance/disk/read_ops_count<br>instance/disk/write_ops_count|gce.instance.disk_ops.[ディスク名].read<br>gce.instance.disk_ops.[ディスク名].write|integer|
|Disk throttled bytes|instance/disk/throttled_read_bytes_count<br>instance/disk/throttled_write_bytes_count|gce.instance.disk-throttled_io.[ディスク名].[スロットリングの原因].read<br>gce.instance.disk-throttled_io.[ディスク名].[スロットリングの原因].write|bytes|
|Disk throttled ops|instance/disk/throttled_read_ops_count<br>instance/disk/throttled_write_ops_count|gce.instance.disk-throttled_ops.[ディスク名].[スロットリングの原因].read<br>gce.instance.disk-throttled_ops.[ディスク名].[スロットリングの原因].write|integer|
|Network bytes|instance/network/received_bytes_count<br>instance/network/sent_bytes_count|gce.instance.network.received<br>gce.instance.network.sent|bytes|
|Network packets count|instance/network/received_packets_count<br>instance/network/sent_packets_count|gce.instance.network_packets.received<br>gce.instance.network_packets.sent|integer|
|Uptime|instance/uptime|gce.instance.uptime.uptime|integer|
|Firewall dropped bytes|firewall/dropped_bytes_count|gce.firewall.dropped|bytes|
|Firewall dropped packets count|firewall/dropped_packets_count|gce.firewall_packets.dropped|integer|
|Mirroring bytes|mirroring/mirrored_bytes_count|gce.mirroring.mirroed|bytes|
|Mirroring packets|mirroring/mirrored_packets_count|gce.mirroring_packets.mirrored|integer|
|Mirroring packets dropped|mirroring/dropped_packets_count|gce.mirroring_packets_dropped.[パケットドロップの原因]|integer|

* "Mackerel上のメトリック名"の[ディスク名]にはCompute Engineインスタンスに接続されたディスクデバイス名が入ります。
* "Mackerel上のメトリック名"の[パケットドロップの原因]にはパケットドロップの原因となった識別子が入ります。
* "Mackerel上のメトリック名"の[スロットリングの原因]には、`PER_GB`や`PER_VM`など、スロットリングの原因となった識別子が入ります。
