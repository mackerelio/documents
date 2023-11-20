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

[EC2](https://mackerel.io/docs/entry/integrations/aws/ec2)・[ELB (CLB)](https://mackerel.io/docs/entry/integrations/aws/elb)・[ALB](https://mackerel.io/docs/entry/integrations/aws/alb)・[NLB](https://mackerel.io/docs/entry/integrations/aws/nlb)・[RDS](https://mackerel.io/docs/entry/integrations/aws/rds)・[ElastiCache](https://mackerel.io/docs/entry/integrations/aws/elasticache)・[Redshift](https://mackerel.io/docs/entry/integrations/aws/redshift)・[Lambda](https://mackerel.io/docs/entry/integrations/aws/lambda)・[SQS](https://mackerel.io/docs/entry/integrations/aws/sqs)・[DynamoDB](https://mackerel.io/docs/entry/integrations/aws/dynamodb)・[CloudFront](https://mackerel.io/docs/entry/integrations/aws/cloudfront)・[API Gateway](https://mackerel.io/docs/entry/integrations/aws/apigateway)・[Kinesis Data Streams](https://mackerel.io/docs/entry/integrations/aws/kinesis)・[S3](https://mackerel.io/docs/entry/integrations/aws/s3)・[Elasticsearch Service](https://mackerel.io/docs/entry/integrations/aws/es)・[ECS](https://mackerel.io/docs/entry/integrations/aws/ecs)・[SES](https://mackerel.io/docs/entry/integrations/aws/ses)・[Step Functions](https://mackerel.io/docs/entry/integrations/aws/states)・[EFS](https://mackerel.io/docs/entry/integrations/aws/efs)・[Kinesis Data Firehose](https://mackerel.io/docs/entry/integrations/aws/firehose)・[Batch](https://mackerel.io/docs/entry/integrations/aws/batch)・[WAF](https://mackerel.io/docs/entry/integrations/aws/waf)・[Billing](https://mackerel.io/docs/entry/integrations/aws/billing)・[Route 53](https://mackerel.io/docs/entry/integrations/aws/route53)・[Connect](https://mackerel.io/docs/entry/integrations/aws/connect)・[DocumentDB](https://mackerel.io/docs/entry/integrations/aws/docdb)・[CodeBuild](https://mackerel.io/docs/entry/integrations/aws/codebuild)


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

Grant the policies listed below for the role.
Be sure to use the policies and actions listed below and be careful not to grant FullAccess permission.

Also, the maximum number of policies that can be attached to one IAM role is limited to 10. This is a specification of AWS. If necessary, you can apply to AWS for an upper limit extension.

If you want to configure all the permissions used in AWS Integration, please refer this list of <a href="#iam_policy">IAM policies used in AWS Integration</a>.

| AWS Cloud Products | Policy / Action | Note |
| :--- | :--- | :--- |
| EC2 |  AmazonEC2ReadOnlyAccess  |  |
| ELB (CLB) |  AmazonEC2ReadOnlyAccess  |  |
| ALB | AmazonEC2ReadOnlyAccess |  |
| NLB | AmazonEC2ReadOnlyAccess |  |
| RDS | AmazonRDSReadOnlyAccess |  |
| ElastiCache | AmazonElastiCacheReadOnlyAccess |  |
| Redshift | AmazonRedshiftReadOnlyAccess |  |
| Lambda [*1](#single-product) | AWSLambda_ReadOnlyAccess |  |
| SQS | AmazonSQSReadOnlyAccess |  |
| DynamoDB | AmazonDynamoDBReadOnlyAccess |  |
| CloudFront [*1](#single-product) | CloudFrontReadOnlyAccess |  |
| API Gateway [*1](#single-product) | `apigateway:GET` | Specify a resource policy like so `arn:aws:apigateway:ap-northeast-1::/*`.<br> You can not limit integration targets by resource policy. |
| Kinesis Data Streams [*1](#single-product) | AmazonKinesisReadOnlyAccess |  |
| S3 [*1](#single-product) | AmazonS3ReadOnlyAccess | Request metrics must be enabled in the S3 configuration.<br>See <a href="https://docs.aws.amazon.com/AmazonS3/latest/userguide/configure-request-metrics-bucket.html">Creating a CloudWatch metrics configuration for all the objects in your bucket</a> and set the filter name as `EntireBucket`. |
| Elasticsearch Service [*1](#single-product) | AmazonESReadOnlyAccess |  |
| ECS [*1](#single-product) | `ecs:Describe*` <br> `ecs:List*` |  |
| SES [*1](#single-product) | AmazonSESReadOnlyAccess <br> `ses:Describe*` |  |
| Step Functions [*1](#single-product) | AWSStepFunctionsReadOnlyAccess |  |
| EFS [*1](#single-product) | AmazonElasticFileSystemReadOnlyAccess |  |
| Kinesis Data Firehose [*1](#single-product) | AmazonKinesisFirehoseReadOnlyAccess |  |
| Batch [*1](#single-product) | `batch:Describe*` <br> `batch:List*` |  |
| WAF [*1](#single-product) | AWSWAFReadOnlyAccess |  |
| Billing [*1](#single-product) | AWSBudgetsReadOnlyAccess |  |
| Route 53 [*1](#single-product) | AmazonRoute53ReadOnlyAccess |  |
| Connect [*1](#single-product) | AmazonConnectReadOnlyAccess |  |
| DocumentDB | AmazonRDSReadOnlyAccess |  |
| CodeBuild [*1](#single-product) | `codebuild:BatchGetProjects` <br> `codebuild:ListProjects` |  |

<p id="single-product">*1 <code>CloudWatchReadOnlyAccess</code> is required in addition to the required policies/actions for a single linkage of the relevant AWS products.</p>

Furthmore, AWS Integration lets you filter using tags. However, additional policies need to be added to use this function with ElastiCache, SQS or Step Functions.
For more details, refer to <a href="#tag">Filter by tag</a>.

If you exclude a metric, you will still need to grant an additional policy as well as the ability to filter by tag, as it refers to AWS tags.
See the <a href="#select-metric">Restrict which metrics to retrieve</a> section for more information.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20171025/20171025153435.png)

<h3 id="setting_check_host">3. Confirm the host</h3>
After a short while, your AWS cloud product will be registered as a host in Mackerel and begin posting metrics. By creating monitoring rules, you can also be notified of alerts. For more information, see [Setting up monitoring and alerts](https://mackerel.io/docs/entry/howto/alerts).

<h2 id="auto-new-metrics-addition">Configure automatic new metrics addition</h2>

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20231101/20231101163216.png)

Mackerel may add new metrics as each AWS service is updated.

By turning on "Add new metrics automatically", you can avoid having to select metrics for retrieval on the AWS integration settings page each time they are added.

The usage fee for AWS integration is determined by the number of hosts calculated from the number of metrics acquired (Reference: [Handling of host conversion when plan limits are exceeded](https://support.mackerel.io/hc/en-us/articles/360040109431-Handling-of-host-conversion-when-plan-limits-are-exceeded)). Therefore, when a large number of metrics are added, the number of hosts will increase due to exceeding the upper limit, which may also affect usage charges.

If you want to avoid unexpected increases in usage charges, turn off "Add new metrics automatically". Metrics added thereafter will not be automatically acquired, so please select the required metrics individually.

<h2 id="auto-retirement">Configure automatic retirement</h2>

Some services offer the option to retire hosts from Mackerel upon removal of AWS resources. To enable automatic retirement, check the `Enable automatic retirement` checkbox in the configuration page.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230519/20230519115922.png)

Automatic retirement of AWS integration provides automatic retirement of the associated host when it is determined that a resource linked to Mackerel has been deleted. Therefore, automatic retirement will not occur in the following cases:

- Resources that had already been excluded by the tag filter deleted
  - Exception: if the target service is EC2, it will be retired even if it has already been excluded by the tag
- The service was linked in the past, but is now disabled in the integration settings
- The service was linked in the past, but now the entire integration setup has been deleted

In addition, AWS integration check only once when it detects the deletion of an AWS resource, so those that have enabled link after the detection of a deletion are not automatically retired. If the same host is linked to multiple integration settings, it will be automatically retired when any one of the settings is determined to be deleted.

<h2 id="select-metric">Limit metrics retrieved</h2>

You can reduce the number of hosts and cost of the CloudWatch API by limiting the metrics to be retrieved.

<h3>1. Grant permissions to limit the metrics to be retrieved</h3>
In order to restrict the metrics to be retrieved, additional permissions for the following actions are required in addition to the policies granted for the AWS Integration setup.

- `elasticache:ListTagsForResource`
- `sqs:ListQueueTags`
- `states:ListTagsForResource`

To add policies, use the Inline Policies process.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160616/20160616150058.png)
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160616/20160616150059.png)

<h3>2. Configure settings to limit the metrics to be retrieved</h3>

The number of hosts is calculated using a moving average of the past month. For more information about that, refer to the FAQ page [Calculating the number of hosts](https://support.mackerel.io/hc/en-us/articles/360039702912).

Select the metrics you wish to retrieve in the Mackerel configuration page. Uncheck any unwanted metrics and save them.

For example, you can specify to not retrieve a metric like `kinesis.latency.#.minimum` by simply unchecking the box as shown in the image below. This configuration limits the minimum for `GetRecords.Latency`,`PutRecord.Latency`, and `PutRecords.Latency` and reduces a maximum of 3 metrics.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200217/20200217172630.png)

<h2 id="tag">Filter by tag</h2>
AWS cloud products to be registered as hosts and retrieve metrics can be filtered based on the tags appended by AWS.

<h3>1. Grant permissions for tag retrieval</h3>
In order to filter using an AWS tag, permission for the following actions is required in addition to the policies that were granted to configure AWS integration.

- `elasticache:ListTagsForResource`
- `sqs:ListQueueTags`
- `states:ListTagsForResource`

To add policies, use the Inline Policies process.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160616/20160616150058.png)
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160616/20160616150059.png)

<h3>2. Tag filtering settings</h3>
You can specify tags from the Mackerel settings screen. Confirm the number of linked hosts and save your changes.

By specifying the tag as `service:foo, service:bar`, instances tagged with a key of `service` and value of `foo` or a key of `service` and value of `bar` will be targeted. If the key or value contains characters such as a colon `:` or comma `,`, enclose it with quotation marks (`"` or `'`). For example, if the key is `service:role` and the value is `foo,bar`, specify it as `"service:role":"foo,bar"`.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160622/20160622115520.png)

<h2 id="resource_tag">Assigning roles by tags</h2>

There is a feature that is about assigning roles by tags.
This feature enables tags with the label `mackerel-integration` on AWS resources to be automatically imported as Mackerel services and roles.
The tag format is described as follows:

* Key: `mackerel-integration`
* Value: `<Service>:<Role> [ / <Service>:<Role> ...]`
  * Service: `/^[a-zA-Z0-9_-]+$/`
  * Role: `/^[a-zA-Z0-9_-]+$/`

To assign multiple roles to a host, specify them by separating with `/`. If a specified Mackerel service and role doesn’t exist, it will be automatically created for you.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220215/20220215164116.png)

Additional policies need to be added to use this feature with ElastiCache, SQS or Step Functions. For more details, refer to **Grant permissions for tag retrieval** section of <a href="#tag">Filter by tag</a>.

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
                "es:DescribeElasticsearchDomain",
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

### Regarding the access key’s authority check and CreateInternetGateway

In order to check whether or not the access key registered by the user has an unnecessarily strong authority, AWS Integration periodically calls the CreateInternetGateway API in dry-run. Metrics will not be obtained or posted if the access key has an authority more than necessary so be careful. The reason why checks still periodically occur after registration is because there is a possibility that policies will get added to the access key, resulting in a key with stronger authority.

### Regarding retiring hosts linked with AWS Integration

By configuring the integration above, AWS cloud products complying with the target service and tag conditions are automatically integrated with Mackerel and registered as a host. On the other hand, if [the service does not support automatic retirement or automatic retirement feature is disabled](#auto-retirement), hosts in Mackerel will not be deleted (retired) simply by deleting instances etc. in AWS. In order to remove hosts integrated with AWS from Mackerel's managed targets, you need to retire them separately.

[https://mackerel.io/docs/entry/howto/host-retirement:embed:cite]

Even if a host is not retired, host information will remain and hosts without metric posts will not be subject to billing.

<h3 id="plugin-custom-identifier">Regarding the aggregation of monitoring content with plugins in integrated hosts</h3>

`custom_identifier` can be specified in the settings of mackerel-agent's custom metrics and check monitoring plugins. `custom_identifier` is a mechanism that allows the user to assign a distinct identifier to a host. Using this, it is possible to aggregate metrics and check monitors that have been posted from mackerel-agent installed on another machine as a part of the AWS integration host. `custom_identifier` is specified in the configuration of compatible plugins.

For example, the endpoint in the case of Amazon RDS, and the DNS Name in the case of ELB, will be the `custom_identifier`string.

#### Example uses
Here are two example uses. In either case, the agent needs to be restarted after adding to the mackerel-agent configuration file.

The first example is of MySQL monitoring using the [mackerel-plugin-mysql](https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-mysql) plugin for Amazon RDS. Metrics retrieved by the plugin can be aggregated as custom metrics for the RDS host by adding the plugin configuration which includes the `custom_identifier` as shown below to the mackerel-agent.conf.

```
[plugin.metrics.mysql]
command = ["mackerel-plugin-mysql", "-host", "<RDS endpoint>", "-username", "user", "-password", "pass"]
custom_identifier = "<RDS endpoint>"
```

The second example is of Elasticsearch monitoring using the Amazon Elasticsearch Service and the [check-elasticsearch](https://github.com/mackerelio/go-check-plugins/tree/master/check-elasticsearch) plugin. Elasticsearch Service Cluster Health Checks can be aggregated as check monitoring for ElasticsearchService hosts by adding the plugin configuration which includes the `custom_identifier` as shown below to the mackerel-agent.conf.

```
[plugin.checks.elasticsearch]
command = ["check-elasticsearch", "-s", "https", "-H", "<Elasticsearch Service endpoint>", "-p", "443"]
custom_identifier = "<Elasticsearch Service domain ARN>"
```
