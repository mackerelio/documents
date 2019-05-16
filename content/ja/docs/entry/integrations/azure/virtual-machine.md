---
Title: Azureインテグレーション - Virtual Machine
Date: 2017-07-12T14:12:35+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/azure/virtual-machine
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8599973812278972185
---

MackerelはAzureインテグレーションにて<a href="https://azure.microsoft.com/ja-jp/services/virtual-machines/" target="_blank">Virtual Machine</a>のメトリック取得や監視に対応しています。Azureインテグレーションで連携をおこなった場合、課金対象として 1インスタンス = 1スタンダードホスト と換算します。

Azureインテグレーションの設定方法や対応Azureサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/azure">Azureインテグレーション</a>

## 取得メトリック
AzureインテグレーションのVirtual Machine対応で取得できるメトリックは以下の通りです。 `メトリック` の説明に関しては<a href="https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics#a-namemicrosoftcomputevirtualmachinesamicrosoftcomputevirtualmachines" target="_blank">Azureのドキュメント</a>をご確認ください。

最大で7個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Aggregation Type|
|:---|:---|:---|:---|:---|
|CPU|Percentage CPU|azure.virtual_machine.cpu.percent|percentage|Average|
|Disk IOPS|Disk Read Operations/Sec<br>Disk Write Operations/Sec|azure.virtual_machine.disk_iops.read<br>azure.virtual_machine.disk_iops.write|iops|Average|
|Network In/Out|Network In<br>Network Out|azure.virtual_machine.network.in<br>azure.virtual_machine.network.out|bytes|Total|
|Disk Read/Write Bytes|Disk Read Bytes<br>Disk Write Bytes|azure.virtual_machine.disk.read<br>azure.virtual_machine.disk.write|bytes|Total|


## mackerel-agent との併用について

インテグレーションの対象となっているVirtual Machineインスタンスにmackerel-agentが導入されている場合、Mackerel上でホスト情報は自動的に統合され、ひとつのホストとして登録されます。課金対象ホストとして重複カウントされることはありません。

Virtual Machineの場合、Azureインテグレーションで簡易的な監視をおこない、より詳細な監視をおこないたい場合に、mackerel-agentを導入するのがおすすめです。
