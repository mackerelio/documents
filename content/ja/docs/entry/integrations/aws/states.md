---
Title: AWSインテグレーション - Step Functions
Date: 2019-05-28T16:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/states
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17680117127170721430
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/step-functions/" target="_blank">AWS Step Functions</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1ステートマシン = 1マイクロホストと換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのStep Functions対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/step-functions/latest/dg/procedure-cw-metrics.html
" target="_blank">AWSのヘルプ</a>をご確認ください。

最大で `7 + 9 × (アクティビティ数) + 8 × (Lambdaファンクション数) + 8 × (サービス統合数)` のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:--|:--|:--|:--|:--|
|Executions|ExecutionsAborted<br>ExecutionsFailed<br>ExecutionsStarted<br>ExecutionsSucceeded<br>ExecutionsTimedOut|states.executions.aborted<br>states.executions.failed<br>states.executions.started<br>states.executions.succeeded<br>states.executions.timed_out|integer|Sum|
|Execution Time|ExecutionTime|states.execution_time.execution_time|float|Average|
|Execution Throttled|ExecutionThrottled|states.execution_throttled.execution_throttled|integer|Sum|
|Activities|ActivitiesScheduled<br>ActivitiesStarted<br>ActivitiesSucceeded<br>ActivitiesTimedOut<br>ActivitiesHeartbeatTimedOut<br>ActivitiesFailed|states.activities.ACTIVITY_NAME.scheduled<br>states.activities.ACTIVITY_NAME.started<br>states.activities.ACTIVITY_NAME.succeeded<br>states.activities.ACTIVITY_NAME.timed_out<br>states.activities.ACTIVITY_NAME.heartbeat_timed_out<br>states.activities.ACTIVITY_NAME.failed|integer|Sum|
|Activity Time|ActivityScheduleTime<br>ActivityRunTime<br>ActivityTime|states.activity_time.ACTIVITY_NAME.schedule<br>states.activity_time.ACTIVITY_NANE.run<br>states.activity_time.ACTIVITY_NANE.all|float|Average|
|Lambda Functions|LambdaFunctionsScheduled<br>LambdaFunctionsStarted<br>LambdaFunctionsSucceeded<br>LambdaFunctionsTimedOut<br>LambdaFunctionsFailed|states.lambda_functions.LAMBDA_FUNCTION_NAME.scheduled<br>states.lambda_functions.LAMBDA_FUNCTION_NAME.started<br>states.lambda_functions.LAMBDA_FUNCTION_NAME.succeeded<br>states.lambda_functions.LAMBDA_FUNCTION_NAME.timed_out<br>states.lambda_functions.LAMBDA_FUNCTION_NAME.failed|integer|Sum|
|Lambda Function Time|LambdaFunctionsScheduleTime<br>LambdaFunctionRunTime<br>LambdaFunctionTime|states.lambda_function_time.LAMBDA_FUNCTION_NAME.schedule<br>states.lambda_function_time.LAMBDA_FUNCTION_NAME.run<br>states.lambda_function_time.LAMBDA_FUNCTION_NAME.all|float|Average|
|Service Integrations|ServiceIntegrationsFailed<br>ServiceIntegrationsScheduled<br>ServiceIntegrationsStarted<br>ServiceIntegrationsSucceeded<br>ServiceIntegrationsTimedOut|states.service_integrations.SERVICE_INTEGRATION_NAME.failed<br>states.service_integrations.SERVICE_INTEGRATION_NAME.scheduled<br>states.service_integrations.SERVICE_INTEGRATION_NAME.started<br>states.service_integrations.SERVICE_INTEGRATION_NAME.succeeded<br>states.service_integrations.SERVICE_INTEGRATION_NAME.timedout|integer|Sum|
|Service Integration Time|ServiceIntegrationRunTime<br>ServiceIntegrationScheduleTime<br>ServiceIntegrationTime|states.service_integration_time.SERVICE_INTEGRATION_NAME.run<br>states.service_integration_time.SERVICE_INTEGRATION_NAME.schedule<br>states.service_integration_time.SERVICE_INTEGRATION_NAME.all|float|Average|

- "Mackerel上のメトリック名"のACTIVITY_NANEにはアクティビティ名が入ります。
- "Mackerel上のメトリック名"のLAMBDA_FUNCTION_NAMEにはLambdaファンクション名が入ります。
- "Mackerel上のメトリック名"のSERVICE_INTEGRATION_NAMEにはサービス統合名が入ります。

<h2 id="notes">注意事項</h2>
- AWSインテグレーションのStep Functions対応では、名前にマルチバイト文字を含むステートマシンをホストとして登録しません。
