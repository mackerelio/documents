---
Title: Aggregating Amazon CloudWatch Logs and posting the metrics to Mackerel
Date: 2022-02-25T11:07:37+09:00
URL: https://mackerel.io/docs/entry/advanced/cloudwatch-logs-aggregator
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/13574176438066957908
CustomPath: advanced/cloudwatch-logs-aggregator
---

Using [cloudwatch-logs-aggregator](https://github.com/mackerelio-labs/mackerel-monitoring-modules/tree/main/cloudwatch-logs-aggregator), you can aggregate logs output to [Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html) and post the results to Mackerel as service metrics.

Metrics, which are numerical data, are essential for quantitatively monitoring application usage and visualizing it with graphs, etc. The following are some examples of such metrics.

- Number of data processed
- Speed of data processing
- Number of errors that occurred

However, to output these metrics directly from the application, it is necessary to

- conduct statistical processing
- use an API to post the metrics

This makes it complicated to incorporate such features into an application.

cloudwatch-logs-aggregator uses logs output to CloudWatch Logs as the source data and separates the processing required to output the metrics—as listed above—from the application. This allows you to easily monitor and visualize application metrics, especially those running on AWS cloud environments such as Amazon ECS and AWS Lambda.

## cloudwatch-logs-aggregator Overview
cloudwatch-logs-aggregator is a Lambda function used to aggregate logs using [CloudWatch Logs Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AnalyzingLogData.html) and post the results to Mackerel as service metrics.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220214/20220214181027.png" alt="Architecture diagram" width="600" height="470" loading="lazy" title="Architecture diagram" class="hatena-fotolife" itemprop="image"></span></p>

For instance, it can use the application logs output to CloudWatch Logs, as shown in the example below, as a data source to aggregate the number of logs and values inside the logs, and post the results to Mackerel as metrics.

``` json
{"level":"info","msg":"query complete","bytes_scanned":51828}
{"level":"info","msg":"query complete","bytes_scanned":124413}
...
```

<p><span itemscope="" itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220217/20220217110906.png" alt="Graph of metrics extracted from logs" width="444" height="334" loading="lazy" title="Graph of metrics extracted from logs" class="hatena-fotolife" itemprop="image"></span></p>

cloudwatch-logs-aggregator comes with a source code to construct the Lambda function on the user's AWS account. This source code is provided in the form of a [Terraform](https://www.terraform.io) module.

## How to use
Please refer to the following article for more specific practical use cases, with step-by-step instructions.

- [Extract Metrics from Logs in Amazon CloudWatch Logs Using cloudwatch-logs-aggregator](https://mackerel.io/blog/entry/cloudwatch-logs-aggregator)

### 1. Set up the application
#### Change the output destination of logs to CloudWatch Logs
To use the cloudwatch-logs-aggregator, the application logs must be sent to CloudWatch Logs.

For instructions to change the output destination of logs to CloudWatch Logs, please check the help documentation provided by the service you are using.

- [Monitoring your container instances - Amazon Elastic Container Service](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/using_cloudwatch_logs.html)
- [Accessing Amazon CloudWatch logs for AWS Lambda - AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/monitoring-cloudwatchlogs.html)

#### Output logs, which will be the source of metrics
Output the logs, which will be the source of metrics, from the application.

For example, if you want to record the number of data processed, the number of successful data processing, and the number of failures over a certain period, output the log from the application as follows.

``` json
{"job_name":"foo","processed":18,"success":15,"failure":3}
```

The logs must be output into a format that CloudWatch Logs Insights can analyze. In general, it is recommended to output logs in JSON.

### 2. Setup cloudwatch-logs-aggregator
#### Acquire Mackerel API Key
Go to [Mackerel's API Key page](https://mackerel.io/my?tab=apikeys) to acquire the API Key required to post the metrics from cloudwatch-logs-aggregator and save it to [Parameter Store of AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html). It is recommended to set the parameter type as SecureString to save the API key in an encrypted form.

The parameter name configured here will be used in the configuration steps later.

#### Create Lambda function
Create the Lambda function, the main component of cloudwatch-logs-aggregator.

The source code to create the Lambda function is provided in the form of a Terraform module. Refer to [the document in the repository](https://github.com/mackerelio-labs/mackerel-monitoring-modules/tree/main/cloudwatch-logs-aggregator#cloudwatch-logs-aggregator-lambda) and use the module from the Terraform configuration file (e.g., `main.tf`).

#### Create EventBridge rules
Create Amazon EventBridge (CloudWatch Events) rules to configure the Lambda function parameters, such as the execution interval of the function. You will also configure other settings here, such as the CloudWatch Logs Insights queries for aggregating the logs and the names of the metrics on Mackerel.

You can create multiple rules for a single Lambda function.

As is the case for the Lambda function, the source code to create the EventBridge rules is provided in the form of a Terraform module. Refer to [the document in the repository](https://github.com/mackerelio-labs/mackerel-monitoring-modules/tree/main/cloudwatch-logs-aggregator#cloudwatch-logs-aggregator-rule) and use the module from the Terraform configuration file.

- In `function_arn`, configure the ARN for the Lambda function created in the step above.
- In `api_key_name`, configure the parameter name on Parameter Store where you have stored the Mackerel API key in the step above.
- In `query`, configure the CloudWatch Logs Insights queries. Please refer to [AWS documentation](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax.html) for the query syntax. It is recommended to use [the AWS console](https://console.aws.amazon.com/cloudwatch/home#logsV2:logs-insights) to create the queries as you can check the execution results simultaneously.
- We have prepared [several examples](https://github.com/mackerelio-labs/mackerel-monitoring-modules/tree/main/cloudwatch-logs-aggregator#examples) of queries and Mackerel metrics names, so please use them as references.

## Pricing
While cloudwatch-logs-aggregator is provided free-of-charge, usage fees will be charged by Mackerel based on the number of service metrics posted as well as by AWS for the execution of the Lambda function, the saving and retrieval of logs on CloudWatch Logs, etc..

For details, please check the pricing for Mackerel and AWS' services.

- [Pricing - Mackerel: A Revolutionary Server Management and Monitoring Service](https://en.mackerel.io/pricing)
- [Serverless Computing – AWS Lambda Pricing – Amazon Web Services](https://aws.amazon.com/lambda/pricing/)
- [Amazon CloudWatch Pricing – Amazon Web Services (AWS)](https://aws.amazon.com/cloudwatch/pricing/)
