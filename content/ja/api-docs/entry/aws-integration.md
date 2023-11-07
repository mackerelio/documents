---
Title: AWSインテグレーション
Date: 2020-05-29T16:00:00+09:00
URL: https://mackerel.io/ja/api-docs/entry/aws-integration
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/26006613578997368
CustomPath: aws-integration
---

<ul class="internal-nav">
  <li><a href="#list">AWSインテグレーション設定の一覧</a></li>
  <li><a href="#get">AWSインテグレーション設定の取得</a></li>
  <li><a href="#create">AWSインテグレーション設定の登録</a></li>
  <li><a href="#update">AWSインテグレーション設定の更新</a></li>
  <li><a href="#delete">AWSインテグレーション設定の削除</a></li>
  <li><a href="#generate-external-id">AWSインテグレーション外部IDの生成</a></li>
  <li><a href="#excludable-metrics">AWSインテグレーションの除外可能なメトリック名一覧</a></li>
</ul>

<h2 id="list">AWSインテグレーション設定の一覧</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/aws-integrations</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

#### 成功時

AWSインテグレーション設定の一覧が返却されます。

```json
{
  "aws_integrations": [<aws_integration>, <aws_integration>, ...]
}
```

<i>`<aws_integration>`</i> は以下のキーを持つオブジェクトです。

| KEY             | TYPE              | DESCRIPTION                                                            |
| --------        | ------            | -----------                                                            |
| `id`            | *string*          | AWSインテグレーション設定ID                                            |
| `name`          | *string*          | AWSインテグレーション設定名                                            |
| `memo`          | *string*          | AWSインテグレーション設定に関するメモ                                  |
| `key`           | *string* / *null* | AWS のアクセスキーID                                                   |
| `roleArn`       | *string* / *null* | AWS IAMロールのリソース名(ARN)                                         |
| `externalId`    | *string* / *null* | 外部ID                                                                 |
| `region`        | *string*          | AWS のリージョン名                                                     |
| `includedTags`  | *string*          | カンマ(,)句切されたタグのリスト                                        |
| `excludedTags`  | *string*          | カンマ(,)句切された除外タグのリスト                                    |
| `services`      | *services*        | 各AWSサービスのインテグレーション設定                                  |

<i>`<services>`</i> は以下のキーを持つオブジェクトです。

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

<i>`<awsServiceName>`</i> には [各AWSサービスの識別子](#awsServiceNames) が入ります。

| KEY                   | TYPE              | DESCRIPTION                                      |
| --------              | ------            | -----------                                      |
| `enable`              | *boolean*         | インテグレーション設定が有効かどうか             |
| `role`                | *string* / *null* | ロールの詳細名                                   |
| `excludedMetrics`     | *array[string]*   | 除外するメトリック名のリスト                     |

<i>`role`</i> は [AWSインテグレーション設定の登録](#create)の項目の <i>`role`</i> と同じ。

EC2・RDS の場合は、追加で以下のキーが設定されています。

| KEY                   | TYPE              | DESCRIPTION                              |
| --------              | ------            | -----------                              |
| `retireAutomatically` | *boolean*         | 自動退役が有効かどうか                   |

#### 失敗時

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
      <td>APIキーが不正のとき</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="get">AWSインテグレーション設定の取得</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/aws-integrations/<em>&lt;awsIntegrationId&gt;</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

#### 成功時

AWSインテグレーション設定の情報が返却されます。

フォーマットは<a href="#list">AWSインテグレーション設定の一覧</a>APIの <i>`<aws_integration>`</i> オブジェクトと同じ形式です。

#### 失敗時

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
      <td>APIキーが不正のとき</td>
    </tr>
    <tr>
      <td>404</td>
      <td>指定されたIDのAWSインテグレーション設定が存在しないとき</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="create">AWSインテグレーション設定の登録</h2>

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/aws-integrations</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

以下のキーをもつオブジェクト。

| KEY              | TYPE              | DESCRIPTION                                                            |
| --------         | ------            | -----------                                                            |
| `name`           | *string*          | AWSインテグレーション設定名[*1](#name)                                 |
| `memo`           | *string*          | AWSインテグレーション設定に関するメモ                                  |
| `key`            | *string*          | [optional] AWS のアクセスキーID[*2](#awsAccessInfo)                    |
| `secretKey`      | *string*          | [optional] AWS のシークレットアクセスキー[*2](#awsAccessInfo)          |
| `roleArn`        | *string*          | [optional] AWS IAM ロールのリソースネーム(ARN)[*2](#awsAccessInfo)     |
| `externalId`     | *string*          | [optional] 外部ID[*2](#awsAccessInfo)                                  |
| `region`         | *string*          | AWS のリージョン名                                                     |
| `includedTags`   | *string*          | カンマ(,)句切りされたタグのリスト[*3](#tags)                           |
| `excludedTags`   | *string*          | カンマ(,)句切りされた除外タグのリスト[*3](#tags)                       |
| `services`       | *services*        | 各サービスのインテグレーション設定                                     |

<i>`<services>`</i> は以下のようなオブジェクトです。

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

<i>`<awsServiceName>`</i> には [各AWSサービスの識別子](#awsServiceNames) が入ります。

| KEY                   | TYPE              | DESCRIPTION                                                                                |
| --------              | ------            | -----------                                                                                |
| `enable`              | *boolean*         | インテグレーション設定の有効(`true`)/無効(`false`)                                         |
| `role`                | *string* / *null* | ロールの詳細名[*4](#roleFullName)。ロールを指定しない場合は、 `null` を設定してください。  |
| `excludedMetrics`     | *array[string]*   | 除外するメトリック名のリスト[*5](#excludedMetrics)                                         |

EC2・RDS の場合は、追加で以下のキーが設定できます。

| KEY                   | TYPE              | DESCRIPTION                                                                                                                                      |
| --------              | ------            | -----------                                                                                                                                      |
| `retireAutomatically` | *boolean*         | [optional] [自動退役](https://mackerel.io/ja/blog/entry/weekly/20180903) の有効(`true`)/無効(`false`)。設定されなかった場合、無効扱いとなります。|

あるAWSサービスに対する設定を省略した場合、そのAWSサービスはインテグレーション無効扱いとなります。
例えば、以下の入力例では、 EC2・ELB・ALB以外のAWSサービスはインテグレーション無効扱いとなり、メトリックは連携されません。

#### 入力例

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

<h4 id="name" class="annotation">*1 AWSインテグレーション設定名について</h4>

ひとつのオーガニゼーションの中に、同じ名前のAWSインテグレーション設定名を複数つくることはできません。

<h4 id="awsAccessInfo" class="annotation">*2 AWSアクセス情報について</h4>

AWSインテグレーションの連携方法には2つの方法があります。

1. MackerelのシステムのAWSアカウントからのアクセスのみを許可するIAMロールを設定し、AssumeRoleで認証する方法
2. アクセスキーIDとシークレットアクセスキーを設定する方法

詳しくは、[こちら](https://mackerel.io/ja/docs/entry/integrations/aws#setting) をご覧ください。

連携方法によって、 `key`・`secretKey`・`roleArn`・`externalId` に設定する値が以下のように異なります。

**連携方法1の場合**

`roleArn` にIAMロールのリソース名、`externalId` に外部IDを設定する必要があります。

外部IDは Mackerel より発行されるIDです。

[AWSインテグレーション外部IDの生成](#generate-external-id) APIを利用して発行することができます。

同一オーガニゼーション内であれば、同じ外部IDを持ったAWSインテグレーションを複数設定することができます。

**連携方法2の場合**

`key` にアクセスキーID、`secretKey` にシークレットアクセスキーを設定する必要があります。

- ※ 上記どちらの連携方法においても、[適切なアクセス権限が設定されている必要](https://mackerel.io/ja/docs/entry/integrations/aws#setting) があります。指定したIAMロールが存在しない、適切なアクセス権限が付与されていないなど、AWSアクセス情報が不正な場合、正常にメトリックが連携されない恐れがありますので、ご注意ください。

<h4 id="tags" class="annotation">*3 タグ/除外タグについて</h4>

Mackerel ではAWSのタグを指定して、登録するホストを絞り込むことができます。

指定したタグが付与されたリソースのみをホストとして登録し、メトリックを収集します。除外するタグを指定すると、そのタグが付与されたリソースはホストとして登録しません。

タグを `service:foo, service:bar` のように指定すると、キーが service で値が foo またはキーが service で値が bar であるタグが付与されているリソースが対象となります。

詳しくは、 [こちら](https://mackerel.io/ja/docs/entry/integrations/aws#tag) をご覧ください。

<h4 id="roleFullName" class="annotation">*4 ロールの詳細名</h4>
ロールの詳細名は `<service name>:<role name>` というフォーマットの文字列です。

<table class="eg-table">
  <tbody>
    <tr>
      <th>e.g.</th>
      <td>Hatena-Bookmark サービスの db-master ロールなら<code>Hatena-Bookmark:db-master</code></td>
    </tr>
  </tbody>
</table>

<h4 id="excludedMetrics" class="annotation">*5 除外可能なメトリック名について</h4>

<i>`excludedMetrics`</i> では各AWSサービスに対して、監視対象から除外するメトリック名のリストを設定します。

各AWSサービスで除外可能なメトリック名の一覧は、[こちら](https://mackerel.io/ja/docs/entry/integrations/aws) でご確認いただけます。

もしくは、[AWSインテグレーションの除外可能なメトリック名一覧](#excludable-metrics) APIを利用して取得することも可能です。


### 応答

#### 成功時

作成されたAWSインテグレーション設定が返却されます。

フォーマットは<a href="#list">AWSインテグレーション設定の一覧</a>APIの <i>`<aws_integration>`</i> オブジェクトと同じ形式です。

#### 失敗時

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
      <td>入力が受け付けられないフォーマットだったとき</td>
    </tr>
    <tr>
      <td>401</td>
      <td>APIキーが不正のとき / APIキーに書き込み権限がないとき</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="update">AWSインテグレーション設定の更新</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/aws-integrations/<em>&lt;awsIntegrationId&gt;</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 入力

[AWSインテグレーション設定の登録](#create)と同様です。

ただし、 `key`・`secretKey`・`roleArn`・`externalId` に関しては、省略された場合に更新されません。
また、 `key`・`secretKey`・`roleArn`・`externalId` に `null` を指定して連携方法を替えることができます。
その他のキーに関しては上書き更新となります。

### 応答

#### 成功時

更新されたAWSインテグレーション設定が返却されます。

フォーマットは<a href="#list">AWSインテグレーション設定の一覧</a>APIの<i>`<aws_integration>`</i>オブジェクトと同じ形式です。

#### 失敗時

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
      <td>入力が受け付けられないフォーマットだったとき</td>
    </tr>
    <tr>
      <td>401</td>
      <td>APIキーが不正のとき / APIキーに書き込み権限がないとき</td>
    </tr>
    <tr>
      <td>404</td>
      <td>指定されたIDのAWSインテグレーション設定が存在しないとき</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="delete">AWSインテグレーション設定の削除</h2>

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/aws-integrations/<em>&lt;awsIntegrationId&gt;</em></code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 応答

#### 成功時

削除されたAWSインテグレーション設定が返却されます。

フォーマットは<a href="#list">AWSインテグレーション設定の一覧</a>APIの<i>`<aws_integration>`</i>オブジェクトと同じ形式です。

#### 失敗時

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
      <td>入力が受け付けられないフォーマットだったとき</td>
    </tr>
    <tr>
      <td>401</td>
      <td>APIキーが不正のとき / APIキーに書き込み権限がないとき</td>
    </tr>
    <tr>
      <td>404</td>
      <td>指定されたIDのAWSインテグレーション設定が存在しないとき</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="generate-external-id">AWSインテグレーション外部IDの生成</h2>

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/aws-integrations-external-id</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### 応答

#### 成功時

生成された外部IDが返却されます。

一度発行した外部IDは、当該外部IDが設定されたAWSインテグレーション設定が全て削除されない限り、同一オーガニゼーション内であれば再利用することができます。

``` json
{
  "externalId": <externalId>
}
```
| KEY                   | TYPE              | DESCRIPTION                          |
| --------              | ------            | -----------                          |
| `externalId`          | *string*          | 生成された外部ID                     |

#### 失敗時

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
      <td>入力が受け付けられないフォーマットだったとき</td>
    </tr>
    <tr>
      <td>401</td>
      <td>APIキーが不正のとき / APIキーに書き込み権限がないとき</td>
    </tr>
  </tbody>
</table>


----------------------------------------------

<h2 id="excludable-metrics">AWSインテグレーションの除外可能なメトリック名一覧</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/aws-integrations-excludable-metrics</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

#### 成功時

```json
{
  <awsServiceName>: [<metric>, <metric>, ...],
  <awsServiceName>: [<metric>, <metric>, ...],
  ...
}
```

| KEY                | TYPE                   | DESCRIPTION                                      |
| --------           | ------                 | -----------                                      |
| `<awsServiceName>` | *array[string]*        | 各AWSサービスの除外可能なメトリック名一覧        |

<i>`<awsServiceName>`</i> には [各AWSサービスの識別子](#awsServiceNames) が入ります。

#### 失敗時

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
      <td>APIキーが不正のとき</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="ref">その他</h2>

<h3 id="awsServiceNames">AWSサービス識別子一覧</h3>

Mackerel で用いられる識別子（ID）とそれに対応するAWSサービス（AWS SERVICE）は以下の通りです。

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
