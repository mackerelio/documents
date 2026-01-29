---
Title: AWSインテグレーション
Date: 2016-04-21T18:08:24+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653812171392306672
---

**本機能はTrialプランとStandardプランのみの提供となります。**

AWSインテグレーションではAWSクラウド製品をMackerelのホストとして管理し、メトリックを監視できます。

AWSクラウド製品のうち、EC2についてはスタンダードホスト、その他の製品についてはマイクロホストとして課金されます。
また、5分ごとに取得対象となるメトリックの数だけAWSのAPIをコールするため、[Amazon CloudWatch API利用の料金](https://aws.amazon.com/jp/cloudwatch/pricing/)が発生する場合がありますのでご注意ください。

<details>
<summary>目次（クリックすると開閉します）</summary>

[:contents]

</details>

<h2 id="support-products">対応しているクラウド製品</h2>

AWSインテグレーションでは、以下のAWSクラウド製品に対応しています。取得するメトリックなど詳細については各製品のドキュメントを参照ください。

* [EC2](https://mackerel.io/ja/docs/entry/integrations/aws/ec2)
* [CLB (ELB)](https://mackerel.io/ja/docs/entry/integrations/aws/elb)
* [ALB](https://mackerel.io/ja/docs/entry/integrations/aws/alb)
* [NLB](https://mackerel.io/ja/docs/entry/integrations/aws/nlb)
* [RDS](https://mackerel.io/ja/docs/entry/integrations/aws/rds)
* [ElastiCache](https://mackerel.io/ja/docs/entry/integrations/aws/elasticache)
* [Redshift](https://mackerel.io/ja/docs/entry/integrations/aws/redshift)
* [Lambda](https://mackerel.io/ja/docs/entry/integrations/aws/lambda)
* [SQS](https://mackerel.io/ja/docs/entry/integrations/aws/sqs)
* [DynamoDB](https://mackerel.io/ja/docs/entry/integrations/aws/dynamodb)
* [CloudFront](https://mackerel.io/ja/docs/entry/integrations/aws/cloudfront)
* [API Gateway](https://mackerel.io/ja/docs/entry/integrations/aws/apigateway)
* [Kinesis Data Streams](https://mackerel.io/ja/docs/entry/integrations/aws/kinesis)
* [S3](https://mackerel.io/ja/docs/entry/integrations/aws/s3)
* [OpenSearch Service](https://mackerel.io/ja/docs/entry/integrations/aws/es)
* [ECS Cluster](https://mackerel.io/ja/docs/entry/integrations/aws/ecs)
* [SES](https://mackerel.io/ja/docs/entry/integrations/aws/ses)
* [Step Functions](https://mackerel.io/ja/docs/entry/integrations/aws/states)
* [EFS](https://mackerel.io/ja/docs/entry/integrations/aws/efs)
* [Kinesis Data Firehose](https://mackerel.io/ja/docs/entry/integrations/aws/firehose)
* [AWS Batch](https://mackerel.io/ja/docs/entry/integrations/aws/batch)
* [WAF](https://mackerel.io/ja/docs/entry/integrations/aws/waf)
* [Billing](https://mackerel.io/ja/docs/entry/integrations/aws/billing)
* [Route 53](https://mackerel.io/ja/docs/entry/integrations/aws/route53)
* [Amazon Connect](https://mackerel.io/ja/docs/entry/integrations/aws/connect)
* [DocumentDB](https://mackerel.io/ja/docs/entry/integrations/aws/docdb)
* [CodeBuild](https://mackerel.io/ja/docs/entry/integrations/aws/codebuild)
* [Athena](https://mackerel.io/ja/docs/entry/integrations/aws/athena)

<h2 id="setting">連携方法</h2>

AWSインテグレーションの連携方法には、IAMロールで連携（AssumeRoleによる認証）を行う方法と、Access Key IDとSecret Access Keyで連携する2通りの方法があります。

いずれかを設定していただきますが、セキュリティ保全の観点から、IAMロールによる連携を強く推奨しています。

<ul>
  <li><a href="#setting_aws">1. インテグレーション用のIAMロールもしくはIAMユーザーを追加する（以下のいずれかを設定してください。）</a></li>
  <ul>
      <li><a href="#setting_aws_iam_role">1-a. IAMロールを設定する方法</a></li>
      <li><a href="#setting_aws_access_key">1-b. Access Key IDとSecret Access Keyを設定する方法</a></li>
  </ul>
  <li><a href="#setting_policy">2. ポリシーを付与する</a></li>
  <li><a href="#setting_check_host">3. ホストを確認する</a></li>
</ul>

<h3 id="setting_aws">1. インテグレーション用のIAMロールもしくはIAMユーザーを追加する</h3>

<h4 id="setting_aws_iam_role">1-a. IAMロールを設定する方法</h4>
<h5>IAM Management Consoleにてロールを作成する</h5>
<a href="https://console.aws.amazon.com/iam" target="_blank">IAM Management Console</a>にて新しいロールを作成します。
ロールのタイプを選択する画面では「別のAWSアカウント」 (`Another AWS account`) を選択します。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190528/20190528124822.png)

[MackerelのAWSインテグレーション設定のページ](https://mackerel.io/my?tab=awsIntegration)から作成ボタンを押して、External IDを取得してください。許可するAccount IDには `217452466226` を入力してください。また、`Require external ID` のオプションを選択した上で、External IDにはMackerelの設定作成ページで取得したExternal IDを指定してください。このアカウントはMackerelのシステムがユーザーのロールにアクセスする際に利用するアカウントです。この設定により、作成されたロールにはMackerelのアカウントしかアクセスできない状態になります。`Require MFA` はチェックせずに次の設定ページに移動してください。

続いて<a href="#setting_policy">ポリシーの付与</a>を行います。

<h4 id="setting_aws_access_key">1-b. Access Key IDとSecret Access Keyを設定する方法</h4>
以下の方法はセキュリティー保全の観点から推奨しておりません。
<h5>IAM Management Consoleにてユーザーを作成する</h5>
<a href="https://console.aws.amazon.com/iam" target="_blank">IAM Management Console</a>にて新しいユーザーを作成します。
`MackerelAWSIntegrationUser` のようにMackerelのAWSインテグレーションで使用していることが分かりやすい名前を付けることを推奨します。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160425/20160425173140.png)

<h5>アクセスキーをMackerelに登録する</h5>
作成時の画面に表示されるAccess Key IDとSecret Access Keyを、[Mackerelに登録](https://mackerel.io/my?tab=awsIntegration)します。
登録するオーガニゼーションを間違えないようにご注意ください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160428/20160428110910.png)


<h3 id="setting_policy">2. ポリシーを付与する</h3>

作成したIAMロールもしくはIAMユーザーには、後述の表に記載の製品ごとに必要なポリシー／アクションを付与してください。FullAccess権限など必要以上に強い権限が付与されている場合、AWSインテグレーションが正常に動作しません。

* Mackerelにおける権限チェックは、後述の[アクセスキーの権限チェックと CreateInternetGateway に関して](#permission-check)に記載の方法で行っています。

表の「AWS製品」の列に *1 が付いた製品については、AWSインテグレーション設定でその製品だけを対象にする場合に、CloudWatchReadOnlyAccessが追加で必要です。

* *1 の付いていない製品もあわせて対象にする場合は不要です。

SQS、OpenSearch Service、Step Functionsで以下の設定を利用する場合は、表の「必要なポリシー／アクション」の列にある *2 の付いたアクションが追加で必要です。

* [タグを指定して登録するホストを絞り込む](#tag)
* [取得するメトリックを指定する](#select-metric)

#### 製品ごとに必要なポリシー／アクション

| AWS製品 | 必要なポリシー／アクション | 備考 |
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
| API Gateway *1 | `apigateway:GET` | リソースポリシーは`arn:aws:apigateway:ap-northeast-1::/*`などのように指定します。<br>リソースポリシーで連携対象を制限することはできません。 |
| Kinesis Data Streams *1 | AmazonKinesisReadOnlyAccess |  |
| S3 *1 | AmazonS3ReadOnlyAccess | S3側でバケットのリクエストメトリックを有効にする必要があります。<br><a href="https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/userguide/configure-request-metrics-bucket.html">S3バケットにリクエストメトリックを設定する方法</a>を参考に`EntireBucket`というフィルタ名で設定してください。 |
| OpenSearch Service *1 | AmazonOpenSearchServiceReadOnlyAccess<br>`elasticache:ListTagsForResource` *2 | AmazonESReadOnlyAccessでも利用可能です。 |
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

AWSの仕様で、ひとつのIAMロール／IAMユーザーに対してアタッチ可能なポリシーのクォータは10個に制限されています。必要に応じて、AWSに対してクォータの引き上げをリクエストしてください。

* [サービスクォータの引き上げをリクエストします。 - AWS サポート](https://docs.aws.amazon.com/ja_jp/awssupport/latest/user/create-service-quota-increase.html)

AWSインテグレーションで使用する全ての権限を設定する場合、<a href="#iam_policy">AWSインテグレーションで使用するIAMポリシー</a> の項目を参照してください。

参考：ポリシーの付与

![](https://cdn-ak2.f.st-hatena.com/images/fotolife/m/mackerelio/20170912/20170912165028.png)

参考：アクションの付与（Inline Policies）

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160616/20160616150058.png)
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160616/20160616150059.png)

<h3 id="setting_check_host">3. ホストを確認する</h3>
しばらくすると、ご利用のAWSクラウド製品がMackerelにホストとして登録され、メトリックが投稿されます。
監視ルールを作成し、アラートを通知することもできます。
詳しくは[監視・通知を設定する](https://mackerel.io/ja/docs/entry/howto/alerts)をご覧ください。

<h2 id="auto-new-metrics-addition">新規メトリックの自動追加を設定する</h2>

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20231101/20231101162010.png)

Mackerelでは、各AWSサービスの更新に伴って、新たなメトリックが追加されることがあります。

この「新規メトリックを自動的に追加する」をオンにすると、メトリックが追加されるつどAWSインテグレーション設定ページで取得対象として選択する手間を省けます。

AWSインテグレーションの利用料金は、連携しているホスト数と、1ホストあたりの上限を超えて投稿されているメトリック数によって決定します（参考：[利用料金算出方法](https://support.mackerel.io/hc/ja/articles/31304727432729-%E5%88%A9%E7%94%A8%E6%96%99%E9%87%91%E7%AE%97%E5%87%BA%E6%96%B9%E6%B3%95-2024%E5%B9%B411%E6%9C%881%E6%97%A5%E4%BB%A5%E9%99%8D)）。

予期せぬ利用料金の増加を避けたい場合は、「新規メトリックを自動的に追加する」をオフにします。以後追加されるメトリックは自動取得対象外となりますので、必要なメトリックを個別に選択してください。

<h2 id="auto-retirement">自動退役を設定する</h2>

一部のサービスでは、AWSリソースの削除に伴ってMackerelのホストを退役するオプションを提供しています。自動退役を有効にするには、設定画面で「自動退役を有効にする」にチェックを入れます。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230519/20230519114916.png)

AWSインテグレーションにおける自動退役は、Mackerelと連携しているリソースが削除されたと判断したとき、自動で関連するホストを退役するためのオプションです。そのため、以下の場合には自動退役を行いません。

- タグフィルタにより除外されているリソースが削除された
- 過去に連携していたが、現在はインテグレーションの設定でサービスを無効にしている
- 過去に連携していたが、現在はインテグレーションの設定を削除している

また、AWSインテグレーションがAWSリソースの削除を検出した時点で1回だけ判定を行うため、削除の検出後に連携を有効にしても自動退役の対象となりません。複数のインテグレーション設定で同じホストを連携している場合は、どれか1つの設定で削除と判断された時点で自動退役します。

<h2 id="select-metric">取得するメトリックを指定する</h2>

一部のメトリックを取得しないように設定して、メトリック料金を削減したり、CloudWatch APIのリクエスト回数を減らしたりできます。

<h3>1. 取得するメトリックを指定するための権限を付与する（一部の製品のみ）</h3>

一部の製品は、取得するメトリックを指定するために追加のアクションが必要になります。詳しくは[ポリシーを付与する](#setting_policy)を参照してください。

<h3>2. 取得するメトリックを指定する設定を行う</h3>

Mackerelの設定画面で取得するメトリックを選択します。不要なメトリックのチェックを外して、保存してください。

例えばKinesis Data Streamsの`kinesis.latency.#.minimum`を取得しないようにする場合は以下のようにチェックボックスを外します。この設定により`GetRecords.Latency`、`PutRecord.Latency`、`PutRecords.Latency`それぞれのminimumの取得を制限し、最大で3メトリックを削減します。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200129/20200129193706.png)

<h2 id="tag">タグを指定して登録するホストを絞り込む</h2>
ホストとして登録してメトリックを取得するAWSクラウド製品を、AWSで付与しているタグで絞り込めます。

<h3>1. タグを取得するための権限を付与する（一部の製品のみ）</h3>

一部の製品は、タグで登録するホストを絞り込むために追加のアクションが必要になります。詳しくは[ポリシーを付与する](#setting_policy)を参照してください。

<h3>2. タグで絞り込む設定を行う</h3>
Mackerelの設定画面でタグを指定します。連携ホスト数を確認し、保存してください。

タグを`service:foo, service:bar`のように指定すると、キーがserviceで値がfooまたはキーがserviceで値がbarであるタグが付与されているインスタンスが対象となります。
キーや値にコロン `:` やカンマ `,` などを含む場合は、クォート (`"` または `'`) で囲ってください。例えば、キーが`service:role`で値が`foo,bar`である場合は、`"service:role": "foo,bar"`のように指定します。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160617/20160617104510.png)

<h2 id="resource_tag">タグの値からサービス・ロールを割り当てる</h2>
各AWSリソースに `mackerel-integration` タグを付与しておくと、AWSインテグレーションがメトリックを取得するときに自動でロールを割り当てます。タグの書式は以下の通りです。

* Key: `mackerel-integration`
* Value: `<Service>:<Role> [ / <Service>:<Role> ...]`

複数のロールを割り当てたい場合は、`/` で区切って必要なだけ記述してください。ここで設定したサービス・ロールがMackerelに存在しない場合は、AWSインテグレーションが必要なサービス・ロールを作成します。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220204/20220204114346.png)

 なお、`mackerel-integration` タグを設定したホストでは、MackerelのWebコンソールで設定したデフォルトロールの割り当ては行われません。

### タグの設定が反映されない場合

タグの内容がロールに反映されない場合は、以下の項目に該当するかご確認ください。

* タグに設定したサービス・ロール名に不正な文字がないか
  * 利用できる文字はMackerelのサービス・ロールに使える文字と同等です
* タグを追加したAWSサービスが`mackerel-integration`に対応しているか
  * `mackerel-integration`への対応は随時行っていきます
  * 対応状況は各インテグレーションのドキュメントを参照ください

なお、ホストにロールを反映するまで数時間いただくことがあります。

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
                "connect:ListTagsForResource",
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



<h2 id="faq">よくある質問</h2>

<h3 id="permission-check">アクセスキーの権限チェックと CreateInternetGateway に関して</h3>

ユーザーが登録したアクセスキーが不必要に強い権限を持っていないかチェックするために、AWSインテグレーションは定期的に CreateInternetGateway API を dry-run にて実行しています。アクセスキーが必要以上の権限を持っていた場合には、メトリックの収集と投稿は行われませんのでご注意ください。

定期的にチェックを行う理由は、アクセスキーに対してポリシーが追加され、権限が強くなってしまう可能性があるためです。これに伴い、お使いのAWS環境にCreateInternetGatewayに関するログが出力されることがあります。

### AWSインテグレーションにより連携したホストの退役について

上記の連携設定を行うことで、対象サービスとタグ条件に合致した AWS クラウド製品は自動的に Mackerel と連携されホストとして登録されます。一方で、[サービスの設定で自動退役に対応していない・自動退役を無効にしている](#auto-retirement)場合、AWS 側でインスタンスの削除などを行っただけでは、Mackerel 側のホストは削除（退役）されません。AWSインテグレーションにより連携したホストを Mackerel の監視対象から外すには、別途退役の作業を実施する必要があります。

[https://mackerel.io/ja/docs/entry/howto/host-retirement:embed:cite]

仮に退役作業をしない場合でも、ホスト情報が残り続けるだけで、メトリック投稿のないホストは課金対象にはなりません。

<h3 id="plugin-custom-identifier">プラグインによる監視内容の連携ホストへの集約に関して</h3>

mackerel-agent のカスタムメトリックとチェック監視の plugin 設定には、`custom_identifier` を指定できます。`custom_identifier` とは、ホストの識別子としてユーザー独自の identifier を付与するための仕組みです。これを利用して、別のマシンにインストールした mackerel-agent から投稿されたメトリックやチェック監視を、AWSインテグレーション連携ホストの物として集約できます。`custom_identifier` は、対応するプラグイン設定に指定します。

例えば Amazon RDS の場合はそのエンドポイントが、ELB の場合は DNS Name が、それぞれ `custom_identifier` 文字列となります。

#### 利用例
以下にふたつの利用例を紹介します。いずれの場合も、mackerel-agent の設定ファイルへの追記後はエージェントの再起動が必要です。

ひとつめの例は、Amazon RDS に対する [mackerel-plugin-mysql](https://github.com/mackerelio/mackerel-plugin-mysql) プラグインを用いた MySQL 監視です。mackerel-agent.conf の設定に以下のように `custom_identifier` を含むプラグイン設定を追加することで、プラグインで取得したメトリックをRDSホストのカスタムメトリックとして集約できます。

```toml
[plugin.metrics.mysql]
command = ["mackerel-plugin-mysql", "-host", "RDSのエンドポイント", "-username", "user", "-password", "pass"]
custom_identifier = "RDSのエンドポイント"
```

ふたつめの例は、 Amazon OpenSearch Service と [check-elasticsearch](https://github.com/mackerelio/go-check-plugins/tree/master/check-elasticsearch) プラグインを用いた OpenSearch 監視です。mackerel-agent.conf の設定に以下のように `custom_identifier` を含むプラグイン設定を追加することで、 OpenSearch Service クラスターのヘルスチェックを OpenSearch Service ホストのチェック監視として集約できます。

```toml
[plugin.checks.elasticsearch]
command = ["check-elasticsearch", "-s", "https", "-H", "OpenSearch Service のエンドポイント", "-p", "443"]
custom_identifier = "OpenSearch Service の ARN"
```
