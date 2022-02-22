---
Title: Amazon CloudWatch Logs のログを集計して Mackerel にメトリックを投稿する
Date: 2022-02-10T12:02:32+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/cloudwatch-logs-aggregator
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/13574176438061861957
CustomPath: advanced/cloudwatch-logs-aggregator
---

[cloudwatch-logs-aggregator](https://github.com/mackerelio-labs/mackerel-monitoring-modules/tree/main/cloudwatch-logs-aggregator) を使用すると、[Amazon CloudWatch Logs](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html) に出力されたログを集計し、その結果を Mackerel にサービスメトリックとして投稿できます。

アプリケーションの動作状況の定量的な監視や、グラフなどを用いた可視化を行うためには、数値データであるメトリックが重要になります。このようなメトリックは、具体的には以下のようなものが挙げられます。

- 処理されたデータの数
- データの処理にかかった速度
- 発生したエラー数

一方で、こういったメトリックをアプリケーションから直接出力するためには、

- 統計的処理を行う
- API を使ってメトリックを投稿する

といった仕組みが要求され、アプリケーションの実装が複雑化してしまいます。

cloudwatch-logs-aggregator は、アプリケーションから CloudWatch Logs に出力されたログをメトリックの元となるデータとして利用することで、上記のようなメトリックの出力に必要な仕組みをアプリケーションから切り離します。これによって、特に Amazon ECS や AWS Lambda といった AWS のクラウド環境で動作するアプリケーションについて、手軽にメトリックによる監視・可視化を行うことができるようになります。

## cloudwatch-logs-aggregator の概要
cloudwatch-logs-aggregator は、[CloudWatch Logs Insights](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/logs/AnalyzingLogData.html) を使用してログを集計し Mackerel にサービスメトリックを投稿するための Lambda 関数です。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220214/20220214181027.png" alt="構成図" width="600" height="470" loading="lazy" title="構成図" class="hatena-fotolife" itemprop="image"></span></p>

例えば CloudWatch Logs に出力された以下のようなアプリケーションのログを元にして、ログの件数や、ログに含まれる数値を集計した結果を、Mackerel にメトリックとして投稿できます。

``` json
{"level":"info","msg":"query complete","bytes_scanned":51828}
{"level":"info","msg":"query complete","bytes_scanned":124413}
...
```

<p><span itemscope="" itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220217/20220217110906.png" alt="ログを元にしたメトリックのグラフ" width="444" height="334" loading="lazy" title="ログを元にしたメトリックのグラフ" class="hatena-fotolife" itemprop="image"></span></p>

cloudwatch-logs-aggregator には、Lambda 関数をユーザーの AWS アカウント上で構築するためのコードが付属します。このコードは [Terraform](https://www.terraform.io) のモジュールとして提供されます。

## 使い方
以下の記事では、より実践的な使用例を手順ごとに詳しく紹介していますので、合わせてご覧ください。

- [cloudwatch-logs-aggregator で Amazon CloudWatch Logs のログのメトリック化を実践する](https://mackerel.io/ja/blog/entry/cloudwatch-logs-aggregator)

### 1. アプリケーションの準備
#### ログの出力先を CloudWatch Logs とする
cloudwatch-logs-aggregator を利用するためには、アプリケーションのログが CloudWatch Logs に対して出力されている必要があります。

ログの出力先を CloudWatch Logs に設定する方法については、各サービスのドキュメントをご確認ください。

- [コンテナインスタンスのモニタリング - Amazon Elastic Container Service](https://docs.aws.amazon.com/ja_jp/AmazonECS/latest/developerguide/using_cloudwatch_logs.html)
- [AWS Lambda の Amazon CloudWatch Logs へのアクセス - AWS Lambda](https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/monitoring-cloudwatchlogs.html)

#### メトリックの元となるログを出力する
アプリケーションからは、メトリックの元となるデータを含むログを出力します。

例えば、一定期間内に処理されたデータの件数・成功した件数・失敗した件数をメトリックとして記録する場合であれば、アプリケーションからは以下のようなログを出力します。

``` json
{"job_name":"foo","processed":18,"success":15,"failure":3}
```

ここで出力するログの形式は CloudWatch Logs Insights で解析できる必要があります。一般的には、JSON 形式のログを出力することをおすすめします。

### 2. cloudwatch-logs-aggregator のセットアップ
#### Mackerel の API キーの取得
cloudwatch-logs-aggregator でのメトリックの投稿に使用する API キーを [Mackerel の API キー画面](https://mackerel.io/my?tab=apikeys)から取得し、[AWS Systems Manager の Parameter Store](https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/systems-manager-parameter-store.html) に保存します。このときパラメータのタイプは SecureString とし、API キーを暗号化して保存することをおすすめします。

ここで設定したパラメータの名前は、後の設定で使用します。

#### Lambda 関数の作成
cloudwatch-logs-aggregator の本体となる Lambda 関数を作成します。

Lambda 関数の作成のためのコードは Terraform モジュールとして提供されます。[リポジトリのドキュメント](https://github.com/mackerelio-labs/mackerel-monitoring-modules/tree/main/cloudwatch-logs-aggregator#cloudwatch-logs-aggregator-lambda)を参照し、Terraform の設定ファイル (例: `main.tf`) からモジュールを利用してください。

#### EventBridge ルールの作成
Lambda 関数の実行間隔や、関数に与えるパラメータを設定するための Amazon EventBridge (CloudWatch Events) ルールを作成します。ログを集計する CloudWatch Logs Insights のクエリや、Mackerel のメトリック名などの設定についてもここで行います。

1 つの Lambda 関数に対して、複数のルールを作成できます。

EventBridge ルールの作成のためのコードも、Lambda 関数と同様に Terraform モジュールとして提供されます。[リポジトリのドキュメント](https://github.com/mackerelio-labs/mackerel-monitoring-modules/tree/main/cloudwatch-logs-aggregator#cloudwatch-logs-aggregator-rule)を参照し、Terraform の設定ファイルから利用してください。

- `function_arn` には、上で作成した Lambda 関数の ARN を設定します。
- `api_key_name` には、上で設定した Mackerel のAPI キーを保存した Parameter Store のパラメータの名前を設定します。
- `query` には CloudWatch Logs Insights のクエリを設定します。クエリの構文については [AWS のドキュメント](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/logs/CWL_QuerySyntax.html)を参照してください。[AWS のコンソール](https://console.aws.amazon.com/cloudwatch/home#logsV2:logs-insights)から実行結果を確認しながらクエリを作成するのがおすすめです。
- クエリや Mackerel のメトリック名についての設定は、[こちら](https://github.com/mackerelio-labs/mackerel-monitoring-modules/tree/main/cloudwatch-logs-aggregator#examples)にいくつか例を用意していますので、合わせてご参照ください。

## 料金
cloudwatch-logs-aggregator は無償で提供されますが、利用にあたっては Mackerel へのサービスメトリックの投稿数に応じた利用料金、および Lambda 関数の実行や CloudWatch Logs へのログの保存・検索などに伴う AWS の利用料金が発生します。

詳しくは以下の Mackerel および AWS の各サービスの料金ページをご確認ください。

- [料金 - Mackerel（マカレル）: 新世代のサーバー管理・監視サービス](https://ja.mackerel.io/pricing)
- [料金 - AWS Lambda ｜AWS](https://aws.amazon.com/jp/lambda/pricing/)
- [料金 - Amazon CloudWatch | AWS](https://aws.amazon.com/jp/cloudwatch/pricing/)
