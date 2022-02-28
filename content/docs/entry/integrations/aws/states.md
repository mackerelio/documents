---
Title: AWS Integration - Step Functions
Date: 2019-05-28T16:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/aws/states
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/17680117127170725119
---

Mackerel supports obtaining and monitoring the metric of <a href="https://aws.amazon.com/en/step-functions/" target="_blank">AWS Step Functions</a> in AWS Integration. When integrating with AWS Integration, billable targets are determined using the conversion 1 state machine = 1 Micro Host. In addition to this, depending on the number of metrics retrieved, you may be charged for exceeding the maximum number of metrics per micro host.

Please refer to the following page for AWS Integration configuration methods and a list of supported AWS services.<br>
<a href="https://mackerel.io/docs/entry/integrations/aws">AWS Integration</a>


## Obtaining metrics
The metrics obtainable with AWS Integration's support for Step Functions are as follows. For `Metric` explanations, refer to <a href="https://docs.aws.amazon.com/en_us/step-functions/latest/dg/procedure-cw-metrics.html" target="_blank">the AWS help page</a>.

The maximum number of metrics obtainable can be calculated using the formula `7 + 9 × (number of activites) + 8 × (number of Lambda functions) + 8 × (number of service integrations)`.

|Graph name|Metric|Metric name in Mackerel|Unit|Statistics|
|:--|:--|:--|:--|:--|
|Executions|ExecutionsAborted<br>ExecutionsFailed<br>ExecutionsStarted<br>ExecutionsSucceeded<br>ExecutionsTimedOut|states.executions.aborted<br>states.executions.failed<br>states.executions.started<br>states.executions.succeeded<br>states.executions.timed_out|integer|Sum|
|Execution Time|ExecutionTime|states.execution_time.execution_time|float|Average|
|Execution Throttled|ExecutionThrottled|states.execution_throttled.execution_throttled|integer|Sum|
|Activities|ActivitiesScheduled<br>ActivitiesStarted<br>ActivitiesSucceeded<br>ActivitiesTimedOut<br>ActivitiesHeartbeatTimedOut<br>ActivitiesFailed|states.activities.ACTIVITY_NAME.scheduled<br>states.activities.ACTIVITY_NAME.started<br>states.activities.ACTIVITY_NAME.succeeded<br>states.activities.ACTIVITY_NAME.timed_out<br>states.activities.ACTIVITY_NAME.heartbeat_timed_out<br>states.activities.ACTIVITY_NAME.failed|integer|Sum|
|Activity Time|ActivityScheduleTime<br>ActivityRunTime<br>ActivityTime|states.activity_time.ACTIVITY_NAME.schedule<br>states.activity_time.ACTIVITY_NAME.run<br>states.activity_time.ACTIVITY_NAME.all|float|Average|
|Lambda Functions|LambdaFunctionsScheduled<br>LambdaFunctionsStarted<br>LambdaFunctionsSucceeded<br>LambdaFunctionsTimedOut<br>LambdaFunctionsFailed|states.lambda_functions.LAMBDA_FUNCTION_NAME.scheduled<br>states.lambda_functions.LAMBDA_FUNCTION_NAME.started<br>states.lambda_functions.LAMBDA_FUNCTION_NAME.succeeded<br>states.lambda_functions.LAMBDA_FUNCTION_NAME.timed_out<br>states.lambda_functions.LAMBDA_FUNCTION_NAME.failed|integer|Sum|
|Lambda Function Time|LambdaFunctionsScheduleTime<br>LambdaFunctionRunTime<br>LambdaFunctionTime|states.lambda_function_time.LAMBDA_FUNCTION_NAME.schedule<br>states.lambda_function_time.LAMBDA_FUNCTION_NAME.run<br>states.lambda_function_time.LAMBDA_FUNCTION_NAME.all|float|Average|
|Service Integrations|ServiceIntegrationsFailed<br>ServiceIntegrationsScheduled<br>ServiceIntegrationsStarted<br>ServiceIntegrationsSucceeded<br>ServiceIntegrationsTimedOut|states.service_integrations.SERVICE_INTEGRATION_NAME.failed<br>states.service_integrations.SERVICE_INTEGRATION_NAME.scheduled<br>states.service_integrations.SERVICE_INTEGRATION_NAME.started<br>states.service_integrations.SERVICE_INTEGRATION_NAME.succeeded<br>states.service_integrations.SERVICE_INTEGRATION_NAME.timedout|integer|Sum|
|Service Integration Time|ServiceIntegrationRunTime<br>ServiceIntegrationScheduleTime<br>ServiceIntegrationTime|states.service_integration_time.SERVICE_INTEGRATION_NAME.run<br>states.service_integration_time.SERVICE_INTEGRATION_NAME.schedule<br>states.service_integration_time.SERVICE_INTEGRATION_NAME.all|float|Average|

- Enter the name of the activity in place of ACTIVITY_NAME in "Metric name in Mackerel".
- Enter the name of the Lambda function in place of LAMBDA_FUNCTION_NAME in "Metric name in Mackerel".
- Enter the name of the service integration in place of SERVICE_INTEGRATION_NAME in "Metric name in Mackerel".

<h2 id="notes">Notes</h2>

Assigning roles by tags is not available with AWS Integration Step Functions support.
