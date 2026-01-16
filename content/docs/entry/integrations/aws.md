---
Title: AWS Integration
Date: 2016-05-12T16:39:45+09:00
URL: https://mackerel.io/docs/entry/integrations/aws
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6653812171395777794
---

**This function is only offered in the Trial plan and Standard plan.**

AWS Integration allows you to manage your AWS cloud products as Mackerel hosts and monitor metrics.

Of the AWS cloud products, EC2 is billed as a standard host and the other products are billed as micro hosts.
Additionally, the API of AWS will be called every 5 minutes for each targeted metric to be obtained. Please take note, for this reason, an [Amazon CloudWatch API usage fee](https://aws.amazon.com/jp/cloudwatch/pricing/) may occur.

<details>
<summary>Table of Contents (click to open/close)</summary>

[:contents]

</details>

<h2 id="support-products">Supported AWS cloud products</h2>

Currently, the following AWS cloud products are supported. For information on obtaining metrics, please refer to each individual document.

* [EC2](https://mackerel.io/docs/entry/integrations/aws/ec2)
* [CLB (ELB)](https://mackerel.io/docs/entry/integrations/aws/elb)
* [ALB](https://mackerel.io/docs/entry/integrations/aws/alb)
* [NLB](https://mackerel.io/docs/entry/integrations/aws/nlb)
* [RDS](https://mackerel.io/docs/entry/integrations/aws/rds)
* [ElastiCache](https://mackerel.io/docs/entry/integrations/aws/elasticache)
* [Redshift](https://mackerel.io/docs/entry/integrations/aws/redshift)
* [Lambda](https://mackerel.io/docs/entry/integrations/aws/lambda)
* [SQS](https://mackerel.io/docs/entry/integrations/aws/sqs)
* [DynamoDB](https://mackerel.io/docs/entry/integrations/aws/dynamodb)
* [CloudFront](https://mackerel.io/docs/entry/integrations/aws/cloudfront)
* [API Gateway](https://mackerel.io/docs/entry/integrations/aws/apigateway)
* [Kinesis Data Streams](https://mackerel.io/docs/entry/integrations/aws/kinesis)
* [S3](https://mackerel.io/docs/entry/integrations/aws/s3)
* [OpenSearch Service](https://mackerel.io/docs/entry/integrations/aws/es)
* [ECS Cluster](https://mackerel.io/docs/entry/integrations/aws/ecs)
* [SES](https://mackerel.io/docs/entry/integrations/aws/ses)
* [Step Functions](https://mackerel.io/docs/entry/integrations/aws/states)
* [EFS](https://mackerel.io/docs/entry/integrations/aws/efs)
* [Kinesis Data Firehose](https://mackerel.io/docs/entry/integrations/aws/firehose)
* [AWS Batch](https://mackerel.io/docs/entry/integrations/aws/batch)
* [WAF](https://mackerel.io/docs/entry/integrations/aws/waf)
* [Billing](https://mackerel.io/docs/entry/integrations/aws/billing)
* [Route 53](https://mackerel.io/docs/entry/integrations/aws/route53)
* [Amazon Connect](https://mackerel.io/docs/entry/integrations/aws/connect)
* [DocumentDB](https://mackerel.io/docs/entry/integrations/aws/docdb)
* [CodeBuild](https://mackerel.io/docs/entry/integrations/aws/codebuild)
* [Athena](https://mackerel.io/docs/entry/integrations/aws/athena)

<h2 id="setting">Integration method</h2>
There are two ways to integrate AWS integration: by IAM role with authentication by AssumeRole, or by Access Key ID and Secret Access Key.

You are required to set up one of them, but from a security standpoint, we strongly recommend that you set up a set using IAM roles.

<ul>
  <li><a href="#setting_aws">1. Choose one of the following methods to configure integration.</a></li>
  <ul>
      <li><a href="#setting_aws_iam_role">1-a. Configure the IAM role</a></li>
      <li><a href="#setting_aws_access_key">1-b. Configure the Access Key ID and Secret Access Key</a></li>
  </ul>
  <li><a href="#setting_policy">2. Grant policies</a></li>
  <li><a href="#setting_check_host">3. Confirm the host</a></li>
</ul>

<h3 id="setting_aws">1. Create an IAM role or IAM user for integration</h3>

<h4 id="setting_aws_iam_role">1-a. Configure the IAM role</h4>
<h5>Creating a role with the IAM Management Console</h5>
Create a new role with the <a href="https://console.aws.amazon.com/iam" target="_blank">IAM Management Console</a>.
Select `Another AWS account` from the role types.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190528/20190528124827.png)

Click the 'Create' button in the [Mackerel AWS Integration Settings page](https://mackerel.io/my?tab=awsIntegration) to get an External ID. Enter `217452466226` as the authorized Account ID. Select the `Require external ID` option and specify the External ID obtained from Mackerel's configuration page. The Mackerel system uses the account to access the user’s role. With this configuration, only the Mackerel account can access the created role. Create the role without checking `Require MFA`.

The next step is to <a href="#setting_policy">grant policies</a>.

<h4 id="setting_aws_access_key">1-b. Configure the Access Key ID and Secret Access Key</h4>
The following method is NOT recommended for security protection reasons.

<h5>Creating a user with the IAM Management Console</h5>

Create a new user with the <a href="https://console.aws.amazon.com/iam" target="_blank">IAM Management Console</a>. We recommend assigning an easy-to-understand name like `MackerelAWSIntegrationUser` for use in Mackerel’s AWS integration.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20160512/20160512162316.png)

<h5>Registering the Access Key in Mackerel</h5>
Register the Access Key ID and Secret Access Key (displayed on the screen when creating the account) [in Mackerel](https://mackerel.io/my?tab=awsIntegration). Be careful not to mistake the organization to be registered.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20160512/20160512162642.png)

<h3 id="setting_policy">2. Grant policies</h3>

Grant the IAM role or IAM user you created the required policies/actions for each product listed in the table below. If permissions that are stronger than necessary, such as FullAccess permissions, are granted, AWS integration will not work properly.

* Mackerel checks permissions as described in [Regarding the access key's authority check and CreateInternetGateway](#permission-check) below.

For products marked with *1 in the "AWS Products" column of the table, CloudWatchReadOnlyAccess is additionally required when targeting only that product in the AWS integration settings.

* This is not required if you are also targeting products without *1.

For SQS, OpenSearch Service, and Step Functions, the actions marked with *2 in the "Required Policies/Actions" column are additionally required when using the following settings.

* [Specify tags to filter hosts to register](#tag)
* [Specify metrics to retrieve](#select-metric)

#### Required policies/actions for each product

| AWS Products | Policy / Action | Note |
| :--- | :--- | :--- |
| EC2 |  AmazonEC2ReadOnlyAccess  |  |
| CLB (ELB) |  AmazonEC2ReadOnlyAccess  |  |
| ALB | AmazonEC2ReadOnlyAccess |  |
| NLB | AmazonEC2ReadOnlyAccess |  |
| RDS | AmazonRDSReadOnlyAccess |  |
| ElastiCache | AmazonElastiCacheReadOnlyAccess |  |
| Redshift | AmazonRedshiftReadOnlyAccess |  |
| Lambda *1 | AWSLambda_ReadOnlyAccess |  |
| SQS | AmazonSQSReadOnlyAccess<br>`sqs:ListQueueTags` *2 |  |
| DynamoDB | AmazonDynamoDBReadOnlyAccess |  |
| CloudFront *1 | CloudFrontReadOnlyAccess |  |
| API Gateway *1 | `apigateway:GET` | Specify a resource policy like `arn:aws:apigateway:ap-northeast-1::/*`.<br>You cannot limit integration targets by resource policy. |
| Kinesis Data Streams *1 | AmazonKinesisReadOnlyAccess |  |
| S3 *1 | AmazonS3ReadOnlyAccess | Request metrics must be enabled in S3.<br>See <a href="https://docs.aws.amazon.com/AmazonS3/latest/userguide/configure-request-metrics-bucket.html">Creating a CloudWatch metrics configuration for all the objects in your bucket</a> and set the filter name as `EntireBucket`. |
| OpenSearch Service *1 | AmazonOpenSearchServiceReadOnlyAccess<br>`elasticache:ListTagsForResource` *2 | You can also use AmazonESReadOnlyAccess. |
| ECS Cluster *1 | `ecs:Describe*` <br> `ecs:List*` |  |
| SES *1 | AmazonSESReadOnlyAccess <br> `ses:Describe*` |  |
| Step Functions *1 | AWSStepFunctionsReadOnlyAccess<br>`states:ListTagsForResource` *2 |  |
| EFS *1 | AmazonElasticFileSystemReadOnlyAccess |  |
| Kinesis Data Firehose *1 | AmazonKinesisFirehoseReadOnlyAccess |  |
| AWS Batch *1 | `batch:Describe*` <br> `batch:List*` |  |
| WAF *1 | AWSWAFReadOnlyAccess |  |
| Billing *1 | AWSBudgetsReadOnlyAccess |  |
| Route 53 *1 | AmazonRoute53ReadOnlyAccess |  |
| Amazon Connect *1 | AmazonConnectReadOnlyAccess |  |
| DocumentDB | AmazonRDSReadOnlyAccess |  |
| CodeBuild *1 | `codebuild:BatchGetProjects` <br> `codebuild:ListProjects` |  |
| Athena *1 | `athena:ListWorkGroups` <br> `athena:ListTagsForResource` |  |

Due to AWS specifications, the quota for the number of policies that can be attached to a single IAM role/IAM user is limited to 10. If necessary, please request a quota increase from AWS.

* [Request a service quota increase - AWS Support](https://docs.aws.amazon.com/awssupport/latest/user/create-service-quota-increase.html)

If you want to configure all the permissions used in AWS Integration, please refer to the <a href="#iam_policy">IAM policies used in AWS Integration</a> section.

Reference: Granting policies

![](https://cdn-ak2.f.st-hatena.com/images/fotolife/m/mackerelio/20170912/20170912165028.png)

Reference: Granting actions (Inline Policies)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160616/20160616150058.png)
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160616/20160616150059.png)

<h3 id="setting_check_host">3. Confirm the host</h3>
After a short while, your AWS cloud product will be registered as a host in Mackerel and begin posting metrics. By creating monitoring rules, you can also be notified of alerts. For more information, see [Setting up monitoring and alerts](https://mackerel.io/docs/entry/howto/alerts).

<h2 id="auto-new-metrics-addition">Configure automatic new metrics addition</h2>

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20231101/20231101163216.png)

Mackerel may add new metrics as each AWS service is updated.

By turning on "Add new metrics automatically", you can avoid having to select metrics for retrieval on the AWS integration settings page each time they are added.

The fee for using AWS integration is determined by the number of hosts that are linked and the number of metrics that are posted in excess of the limit per host (see [How usage fees are calculated](https://support.mackerel.io/hc/en-us/articles/31304727432729) for details).

If you want to avoid unexpected increases in usage charges, turn off "Add new metrics automatically". Metrics added thereafter will not be automatically acquired, so please select the required metrics individually.

<h2 id="auto-retirement">Configure automatic retirement</h2>

Some services offer the option to retire hosts from Mackerel upon removal of AWS resources. To enable automatic retirement, check the `Enable automatic retirement` checkbox in the configuration page.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230519/20230519115922.png)

Automatic retirement of AWS integration provides automatic retirement of the associated host when it is determined that a resource linked to Mackerel has been deleted. Therefore, automatic retirement will not occur in the following cases:

- Resources that had already been excluded by the tag filter deleted
- The service was linked in the past, but is now disabled in the integration settings
- The service was linked in the past, but now the entire integration setup has been deleted

In addition, AWS integration check only once when it detects the deletion of an AWS resource, so those that have enabled link after the detection of a deletion are not automatically retired. If the same host is linked to multiple integration settings, it will be automatically retired when any one of the settings is determined to be deleted.

<h2 id="select-metric">Specify metrics to retrieve</h2>

You can reduce metric charges and the number of CloudWatch API requests by configuring the system to exclude certain metrics.

<h3>1. Grant permissions to specify the metrics to be retrieved (for some products only)</h3>

Some products require additional actions to specify the metrics to be retrieved. For details, refer to [Grant policies](#setting_policy).

<h3>2. Configure settings to specify the metrics to be retrieved</h3>

Select the metrics you wish to retrieve in the Mackerel configuration page. Uncheck any unwanted metrics and save your settings.

For example, to not retrieve `kinesis.latency.#.minimum` for Kinesis Data Streams, uncheck the box as shown below. This configuration limits the minimum for `GetRecords.Latency`, `PutRecord.Latency`, and `PutRecords.Latency`, reducing up to 3 metrics.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200217/20200217172630.png)

<h2 id="tag">Specify tags to filter hosts to register</h2>
AWS cloud products to be registered as hosts and retrieve metrics can be filtered based on the tags appended by AWS.

<h3>1. Grant permissions to retrieve tags (for some products only)</h3>

Some products require additional actions to filter hosts to register by tags. For details, refer to [Grant policies](#setting_policy).

<h3>2. Configure settings to filter by tags</h3>
Specify tags in the Mackerel configuration page. Confirm the number of linked hosts and save your settings.

By specifying the tag as `service:foo, service:bar`, instances tagged with a key of `service` and value of `foo` or a key of `service` and value of `bar` will be targeted. If the key or value contains characters such as a colon `:` or comma `,`, enclose it with quotation marks (`"` or `'`). For example, if the key is `service:role` and the value is `foo,bar`, specify it as `"service:role": "foo,bar"`.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160622/20160622115520.png)

<h2 id="resource_tag">Assigning roles by tags</h2>

There is a feature that is about assigning roles by tags.
This feature enables tags with the label `mackerel-integration` on AWS resources to be automatically imported as Mackerel services and roles.
The tag format is described as follows:

* Key: `mackerel-integration`
* Value: `<Service>:<Role> [ / <Service>:<Role> ...]`

To assign multiple roles to a host, specify them by separating with `/`. If a specified Mackerel service and role doesn’t exist, it will be automatically created for you.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220215/20220215164116.png)

Please note that hosts registered with the `mackerel-integration` tag will not be assigned the default roles specified in the AWS Integration settings of the web console.

### When the tag settings are not reflected

Upon noticing that the contact of the tag hasn’t been reflected in the role, please check if all of the following items are applicably:

* The tag value you assigned must be valid according to Mackerel’s service and role naming rules
* AWS Integration supports the `mackerel-integration` tag for the AWS service
  * Please refer to the relevant documentation for the latest support status

Please note that it may take several hours for the role to be reflected on the host.

<h2 id="iam_policy">IAM policies used in AWS Integration</h2>

The following is a list of all permissions used in AWS Integration.
Create and attach your own policies or specify them in Inline Policies.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "apigateway:Get*",
                "application-autoscaling:DescribeScalableTargets",
                "athena:ListWorkGroups",
                "athena:ListTagsForResource",
                "batch:Describe*",
                "batch:ListJobs",
                "budgets:ViewBudget",
                "cloudfront:Get*",
                "cloudfront:List*",
                "cloudwatch:Get*",
                "cloudwatch:List*",
                "codebuild:BatchGetProjects",
                "codebuild:ListProjects",
                "connect:ListInstances",
                "dynamodb:Describe*",
                "dynamodb:List*",
                "ds:DescribeDirectories",
                "ec2:DescribeInstances",
                "ecs:DescribeClusters",
                "ecs:List*",
                "elasticache:Describe*",
                "elasticache:ListTagsForResource",
                "elasticfilesystem:Describe*",
                "elasticloadbalancing:Describe*",
                "es:DescribeDomain",
                "es:List*",
                "firehose:DescribeDeliveryStream",
                "firehose:List*",
                "kinesis:Describe*",
                "kinesis:List*",
                "lambda:GetFunctionConfiguration",
                "lambda:List*",
                "rds:Describe*",
                "rds:ListTagsForResource",
                "redshift:Describe*",
                "route53:List*",
                "s3:ListAllMyBuckets",
                "s3:GetBucketLocation",
                "s3:GetBucketLogging",
                "s3:GetBucketTagging",
                "s3:GetEncryptionConfiguration",
                "s3:GetMetricsConfiguration",
                "ses:DescribeActiveReceiptRuleSet",
                "ses:GetSendQuota",
                "ses:ListIdentities",
                "sqs:GetQueueAttributes",
                "sqs:List*",
                "states:DescribeStateMachine",
                "states:List*",
                "waf-regional:Get*",
                "waf-regional:List*",
                "waf:Get*",
                "waf:List*",
                "wafv2:GetWebACL",
                "wafv2:List*"
            ],
            "Effect": "Allow",
            "Resource": "*"
        }
    ]
}
```

<h2 id="faq">FAQ</h2>

<h3 id="permission-check">Regarding the access key's authority check and CreateInternetGateway</h3>

To check whether the access key registered by the user has unnecessarily strong permissions, AWS integration periodically executes the CreateInternetGateway API in dry-run mode. If the access key has more permissions than necessary, metrics will not be collected and posted, so please be careful.

The reason for periodic checks is that policies may be added to the access key, resulting in stronger permissions. As a result, logs related to CreateInternetGateway may be output to your AWS environment.

### Regarding retiring hosts linked with AWS Integration

By configuring the integration above, AWS cloud products complying with the target service and tag conditions are automatically integrated with Mackerel and registered as a host. On the other hand, if [the service does not support automatic retirement or automatic retirement feature is disabled](#auto-retirement), hosts in Mackerel will not be deleted (retired) simply by deleting instances etc. in AWS. In order to remove hosts integrated with AWS from Mackerel's managed targets, you need to retire them separately.

[https://mackerel.io/docs/entry/howto/host-retirement:embed:cite]

Even if a host is not retired, host information will remain and hosts without metric posts will not be subject to billing.

<h3 id="plugin-custom-identifier">Regarding the aggregation of monitoring content with plugins in integrated hosts</h3>

`custom_identifier` can be specified in the settings of mackerel-agent's custom metrics and check monitoring plugins. `custom_identifier` is a mechanism that allows the user to assign a distinct identifier to a host. Using this, it is possible to aggregate metrics and check monitors that have been posted from mackerel-agent installed on another machine as a part of the AWS integration host. `custom_identifier` is specified in the configuration of compatible plugins.

For example, the endpoint in the case of Amazon RDS, and the DNS Name in the case of ELB, will be the `custom_identifier`string.

#### Example uses
Here are two example uses. In either case, the agent needs to be restarted after adding to the mackerel-agent configuration file.

The first example is of MySQL monitoring using the [mackerel-plugin-mysql](https://github.com/mackerelio/mackerel-plugin-mysql) plugin for Amazon RDS. Metrics retrieved by the plugin can be aggregated as custom metrics for the RDS host by adding the plugin configuration which includes the `custom_identifier` as shown below to the mackerel-agent.conf.

```toml
[plugin.metrics.mysql]
command = ["mackerel-plugin-mysql", "-host", "<RDS endpoint>", "-username", "user", "-password", "pass"]
custom_identifier = "<RDS endpoint>"
```

The second example is of OpenSearch monitoring using the Amazon OpenSearch Service and the [check-elasticsearch](https://github.com/mackerelio/go-check-plugins/tree/master/check-elasticsearch) plugin. OpenSearch Service Cluster Health Checks can be aggregated as check monitoring for OpenSearch Service hosts by adding the plugin configuration which includes the `custom_identifier` as shown below to the mackerel-agent.conf.

```toml
[plugin.checks.elasticsearch]
command = ["check-elasticsearch", "-s", "https", "-H", "<OpenSearch Service endpoint>", "-p", "443"]
custom_identifier = "<OpenSearch Service domain ARN>"
```
