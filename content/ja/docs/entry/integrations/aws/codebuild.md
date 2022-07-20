---
Title: AWSインテグレーション - CodeBuild
Date: 2022-06-28T10:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/codebuild
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/codebuild/" target="_blank">AWS CodeBuild</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1プロジェクト = 1マイクロホストと換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのCodeBuild対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/codebuild/latest/userguide/monitoring-builds.html" target="_blank">AWSのヘルプ</a>をご確認ください。

最大で20個のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|Builds|Builds<br>SucceededBuilds<br>FailedBuilds|codebuild.builds.count<br>codebuild.builds.succeeded<br>codebuild.builds.failed|integer|Sum|
|Durations|Duration<br>SubmittedDuration<br>QueuedDuration<br>ProvisioningDuration<br>DownloadSourceDuration<br>InstallDuration<br>PreBuildDuration<br>BuildDuration<br>PostBuildDuration<br>UploadArtifactsDuration<br>FinalizingDuration|codebuild.builds.all_builds<br>codebuild.builds.submitted<br>codebuild.builds.queued<br>codebuild.builds.provisioning<br>codebuild.builds.download_source<br>codebuild.builds.install<br>codebuild.builds.pre_build<br>codebuild.builds.build<br>codebuild.builds.post_build<br>codebuild.builds.upload_artifact<br>codebuild.builds.finalizing|seconds|Average|
|CPU Utilization|CPUUtilized|codebuild.cpu_utilization.used|float|Average|
|CPU Utilization Percent|CPUUtilizedPercent|codebuild.cpu_utilization_percent.used|percentage|Average|
|Memory Utilization|MemoryUtilized|codebuild.memory_utilization.used|bytes|Average|
|Memory Utilization Percent|MemoryUtilizedPercent|codebuild.memory_utilization_percent.used|percentage|Average|
|Storage IO Bytes|StorageReadBytes<br>StorageWriteBytes|codebuild.storage_io_bytes.read<br>codebuild.storage_io_bytes.write|bytes/sec|Average