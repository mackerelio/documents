---
Title: AWS Integration - CodeBuild
Date: 2022-07-19T10:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/codebuild
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/
---

Mackerel supports obtaining and monitoring <a href="https://aws.amazon.com/codebuild/" target="_blank">AWS CodeBuild</a> metrics in AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 Project = 1 Micro Host. In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services.<br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>

## Obtaining metrics
The metrics obtainable with AWS Integration's CodeBuild support are as follows. For `Metric` explanations, refer to the <a href="https://docs.aws.amazon.com/codebuild/latest/userguide/monitoring-builds.html" target="_blank">AWS help page</a>.

The maximum number of metrics obtainable is 20.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:--|:--|:--|:--|:--|
|Builds|Builds<br>SucceededBuilds<br>FailedBuilds|codebuild.builds.count<br>codebuild.builds.succeeded<br>codebuild.builds.failed|integer|Sum|
|Durations|Duration<br>SubmittedDuration<br>QueuedDuration<br>ProvisioningDuration<br>DownloadSourceDuration<br>InstallDuration<br>PreBuildDuration<br>BuildDuration<br>PostBuildDuration<br>UploadArtifactsDuration<br>FinalizingDuration|codebuild.builds.all_builds<br>codebuild.builds.submitted<br>codebuild.builds.queued<br>codebuild.builds.provisioning<br>codebuild.builds.download_source<br>codebuild.builds.install<br>codebuild.builds.pre_build<br>codebuild.builds.build<br>codebuild.builds.post_build<br>codebuild.builds.upload_artifact<br>codebuild.builds.finalizing|seconds|Average|
|CPU Utilization|CPUUtilized|codebuild.cpu_utilization.used|float|Average|
|CPU Utilization Percent|CPUUtilizedPercent|codebuild.cpu_utilization_percent.used|percentage|Average|
|Memory Utilization|MemoryUtilized|codebuild.memory_utilization.used|bytes|Average|
|Memory Utilization Percent|MemoryUtilizedPercent|codebuild.memory_utilization_percent.used|percentage|Average|
|Storage IO Bytes|StorageReadBytes<br>StorageWriteBytes|codebuild.storage_io_bytes.read<br>codebuild.storage_io_bytes.write|bytes/sec|Average
