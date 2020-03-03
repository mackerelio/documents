---
Title: AWSインテグレーション
Date: 2016-04-21T18:08:24+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653812171392306672
---

AWSインテグレーションを用いると、AWSクラウド製品をMackerelのホストとして管理し、メトリックを監視できます。本機能はTrialプランとStandardプランのみの提供となります。

AWSのクラウド製品1台が、Mackerelで1ホストとして登録され、Mackerelの課金対象のホスト数としてカウントされます。
ホストの種類は、EC2についてはスタンダードホスト、その他の製品についてはマイクロホストとなります。
また、5分ごとに取得対象となるメトリックの数だけAWSのAPIをコールして値を取得します。そのため、[Amazon CloudWatch API利用の料金](https://aws.amazon.com/jp/cloudwatch/pricing/)が発生する場合がありますのでご注意ください。
取得するメトリックを制限したい場合は[取得するメトリックを制限する](#select-metric)の項目を参照してください。

AWSインテグレーションは現在は以下のAWSクラウド製品に対応しています。取得メトリックなどについてはそれぞれのドキュメントを参照ください。

[EC2](https://mackerel.io/ja/docs/entry/integrations/aws/ec2)・[ELB (CLB)](https://mackerel.io/ja/docs/entry/integrations/aws/elb)・[ALB](https://mackerel.io/ja/docs/entry/integrations/aws/alb)・[NLB](https://mackerel.io/ja/docs/entry/integrations/aws/nlb)・[RDS](https://mackerel.io/ja/docs/entry/integrations/aws/rds)・[ElastiCache](https://mackerel.io/ja/docs/entry/integrations/aws/elasticache)・[Redshift](https://mackerel.io/ja/docs/entry/integrations/aws/redshift)・[Lambda](https://mackerel.io/ja/docs/entry/integrations/aws/lambda)・[SQS](https://mackerel.io/ja/docs/entry/integrations/aws/sqs)・[DynamoDB](https://mackerel.io/ja/docs/entry/integrations/aws/dynamodb)・[CloudFront](https://mackerel.io/ja/docs/entry/integrations/aws/cloudfront)
・[API Gateway](https://mackerel.io/ja/docs/entry/integrations/aws/apigateway)
・[Kinesis Data Streams](https://mackerel.io/ja/docs/entry/integrations/aws/kinesis)・[S3](https://mackerel.io/ja/docs/entry/integrations/aws/s3)・[Elasticsearch Service](https://mackerel.io/ja/docs/entry/integrations/aws/es)・[ECS](https://mackerel.io/ja/docs/entry/integrations/aws/ecs)・[SES](https://mackerel.io/ja/docs/entry/integrations/aws/ses)・[Step Functions](https://mackerel.io/ja/docs/entry/integrations/aws/states)・[EFS](https://mackerel.io/ja/docs/entry/integrations/aws/efs)・[Kinesis Data Firehose](https://mackerel.io/ja/docs/entry/integrations/aws/firehose)・[Batch](https://mackerel.io/ja/docs/entry/integrations/aws/batch)

<h2 id="setting">連携方法</h2>
AWSインテグレーションの連携方法には2つの方法があります。

- MackerelのシステムのAWSアカウントからのアクセスのみを許可するIAMロールを設定し、AssumeRoleで認証する方法
- Access Key IDとSecret Access Keyを設定する方法

セキュリティー保全の観点から、IAMロールで設定する方法を強く推奨します。

<h3>IAMロールを設定する方法</h3>
<h4>1. IAM Management Consoleにてロールを作成する</h4>
<a href="https://console.aws.amazon.com/iam" target="_blank">IAM Management Console</a>にて新しいロールを作成します。
ロールのタイプを選択する画面では「別のAWSアカウント」 (`Another AWS account`) を選択します。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190528/20190528124822.png)

[MackerelのAWSインテグレーション設定のページ](https://mackerel.io/my?tab=awsIntegration)から作成ボタンを押して、External IDを取得してください。許可するAccount IDには `217452466226` を入力してください。また、`Require external ID` のオプションを選択した上で、External IDにはMackerelの設定作成ページで取得したExternal IDを指定して下さい。このアカウントはMackerelのシステムがユーザーのロールにアクセスする際に利用するアカウントです。この設定により、作成されたロールにはMackerelのアカウントしかアクセスできない状態になります。`Require MFA` はチェックせずに次の設定ページに移動してください。

ロールには以下のポリシーを付与します。
FullAccess権限を付与しないようにご注意ください。また、ひとつのIAMロールに対してアタッチ可能なポリシーの上限は10個に制限されており、これはAWSの仕様です。必要に応じて、AWSに対して上限緩和申請をおこなってください。

AWSインテグレーションで使用する全ての権限を設定する場合、<a href="#iam_policy">AWSインテグレーションで使用するIAMポリシー</a> の項目を参照下さい。

- `AmazonRedshiftReadOnlyAccess`
- `AmazonEC2ReadOnlyAccess`
- `AmazonElastiCacheReadOnlyAccess`
- `AmazonRDSReadOnlyAccess`
- `AWSLambdaReadOnlyAccess`
- `AmazonSQSReadOnlyAccess`
- `AmazonDynamoDBReadOnlyAccess`
- `CloudFrontReadOnlyAccess`
- `apigateway:GET`
    - リソースポリシーは `arn:aws:apigateway:ap-northeast-1::/*` などのように指定します。リソースポリシーで対象を制限することはできません。
- `AmazonKinesisReadOnlyAccess`
- `AmazonS3ReadOnlyAccess`
- `AmazonESReadOnlyAccess`
- `ecs:Describe* / ecs:List*`
- `AmazonSESReadOnlyAccess / ses:Describe*`
- `AWSStepFunctionsReadOnlyAccess`
- `AmazonElasticFileSystemReadOnlyAccess`
- `AmazonKinesisFirehoseReadOnlyAccess`
- `batch:Describe* / batch:List*`
- `CloudWatchReadOnlyAccess`
    - 以下のサービスのみを設定する場合に指定します。
        - CloudFront, API Gateway, Kinesis Data Streams, S3, Elasticsearch Service, ECS, SES, Step Functions, EFS, Kinesis Data Firehose, Batch

また、AWSインテグレーションでは後述するようにタグによって絞り込みを行うことが出来ますが、ElastiCacheやSQSでタグによる絞り込みを行う場合は追加のポリシーを付与する必要があります。
詳しくは<a href="#tag">タグで絞り込む</a> の項目を参照してください。

![](https://cdn-ak2.f.st-hatena.com/images/fotolife/m/mackerelio/20170912/20170912165028.png)

ロール名を指定してロールを作成します。
`MackerelAWSIntegrationRole` のようにMackerelのAWSインテグレーションで使用していることが分かりやすい名前を付けることを推奨します。

<h4>2. ロールARNをMackerelに登録する</h4>
ロールARNを、先程External IDを取得したMackerelの画面で登録します。

<h4>3. ホストを確認する</h4>
しばらくすると、ご利用のAWSクラウド製品がMackerelにホストとして登録され、メトリックが投稿されます。
監視ルールを作成し、アラートを通知することもできます。
詳しくは[監視・通知を設定する](https://mackerel.io/ja/docs/entry/howto/alerts)をご覧ください。

<h3>Access Key IDとSecret Access Keyを設定する方法</h3>
以下の方法はセキュリティー保全の観点から推奨しておりません。
<h4>1. IAM Management Consoleにてユーザーを作成する</h4>
<a href="https://console.aws.amazon.com/iam" target="_blank">IAM Management Console</a>にて新しいユーザーを作成します。
`MackerelAWSIntegrationUser` のようにMackerelのAWSインテグレーションで使用していることが分かりやすい名前を付けることを推奨します。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160425/20160425173140.png)

<h4>2. アクセスキーをMackerelに登録する</h4>
作成時の画面に表示されるAccess Key IDとSecret Access Keyを、[Mackerelに登録](https://mackerel.io/my?tab=awsIntegration)します。
登録するオーガニゼーションを間違えないようにご注意ください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160428/20160428110910.png)

<h4>3. ポリシーを付与する</h4>
作成したユーザーに、以下のポリシーを付与します。
FullAccess権限を付与しないようにご注意ください。また、ひとつのIAMユーザーに対してアタッチ可能なポリシーの上限は10個に制限されており、これはAWSの仕様です。必要に応じて、AWSに対して上限緩和申請をおこなってください。

AWSインテグレーションで使用する全ての権限を設定する場合、<a href="#iam_policy">AWSインテグレーションで使用するIAMポリシー</a> の項目を参照下さい。

- `AmazonRedshiftReadOnlyAccess`
- `AmazonEC2ReadOnlyAccess`
- `AmazonElastiCacheReadOnlyAccess`
- `AmazonRDSReadOnlyAccess`
- `AWSLambdaReadOnlyAccess`
- `AmazonSQSReadOnlyAccess`
- `AmazonDynamoDBReadOnlyAccess`
- `CloudFrontReadOnlyAccess`
- `apigateway:GET`
    - リソースポリシーは `arn:aws:apigateway:ap-northeast-1::/*` などのように指定します。リソースポリシーで対象を制限することはできません。
- `AmazonKinesisReadOnlyAccess`
- `AmazonS3ReadOnlyAccess`
- `AmazonESReadOnlyAccess`
- `ecs:Describe* / ecs:List*`
- `AmazonSESReadOnlyAccess / ses:Describe*`
- `AWSStepFunctionsReadOnlyAccess`
- `AmazonElasticFileSystemReadOnlyAccess`
- `AmazonKinesisFirehoseReadOnlyAccess`
- `batch:Describe* / batch:List*`
- `CloudWatchReadOnlyAccess`
    - 以下のサービスのみを設定する場合に指定します。
        - CloudFront, API Gateway, Kinesis Data Streams, S3, Elasticsearch Service, ECS, SES, Step Functions, EFS, Kinesis Data Firehose, Batch

また、AWSインテグレーションでは後述するようにタグによって絞り込みを行うことが出来ますが、ElastiCacheやSQSでタグによる絞り込みを行う場合は追加のポリシーを付与する必要があります。
詳しくは<a href="#tag">タグで絞り込む</a> の項目を参照してください。

![](https://cdn-ak2.f.st-hatena.com/images/fotolife/m/mackerelio/20170912/20170912165028.png)

<h4>4. ホストを確認する</h4>
しばらくすると、ご利用のAWSクラウド製品がMackerelにホストとして登録され、メトリックが投稿されます。
監視ルールを作成し、アラートを通知することもできます。
詳しくは[監視・通知を設定する](https://mackerel.io/ja/docs/entry/howto/alerts)をご覧ください。

<h2 id="select-metric">取得するメトリックを制限する</h2>

一部のメトリックを取得しないように設定して、ホスト数を削減したりCloudWatch APIの料金を減らす事ができます。ホスト台数は過去一ヶ月分の移動平均での算出となります。詳しくは[ホスト数の計算方法について](https://mackerel.io/ja/docs/entry/faq/contracts/calculate-host-number)をご確認ください。

例えばKinesis Data Streamsの`kinesis.latency.#.minimum`を取得しないようにする場合は以下のようにチェックボックスを外します。この設定により`GetRecords.Latency`、`PutRecord.Latency`、`PutRecords.Latency`それぞれのminimumの取得を制限し、最大で3メトリックを削減します。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200129/20200129193706.png)

<h2 id="tag">タグで絞り込む</h2>
ホストとして登録してメトリックを取得するAWSクラウド製品を、AWSで付与しているタグで絞り込めます。

<h3>1. タグを取得するための権限を付与する</h3>
AWSのタグで絞り込むには、AWSインテグレーションの設定のために付与したポリシー以外に、以下のアクションに対する権限が追加で必要になります。

- `elasticache:ListTagsForResource`
- `sqs:ListQueueTags`
- `states:ListTagsForResource`

また、Access Key IDとSecret Access Keyを用いた設定を行っている場合は、以下のアクションに対する権限も必要になります。IAMロールによる設定の場合は不要です。

- 連携ユーザーに対する `iam:GetUser`

これらのポリシーの付与は、Inline Policiesにて行ってください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160616/20160616150058.png)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160616/20160616150059.png)

<h3>2. タグで絞り込む設定を行う</h3>
Mackerelの設定画面でタグを指定します。連携ホスト数を確認し、保存してください。

タグを`service:foo, service:bar`のように指定すると、キーがserviceで値がfooまたはキーがserviceで値がbarであるタグが付与されているインスタンスが対象となります。
キーや値にコロン `:` やカンマ `,` などを含む場合は、クォート (`"` または `'`) で囲ってください。例えば、キーが`service:role`で値が`foo,bar`である場合は、`"service:role": "foo,bar"`のように指定します。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160617/20160617104510.png)

<h2 id="iam_policy">AWS インテグレーションで使用するIAMポリシー</h2>

以下の権限リストはAWSインテグレーションで使用する全ての権限を指定しています。
独自のポリシーを作成してアタッチを行うか、Inline Policiesにて指定します。

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
                "cloudfront:Get*",
                "cloudfront:List*",
                "cloudwatch:Get*",
                "cloudwatch:List*",
                "dynamodb:Describe*",
                "dynamodb:List*",
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
                "iam:GetUser",
                "kinesis:Describe*",
                "kinesis:List*",
                "lambda:GetFunctionConfiguration",
                "lambda:List*",
                "rds:Describe*",
                "rds:ListTagsForResource",
                "redshift:Describe*",
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
                "states:List*"
            ],
            "Effect": "Allow",
            "Resource": "*"
        }
    ]
}
```



<h2 id="faq">FAQ</h2>

### アクセスキーの権限チェックと CreateInternetGateway に関して

ユーザーが登録したアクセスキーが不必要に強い権限を持っていないかのチェックのために、AWSインテグレーションは定期的に CreateInternetGateway API を dry-run にて実行しています。アクセスキーが必要以上の権限を持っていた場合には、メトリックの収集と投稿は行われませんのでご注意ください。チェックを登録後にも定期的におこなう理由は、アクセスキーに対してポリシーが追加され、権限が強くなってしまう可能性があるためです。

### AWSインテグレーションにより連携したホストの退役について

上記の連携設定を行うことで、対象サービスとタグ条件に合致した AWS クラウド製品は自動的に Mackerel と連携されホストとして登録されます。一方で、AWS 側でインスタンスの削除などを行っただけでは、Mackerel 側のホストは削除（退役）されません。AWSインテグレーションにより連携したホストを Mackerel の監視対象から外すには、別途退役の作業を実施する必要があります。

[https://mackerel.io/ja/docs/entry/howto/host-retirement:embed:cite]

仮に退役作業をしない場合でも、ホスト情報が残り続けるだけで、メトリック投稿のないホストは課金対象にはなりません。

<h3 id="plugin-custom-identifier">プラグインによる監視内容の連携ホストへの集約に関して</h3>

mackerel-agent のカスタムメトリックとチェック監視の plugin 設定には、`custom_identifier` を指定できます。`custom_identifier` とは、ホストの識別子としてユーザー独自の identifier を付与するための仕組みです。これを利用して、別のマシンにインストールした mackerel-agent から投稿されたメトリックやチェック監視を、AWSインテグレーション連携ホストの物として集約できます。`custom_identifier` は、対応するプラグイン設定に指定します。

例えば Amazon RDS の場合はそのエンドポイントが、ELB の場合は DNS Name が、それぞれ `custom_identifier` 文字列となります。

#### 利用例
以下にふたつの利用例を紹介します。いずれの場合も、mackerel-agent の設定ファイルへの追記後はエージェントの再起動が必要です。

ひとつめの例は、Amazon RDS に対する [mackerel-plugin-mysql](https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-mysql) プラグインを用いた MySQL 監視です。mackerel-agent.conf の設定に以下のように `custom_identifier` を含むプラグイン設定を追加することで、プラグインで取得したメトリックをRDSホストのカスタムメトリックとして集約できます。

```
[plugin.metrics.mysql]
command = ["mackerel-plugin-mysql", "-host", "RDSのエンドポイント", "-username", "user", "-password", "pass"]
custom_identifier = "RDSのエンドポイント"
```

ふたつめの例は、 Amazon Elasticsearch Service と [check-elasticsearch](https://github.com/mackerelio/go-check-plugins/tree/master/check-elasticsearch) プラグインを用いた Elasticsearch 監視です。mackerel-agent.conf の設定に以下のように `custom_identifier` を含むプラグイン設定を追加することで、 Elasticsearch Service クラスターのヘルスチェックを Elasticsearch Service ホストのチェック監視として集約できます。

```
[plugin.checks.elasticsearch]
command = ["check-elasticsearch", "-s", "https", "-H", "Elasticsearch Service のエンドポイント", "-p", "443"]
custom_identifier = "Elasticsearch Service の ARN"
```

