---
Title: AWS Integration
Date: 2020-06-10T18:00:00+09:00
URL: https://mackerel.io/api-docs/entry/aws-integration
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/26006613583249210
CustomPath: aws-integration
---

<ul class="internal-nav">
  <li><a href="#list">List AWS Integration Settings</a></li>
  <li><a href="#get">Get AWS Integration Settings</a></li>
  <li><a href="#create">Register AWS Integration Settings</a></li>
  <li><a href="#update">Update AWS Integration Settings</a></li>
  <li><a href="#delete">Delete AWS Integration Settings</a></li>
  <li><a href="#generate-external-id">Generate AWS Integration External ID</a></li>
  <li><a href="#excludable-metrics">List Excludable Metrics for AWS Integration</a></li>
</ul>

<h2 id="list">List AWS Integration Settings</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/aws-integrations</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

#### Success

Returns a list of AWS Integration settings.

```json
{
  "aws_integrations": [<aws_integration>, <aws_integration>, ...]
}
```

<i>`<aws_integration>`</i> is an object that holds the following keys.

| KEY             | TYPE              | DESCRIPTION                                                            |
| --------        | ------            | -----------                                                            |
| `id`            | *string*          | AWS Integration setting ID                                           |
| `name`          | *string*          | AWS Integration setting name                                        |
| `memo`          | *string*          | notes related to the AWS Integration setting                                  |
| `key`           | *string* / *null* | AWS access key ID                                                   |
| `roleArn`       | *string* / *null* | AWS IAM role resource(ARN)                                         |
| `externalId`    | *string* / *null* | External ID                                                                 |
| `region`        | *string*          | AWS region                                                     |
| `includedTags`  | *string*          | list of tags separated by commas(,)                                       |
| `excludedTags`  | *string*          | list of excluded tags separated by commas(,)                                    |
| `services`      | *services*        | integration settings of each AWS service                                  |

<i>`<services>`</i> is an object that holds the following keys.

```json
{
  <awsServiceName>: {
    "enable": <enable>,
    "role": <role>,
    "excludedMetrics": [<metric>, <metric>, ...],
    "retireAutomatically": <retireAutomatically>
  },
  <awsServiceName>: {
    "enable": <enable>,
    "role": <role>,
    "excludedMetrics": [<metric>, <metric>, ...]
  },
  ...
}
```

Enter the [AWS service identifier](#awsServiceNames) in place of <i>`<awsServiceName>`</i>.

| KEY                   | TYPE              | DESCRIPTION                                      |
| --------              | ------            | -----------                                      |
| `enable`              | *boolean*         | whether integration settings are enabled             |
| `role`                | *string* / *null* | full role name                                   |
| `excludedMetrics`     | *array[string]*   | list of excluded metrics                     |
| `includedMetrics`     | *array[string]*   | list of included metrics                     |

This <i>`role`</i> is the same as the <i>`role`</i> in [Register AWS Integration Settings](#create).

Only one or the other of <i>`excludedMetrics`</i> and <i>`includedMetrics`</i> will be returned for each AWS integration settings across all services.

With EC2 and RDS, the following additional keys are configured.

| KEY                   | TYPE              | DESCRIPTION                              |
| --------              | ------            | -----------                              |
| `retireAutomatically` | *boolean*         | whether automatic retirement is enabled                   |

#### Error

<table class="default api-error-table">
  <thead>
    <tr>
      <th class="status-code">STATUS CODE</th>
      <th class="description">DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>401</td>
      <td>when the API key is invalid</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="get">Get AWS Integration Settings</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/aws-integrations/<em>&lt;awsIntegrationId&gt;</em></code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

#### Success

Returns information on the AWS Integration settings. 

The format is the same as the object <i>`<aws_integration>`</i> in the <a href="#list">List AWS Integration Settings</a> API.

#### Error

<table class="default api-error-table">
  <thead>
    <tr>
      <th class="status-code">STATUS CODE</th>
      <th class="description">DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>401</td>
      <td>when the API key is invalid</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when AWS Integration settings for the specified ID do not exist</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="create">Register AWS Integration Settings</h2>

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/aws-integrations</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

This object holds the following keys.

| KEY              | TYPE              | DESCRIPTION                                                            |
| --------         | ------            | -----------                                                            |
| `name`           | *string*          | AWS Integration setting name[*1](#name)                                 |
| `memo`           | *string*          | notes related to the AWS Integration setting                                  |
| `key`            | *string*          | [optional] AWS access key ID[*2](#awsAccessInfo)                    |
| `secretKey`      | *string*          | [optional] AWS secret access key[*2](#awsAccessInfo)          |
| `roleArn`        | *string*          | [optional] AWS IAM role resource name(ARN)[*2](#awsAccessInfo)     |
| `externalId`     | *string*          | [optional] External ID[*2](#awsAccessInfo)                                  |
| `region`         | *string*          | AWS region                                                     |
| `includedTags`   | *string*          | list of tags separated by commas(,)[*3](#tags)                           |
| `excludedTags`   | *string*          | list of excluded tags separated by commas(,)[*3](#tags)                       |
| `services`       | *services*        | integration settings of each service                                    |

The object <i>`<services>`</i> is as shown below.

```json
{
  <awsServiceName>: {
    "enable": <enable>,
    "role": <role>,
    "excludedMetrics": [<metric>, <metric>, ...]
  },
  <awsServiceName>: {
    "enable": <enable>,
    "role": <role>,
    "excludedMetrics": [<metric>, <metric>, ...]
  },
  ...
}
```

Enter the [AWS service identifier](#awsServiceNames) in place of <i>`<awsServiceName>`</i>.

| KEY                   | TYPE              | DESCRIPTION                                                                                |
| --------              | ------            | -----------                                                                                |
| `enable`              | *boolean*         | whether integration settings are enabled(`true`)/disabled(`false`)                                        |
| `role`                | *string* / *null* | full role name[*4](#roleFullName). If not specifying a role, set to `null`.  |
| `excludedMetrics`     | *array[string]*   | [optional] list of excluded metrics[*5](#excludedMetrics)                              |
| `includedMetrics`     | *array[string]*   | [optional] list of included metrics[*5](#excludedMetrics)                              |

With EC2 and RDS, the following additional keys can be configured.

| KEY                   | TYPE              | DESCRIPTION                                                                                                                                      |
| --------              | ------            | -----------                                                                                                                                      |
| `retireAutomatically` | *boolean*         | [optional] whether [auto-retire](https://mackerel.io/blog/entry/weekly/20180903) is enabled(`true`)/disabled(`false`). If not configured, it will be treated as disabled.|

If settings are omitted for a certain AWS service, the integration of that AWS service will be treated as disabled.
In the example input below, AWS services other than EC2, ELB, and ALB will be treated as disabled and metrics will not be integrated.

#### Example input

```json
{
  "name": "example",
  "memo": "This is example integration",
  "roleArn": "arn:aws:iam::123456789012:role/MackerelRole",
  "externalId": "string",
  "region": "ap-northeast-1",
  "includedTags": "service:foo,service:bar",
  "excludedTags": "environment:staging",
  "services": {
    "EC2": {
      "enable": true,
      "role": "AWSService:EC2Role",
      "excludedMetrics": ["ec2.status_check_failed.instance"],
      "retireAutomatically": true
    },
    "ELB": {
      "enable": true,
      "role": "AWSService:ELBRole",
      "excludedMetrics": []
    },
    "ALB": {
      "enable": true,
      "role": null,
      "excludedMetrics": ["alb.request.count"]
    }
  }
}
```

<h4 id="name" class="annotation">*1 About AWS Integration settings' names</h4>

Multiple AWS Integration settings of the same name cannot be created within the same organization.

<h4 id="awsAccessInfo" class="annotation">*2 About AWS access information</h4>

There are two ways to integrate AWS integration.

1. Set an IAM role that allows access only from the Mackerel system AWS account and authenticate with AssumeRole
2. Set an access key ID and a secret access key

Refer [here](https://mackerel.io/docs/entry/integrations/aws#setting) for more details.

Depending on the integration method, the values set for `key`, `secretKey`, `roleArn`, and `externalId` will differ in the following ways.

**Integration method 1**

Requires setting an IAM role resource name in `roleArn` and an external ID in `externalId`.

The external ID is an ID issued by Mackerel.

It can be issued using the [Generate AWS Integration External ID](#generate-external-id) API.

Multiple AWS integrations can be configured with the same external ID if within the same organization.

**Integration method 2**

Requires setting an access key ID in `key` and a secret access key in `secretKey`.

- ※ In both of the above methods, it is [required that you set adequate access permissions](https://mackerel.io/docs/entry/integrations/aws#setting). Please note that if AWS access information is invalid, such as the specified IAM role not existing or access permissions not being granted, metrics may not integrate normally.

<h4 id="tags" class="annotation">*3 About tags/excluding tags</h4>

In Mackerel, you can specify AWS tags to narrow down the hosts to register.

Only resources with the specified tag will be registered as a host and metrics collected. By specifying a tag to exclude, the resource with that tag will not be registered as a host.

If you specify the tag as `service:foo, service:bar`, this will target resources with a key of service and value of foo and those with a key of service and value of bar.

Refer [here](https://mackerel.io/docs/entry/integrations/aws#tag) for more details.

<h4 id="roleFullName" class="annotation">*4 Full role name</h4>
The full name of a role is a string in the format of `<service name>:<role name>`.

<table class="eg-table">
  <tbody>
    <tr>
      <th>e.g.</th>
      <td>Hatena-Bookmark service's db-master role looks like this <code>Hatena-Bookmark:db-master</code></td>
    </tr>
  </tbody>
</table>

<h4 id="excludedMetrics" class="annotation">*5 About excludable / includable metrics</h4>

<i>`excludedMetrics`</i> sets a list of metric names to be excluded from monitoring for each AWS service.

<i>`includedMetrics`</i> sets a list of metric names to be explicitly included monitoring for each AWS service.

Only one or the other of <i>`excludedMetrics`</i> and <i>`includedMetrics`</i> can be set for each AWS integration settings across all services.
Both <i>'excludedMetrics'</i> and <i>'includedMetrics'</i> can't coexist for each service within the same AWS integration settings.

Refer [here](https://mackerel.io/docs/entry/integrations/aws) for a list of metrics that can be used for each AWS service.

Alternatively, you can also obtain this using the [List Excludable Metrics for AWS Integration ](#excludable-metrics) API.


### Response

#### Success

Returns registered AWS Integration settings.

The format is the same as the object <i>`<aws_integration>`</i> in the <a href="#list">List AWS Integration Settings</a> API.

#### Error

<table class="default api-error-table">
  <thead>
    <tr>
      <th class="status-code">STATUS CODE</th>
      <th class="description">DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>400</td>
      <td>when the input is in an unacceptable format</td>
    </tr>
    <tr>
      <td>401</td>
      <td>when the API key is invalid / when the API key does not have writing permission</td>	
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="update">Update AWS Integration Settings</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/aws-integrations/<em>&lt;awsIntegrationId&gt;</em></code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

This is the same as [Register AWS Integration Settings](#create).

However, `key`, `secretKey`, `roleArn`, and `externalId` will not be updated if omitted.
Also, you can change the integration method by specifying `null` for `key`, `secretKey`, `roleArn`, and `externalId`.
Other keys will be overwritten and updated.

### Response

#### Success

Returns updated AWS Integration settings.

The format is the same as the object <i>`<aws_integration>`</i> in the <a href="#list">List AWS Integration Settings</a> API.

#### Error

<table class="default api-error-table">
  <thead>
    <tr>
      <th class="status-code">STATUS CODE</th>
      <th class="description">DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>400</td>
      <td>when the input is in an unacceptable format</td>
    </tr>
    <tr>
      <td>401</td>
      <td>when the API key is invalid / when the API key does not have writing permission</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the AWS Integration setting of the specified ID does not exist</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="delete">Delete AWS Integration Settings</h2>

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/aws-integrations/<em>&lt;awsIntegrationId&gt;</em></code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Response

#### Success

Returns deleted AWS Integration settings.

The format is the same as the object <i>`<aws_integration>`</i> in the <a href="#list">List AWS Integration Settings</a> API.

#### Error

<table class="default api-error-table">
  <thead>
    <tr>
      <th class="status-code">STATUS CODE</th>
      <th class="description">DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>400</td>
      <td>when the input is in an unacceptable format</td>
    </tr>
    <tr>
      <td>401</td>
      <td>when the API key is invalid / when the API key does not have writing permission</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the AWS Integration setting of the specified ID does not exist</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="generate-external-id">Generate AWS Integration External ID</h2>

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/aws-integrations-external-id</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Response

#### Success

Returns a generated external ID.

Once issued, the external ID can be reused as long as it is within the same organization, unless all AWS Integration settings configured with the external ID are deleted.

``` json
{
  "externalId": <externalId>
}
```
| KEY                   | TYPE              | DESCRIPTION                          |
| --------              | ------            | -----------                          |
| `externalId`          | *string*          | generated external ID                     |

#### Error

<table class="default api-error-table">
  <thead>
    <tr>
      <th class="status-code">STATUS CODE</th>
      <th class="description">DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>400</td>
      <td>when the input is in an unacceptable format</td>
    </tr>
    <tr>
      <td>401</td>
      <td>when the API key is invalid / when the API key does not have writing permission</td>
    </tr>
  </tbody>
</table>


----------------------------------------------

<h2 id="excludable-metrics">List Excludable Metrics for AWS Integration</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/aws-integrations-excludable-metrics</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

#### Success

```json
{
  <awsServiceName>: [<metric>, <metric>, ...],
  <awsServiceName>: [<metric>, <metric>, ...],
  ...
}
```

| KEY                | TYPE                   | DESCRIPTION                                      |
| --------           | ------                 | -----------                                      |
| `<awsServiceName>` | *array[string]*        | list of excludable metrics for each AWS service        |

Enter the [AWS service identifier](#awsServiceNames) in place of <i>`<awsServiceName>`</i>.

#### Error

<table class="default api-error-table">
  <thead>
    <tr>
      <th class="status-code">STATUS CODE</th>
      <th class="description">DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>401</td>
      <td>when the API key is invalid</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="ref">Others</h2>

<h3 id="awsServiceNames">List of AWS Service Identifiers</h3>

Here is a list of the AWS services and their corresponding identifiers (ID) used in Mackerel.

| ID              | AWS SERVICE                      |
| --------        | ------                           |
| `EC2`           | Amazon EC2                       |
| `ELB`           | Elastic Load Balancing           |
| `ALB`           | Application Load Balancer        |
| `NLB`           | Network Load Balancer            |
| `RDS`           | Amazon RDS                       |
| `Redshift`      | Amazon Redshift                  |
| `ElastiCache`   | Amazon ElastiCache               |
| `SQS`           | Amazon SQS                       |
| `Lambda`        | AWS Lambda                       |
| `DynamoDB`      | Amazon DynamoDB                  |
| `CloudFront`    | AWS CloudFront                   |
| `APIGateway`    | Amazon API Gateway               |
| `Kinesis`       | Amazon Kinesis                   |
| `S3`            | Amazon S3                        |
| `ES`            | Amazon Elasticsearch Service     |
| `ECSCluster`    | Amazon ECS                       |
| `SES`           | Amazon SES                       |
| `States`        | AWS Step Functions               |
| `EFS`           | Amazon EFS                       |
| `Firehose`      | Amazon Kinesis Data Firehose     |
| `Batch`         | AWS Batch                        |
| `WAF`           | AWS WAF                          |
| `Billing`       | AWS Billing                      |
| `Route 53`      | Amazon Route 53                  |
| `Connect`       | Amazon Connect                   |
| `DocDB`         | Amazon DocumentDB                |
| `CodeBuild`     | AWS CodeBuild                    |
