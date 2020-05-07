---
Title: Azureインテグレーション - Load Balancer
Date: 2020-04-01T14:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/azure/load-balancer
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613563391760
---

MackerelはAzureインテグレーションにて<a href="https://azure.microsoft.com/ja-jp/services/load-balancer/" target="_blank">Load Balancer</a>のメトリック取得や監視に対応しています。Azureインテグレーションで連携をおこなった場合、課金対象として 1リソース = 1マイクロホスト と換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

Azureインテグレーションの設定方法や対応Azureサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/azure">Azureインテグレーション</a>

## 取得メトリック
AzureインテグレーションのLoad Balancer対応で取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://docs.microsoft.com/ja-jp/azure/load-balancer/load-balancer-standard-diagnostics" target="_blank">Azureのドキュメント</a>をご確認ください。

最大で `4 × (フロントエンドIPアドレス数) + 2 × (バックエンドIPアドレス数)` 個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
|:---|:---|:---|:---|:---|
|Byte Count|ByteCount|azure.load_balancer.byte_count.#.byte_count|bytes|Total|
|Data Path Availability|VipAvailability|azure.load_balancer.data_path_availability.#.vip_availability|percentage|Average|
|Health Probe Status|DipAvailability|azure.load_balancer.health_probe_status.#.dip_availability|percentage|Average|
|Packet Count|PacketCount|azure.load_balancer.packet_count.#.packet_count|integer|Total|
|SNAT Connection Count|SnatConnectionCount|azure.load_balancer.snat_connection_count.#.snat_connection_count|integer|Total|
|SYN Count|SYNCount|azure.load_balancer.syn_count.#.syn_count|integer|Total|

Mackerel上のメトリック名の「#」には、フロントエンドIPアドレスかバックエンドIPアドレスのいずれかが格納されます。<br>
対応するメトリックは以下の通りです。

- フロントエンドIPアドレス
    - Byte Count
    - Data Path Availability
    - Packet Count
    - SYN Count

- バックエンドIPアドレス
    - Health Probe Status
    - SNAT Connection Count

それぞれのIPアドレスは、ハイフン区切りの形式で格納されます。<br>
例えば、IPアドレスが `127.0.0.1` の場合は `127-0-0-1` となります。
