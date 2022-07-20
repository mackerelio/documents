---
Title: AWS Integration
Date: 2016-05-12T16:39:45+09:00
URL: https://mackerel.io/docs/entry/integrations/aws
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6653812171395777794
---

Using AWS integration, you can manage AWS cloud products as a host of Mackerel and monitor its metrics. This function is only offered in the Trial plan and Standard plan.

Each AWS cloud product will be registered as one host in Mackerel and therefore be counted as a billable host.
The types of hosts consist of standard hosts for EC2 and micro hosts for other products.
Additionally, the API of AWS will be called every 5 minutes for each targeted metric to be obtained. Please take note, for this reason, an [Amazon CloudWatch API usage fee](https://aws.amazon.com/jp/cloudwatch/pricing/) may occur.
To limit the metrics that are retrieved, refer to the [Limit metrics retrieved](#select-metric) section below.

Currently, the following AWS cloud products are supported. For information on obtaining metrics, please refer to each individual document.


[EC2](https://mackerel.io/docs/entry/integrations/aws/ec2)・[ELB (CLB)](https://mackerel.io/docs/entry/integrations/aws/elb)・[ALB](https://mackerel.io/docs/entry/integrations/aws/alb)・[NLB](https://mackerel.io/docs/entry/integrations/aws/nlb)・[RDS](https://mackerel.io/docs/entry/integrations/aws/rds)・[ElastiCache](https://mackerel.io/docs/entry/integrations/aws/elasticache)・[Redshift](https://mackerel.io/docs/entry/integrations/aws/redshift)・[Lambda](https://mackerel.io/docs/entry/integrations/aws/lambda)・[SQS](https://mackerel.io/docs/entry/integrations/aws/sqs)・[DynamoDB](https://mackerel.io/docs/entry/integrations/aws/dynamodb)・[CloudFront](https://mackerel.io/docs/entry/integrations/aws/cloudfront)・[API Gateway](https://mackerel.io/docs/entry/integrations/aws/apigateway)・[Kinesis Data Streams](https://mackerel.io/docs/entry/integrations/aws/kinesis)・[S3](https://mackerel.io/docs/entry/integrations/aws/s3)・[Elasticsearch Service](https://mackerel.io/docs/entry/integrations/aws/es)・[ECS](https://mackerel.io/docs/entry/integrations/aws/ecs)・[SES](https://mackerel.io/docs/entry/integrations/aws/ses)・[Step Functions](https://mackerel.io/docs/entry/integrations/aws/states)・[EFS](https://mackerel.io/docs/entry/integrations/aws/efs)・[Kinesis Data Firehose](https://mackerel.io/docs/entry/integrations/aws/firehose)・[Batch](https://mackerel.io/docs/entry/integrations/aws/batch)・[WAF](https://mackerel.io/docs/entry/integrations/aws/waf)・[Billing](https://mackerel.io/docs/entry/integrations/aws/billing)・[Route 53](https://mackerel.io/docs/entry/integrations/aws/route53)・[Connect](https://mackerel.io/docs/entry/integrations/aws/connect)・[DocumentDB](https://mackerel.io/docs/entry/integrations/aws/docdb)・[CodeBuild](https://mackerel.io/docs/entry/integrations/aws/codebuild)


<h2 id="setting">Integration method</h2>
There are two ways to integrate AWS Integration.

- Configure the IAM role to allow access only from the AWS account of the Mackerel system and authenticate with AssumeRole
- Configure the Access Key ID and Secret Access Key

From a security standpoint, we strongly recommend configuring with the IAM role.

<h3>How to configure an IAM role</h3>
<h4>1. Creating a role with the IAM Management Console</h4>
Create a new role with the <a href="https://console.aws.amazon.com/iam" target="_blank">IAM Management Console</a>.
Select `Another AWS account` from the role types.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190528/20190528124827.png)

Click the 'Create' button in the [Mackerel AWS Integration Settings page](https://mackerel.io/my?tab=awsIntegration) to get an External ID. Enter `217452466226` as the authorized Account ID. Select the `Require external ID` option and specify the External ID obtained from Mackerel's configuration page. The Mackerel system uses the account to access the user’s role. With this configuration, only the Mackerel account can access the created role. Create the role without checking `Require MFA`.

Grant the policies listed below for the role. Be careful not to grant FullAccess permission. Also, the maximum number of policies that can be attached to one IAM role is limited to 10. This is a specification of AWS. If necessary, you can apply to AWS for an upper limit extension.

If you want to configure all the permissions used in AWS Integration, please reference this list of <a href="#iam_policy">IAM policies used in AWS Integration</a>.

- `AmazonEC2ReadOnlyAccess`
- `AmazonElastiCacheReadOnlyAccess`
- `AmazonRDSReadOnlyAccess`
  - Specify for RDS or DocumentDB.
- `AmazonRedshiftReadOnlyAccess`
- `AWSLambda_ReadOnlyAccess`
- `AmazonSQSReadOnlyAccess`
- `AmazonDynamoDBReadOnlyAccess`
- `CloudFrontReadOnlyAccess`
- `apigateway:GET / apigateway:OPTIONS`
    - Specify a resource policy like so `arn:aws:apigateway:ap-northeast-1::/*` . You can not limit integration targets by resource policy.
- `AmazonKinesisReadOnlyAccess`
- `AmazonS3ReadOnlyAccess`
- `AmazonESReadOnlyAccess`
- `ecs:Describe* / ecs:List*`
- `AmazonSESReadOnlyAccess / ses:Describe*`
- `codebuild:BatchGetProjects / codebuild:ListProjects`
- `AWSStepFunctionsReadOnlyAccess`
- `AmazonElasticFileSystemReadOnlyAccess`
- `AmazonKinesisFirehoseReadOnlyAccess`
- `batch:Describe* / batch:List*`
- `AWSWAFReadOnlyAccess`
- `AWSBudgetsReadOnlyAccess`
- `AmazonRoute53ReadOnlyAccess`
- `AmazonConnectReadOnlyAccess`
- `CloudWatchReadOnlyAccess`（When only configuring CloudFront, API Gateway, Kinesis Data Streams, S3, Elasticsearch Service, ECS, SES, Step Functions, EFS, Kinesis Data Firehose, Batch, WAF, Billing, Route 53, Lambda, Connect or CodeBuild）

Furthmore, AWS Integration lets you filter using tags (as is mentioned further down in this document). However, additional policies need to be added to use this function with ElastiCache, SQS or Step Functions.
For more details, refer to <a href="#tag">Filter by tag</a>.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20171025/20171025153435.png)

Fill in the name and create a role.
We recommend assigning an easy-to-understand name like `MackerelAWSIntegrationRole`  for use in Mackerel’s AWS integration.

<h4>2. Register an ARN role in Mackerel</h4>
Register an ARN role from the same Mackerel screen where we just acquired the External ID.

<h4>3. Confirm the host</h4>
After a short while, your AWS cloud product will be registered as a host in Mackerel and begin posting metrics. By creating monitoring rules, you can also be notified of alerts. For more information, see [Setting up monitoring and alerts](https://mackerel.io/docs/entry/howto/alerts).

<h3>Configure the Access Key ID and Secret Access Key</h3>
From a security standpoint, the following method is not recommended.
<h4>1. Creating a user with the IAM Management Console</h4>
Create a new user with the <a href="https://console.aws.amazon.com/iam" target="_blank">IAM Management Console</a>. We recommend assigning an easy-to-understand name like `MackerelAWSIntegrationUser` for use in Mackerel’s AWS integration.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20160512/20160512162316.png)

<h4>2. Registering the Access Key in Mackerel</h4>
Register the Access Key ID and Secret Access Key (displayed on the screen when creating the account) [in Mackerel](https://mackerel.io/my?tab=awsIntegration). Be careful not to mistake the organization to be registered.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20160512/20160512162642.png)

<h4>3. Granting policies</h4>
Grant the policies listed below for the newly created user. Be careful not to grant FullAccess permission. Also, the maximum number of policies that can be attached to one IAM user is limited to 10. This is a specification of AWS. If necessary, you can apply to AWS for an upper limit extension.

If you want to configure all the permissions used in AWS Integration, please reference this list of <a href="#iam_policy">IAM policies used in AWS Integration</a>.

- `AmazonEC2ReadOnlyAccess`
- `AmazonElastiCacheReadOnlyAccess`
- `AmazonRDSReadOnlyAccess`
  - Specify for RDS or DocumentDB.
- `AmazonRedshiftReadOnlyAccess`
- `AWSLambda_ReadOnlyAccess`
- `AmazonSQSReadOnlyAccess`
- `AmazonDynamoDBReadOnlyAccess`
- `CloudFrontReadOnlyAccess`
- `apigateway:GET / apigateway:OPTIONS`
    - Specify a resource policy like so `arn:aws:apigateway:ap-northeast-1::/*` . You can not limit integration targets by resource policy.
- `AmazonKinesisReadOnlyAccess`
- `AmazonS3ReadOnlyAccess`
- `AmazonESReadOnlyAccess`
- `ecs:Describe* / ecs:List*`
- `AmazonSESReadOnlyAccess / ses:Describe*`
- `codebuild:BatchGetProjects / codebuild:ListProjects`
- `AWSStepFunctionsReadOnlyAccess`
- `AmazonElasticFileSystemReadOnlyAccess`
- `AmazonKinesisFirehoseReadOnlyAccess`
- `batch:Describe* / batch:List*`
- `AWSWAFReadOnlyAccess`
- `AWSBudgetsReadOnlyAccess`
- `AmazonRoute53ReadOnlyAccess`
- `AmazonConnectReadOnlyAccess`
- `CloudWatchReadOnlyAccess`（When only configuring CloudFront, API Gateway, Kinesis Data Streams, S3, Elasticsearch Service, ECS, SES, Step Functions, EFS, Kinesis Data Firehose, Batch, WAF, Billing, Route 53, Lambda, Connect or CodeBuild）

Furthmore, AWS Integration lets you filter using tags (as is mentioned further down in this document). However, additional policies need to be added to use this function with ElastiCache or SQS.

For more details, refer to <a href="#tag">Filter by tag</a>.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20171025/20171025153435.png)

<h4>4. Verify the host</h4>
After a short while, your AWS cloud product will be registered as a host in Mackerel and begin posting metrics. By creating monitoring rules, you can also be notified of alerts.
For more information, see [Setting up monitoring and alerts](https://mackerel.io/docs/entry/howto/alerts).

<h2 id="select-metric">Limit metrics retrieved</h2>

You can reduce the number of hosts and cost of the CloudWatch API by limiting the metrics to be retrieved. The number of hosts is calculated using a moving average of the past month. For more information about that, refer to the FAQ page [Calculating the number of hosts](https://support.mackerel.io/hc/en-us/articles/360039702912).

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
This feature enables tags with the label `mackerel-integration` on AWS resources to be automatically imported as Mackarel services and roles.
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

By configuring the integration above, AWS cloud products complying with the target service and tag conditions are automatically integrated with Mackerel and registered as a host. On the other hand, hosts in Mackerel will not be deleted (retired) simply by deleting instances etc. in AWS. In order to remove hosts integrated with AWS from Mackerel's managed targets, you need to retire them separately.

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
