---
Title: AWSインテグレーション - EFS
Date: 2019-06-06T18:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/efs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17680117127188496675
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/efs/" target="_blank">Amazon Elastic File System</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1ファイルシステム = 1マイクロホストと換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのEFS対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/efs/latest/ug/monitoring-cloudwatch.html" target="_blank">AWSのヘルプ</a>をご確認ください。

最大で28個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|Burst Credit Balance|BurstCreditBalance|efs.burst_credit_balance.minimum<br>efs.burst_credit_balance.average<br>efs.burst_credit_balance.maximum|bytes|Mminimum<br>Average<br>Maximum|
|Client Connections|ClientConnections|efs.client_connections.count|integer|Sum|
|IO Limit|PercentIOLimit|efs.io_limit.maximum|percentage|Maximum|
|Permitted Throughput|PermittedThroughput|efs.permitted_throughput.minimum<br>efs.permitted_throughput.average<br>efs.permitted_throughput.maximum|bytes/sec|Minimum<br>Average<br>Maximum|
|Data IO Count|DataReadIOBytes<br>DataWriteIOBytes<br>MetadataIOBytes<br>TotalIOBytes|efs.data_io_count.read<br>efs.data_io_count.write<br>efs.data_io_count.metadata<br>efs.data_io_count.total|integer|SampleCount|
|Data IO Sum Bytes|DataReadIOBytes<br>DataWriteIOBytes<br>MetadataIOBytes<br>TotalIOBytes|efs.data_io_sum_bytes.read<br>efs.data_io_sum_bytes.write<br>efs.data_io_sum_bytes.metadata<br>efs.data_io_sum_bytes.total|bytes|Sum|
|Data IO Bytes|DataReadIOBytes<br>DataWriteIOBytes<br>MetadataIOBytes<br>TotalIOBytes|efs.data_io_bytes.Read.minimum<br>efs.data_io_bytes.Read.average<br>efs.data_io_bytes.Read.maximum<br>efs.data_io_bytes.Write.minimum<br>efs.data_io_bytes.Write.average<br>efs.data_io_bytes.Write.maximum<br>efs.data_io_bytes.Metadata.minimum<br>efs.data_io_bytes.Metadata.average<br>efs.data_io_bytes.Metadata.maximum<br>efs.data_io_bytes.Total.minimum<br>efs.data_io_bytes.Total.average<br>efs.data_io_bytes.Total.maximum|bytes|Minimum<br>Average<br>Maximum|
