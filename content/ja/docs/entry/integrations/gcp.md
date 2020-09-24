---
Title: Google Cloudインテグレーション
Date: 2020-09-15T12:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/gcp
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613631816920
---

Google Cloudインテグレーションを用いるとGoogle Cloud PlatformのサービスをMackerelのホストとして管理し、メトリックを監視できます。本機能はTrialプランとStandardプランのみの提供となります。

Google Cloudのサービスが、Mackerelでホストとして登録され、Mackerelの課金対象ホストとしてカウントされます。
ホストの種類は、Compute Engineについてはスタンダードホスト、その他のサービスについてはマイクロホストとなります。
また、5分ごとに取得対象となるメトリックの数だけGoogle Cloud PlatformのAPIをコールして値を取得します。そのため[Monitoring の料金](https://cloud.google.com/stackdriver/pricing#monitoring-costs)が発生する場合がありますのでご注意ください。

Google Cloudインテグレーションは以下のGoogle Cloudサービスに対応しています。取得メトリックなどはそれぞれのドキュメントを参照ください。

- [Compute Engine](https://mackerel.io/ja/docs/entry/integrations/gcp/gce)
- [Cloud SQL](https://mackerel.io/ja/docs/entry/integrations/gcp/cloudsql)
- [App Engine](https://mackerel.io/ja/docs/entry/integrations/gcp/appengine)

# 連携方法
Google Cloudインテグレーションはサービスアカウントキーを使って連携します。

サービスアカウントとは、Google Cloud Platform上のアプリケーションやインスタンスで使用されるアカウントです。Google Cloudインテグレーションでは一般的なユーザーアカウントは使用できません。サービスアカウントは目的に応じて限られた権限だけを持つのでセキュリティ的により安全です。サービスアカウントについては以下の公式ドキュメントをご確認ください。

[https://cloud.google.com/iam/docs/service-accounts:cite]

[:contents]

## Cloud SDKを用いた連携方法
このセクションでは、Cloud SDKを用いた連携方法について説明します。

### Cloud SDKのインストール
[https://cloud.google.com/sdk/docs/downloads-versioned-archives:cite]

### ログイン
まず以下のコマンドでCloud SDKを承認し、セットアップを行ってください。ここでは、Google Cloudインテグレーションの設定を行うプロジェクトIDをセットします。

```sh
$ gcloud config configurations create NAME
$ gcloud init --configuration NAME
```

異なるプロジェクトIDをセットしている場合は、以下のコマンドで切り替えが可能です。

```sh
$ gcloud config set project --configuration=NAME PROJECT-ID
```

### Cloud APIの有効化
次に以下のコマンドを利用して、Mackerelがメトリックを取得するためCloud APIを有効にします。

```sh
# 常に必要となるAPI
$ gcloud services --configuration=NAME enable cloudresourcemanager.googleapis.com
$ gcloud services --configuration=NAME enable monitoring.googleapis.com

## インテグレーションで設定したサービスごとに必要となるAPI

# Compute Engineを連携する場合
$ gcloud services --configuration=NAME enable compute.googleapis.com
# Cloud SQLを連携する場合
$ gcloud services --configuration=NAME enable sqladmin.googleapis.com
# App Engineを連携する場合
$ gcloud services --configuration=NAME enable appengine.googleapis.com
```

### サービスアカウントキーの作成
Mackerelがメトリックを取得するためのサービスアカウントを作成して、ロールを割り当ててください。安全のため、viewer以上の権限またはグローバルな権限が付与されている場合はMackerelで利用することができませんので、ownerなど強力な権限を付与しないようにご注意ください。

```sh
$ gcloud iam --configuration=NAME service-accounts create ACCOUNT --display-name='Mackerel Integration' --description='Mackerel account'

# 必須ロール割り当て
$ gcloud projects --configuration=NAME add-iam-policy-binding PROJECT-ID --member=serviceAccount:ACCOUNT@PROJECT-ID.iam.gserviceaccount.com --role=roles/browser
$ gcloud projects --configuration=NAME add-iam-policy-binding PROJECT-ID --member=serviceAccount:ACCOUNT@PROJECT-ID.iam.gserviceaccount.com --role=roles/monitoring.viewer

## リソース固有のロール

# Compute Engineを連携する場合
$ gcloud projects --configuration=NAME add-iam-policy-binding PROJECT-ID --member=serviceAccount:ACCOUNT@PROJECT-ID.iam.gserviceaccount.com --role=roles/compute.viewer
# Cloud SQLを連携する場合
$ gcloud projects --configuration=NAME add-iam-policy-binding PROJECT-ID --member=serviceAccount:ACCOUNT@PROJECT-ID.iam.gserviceaccount.com --role=roles/cloudsql.viewer
# App Engineを連携する場合
$ gcloud projects --configuration=NAME add-iam-policy-binding PROJECT-ID --member=serviceAccount:ACCOUNT@PROJECT-ID.iam.gserviceaccount.com --role=roles/appengine.appViewer
```

準備を終えたらサービスアカウントキーを発行してください。以下の例では、**mackerel-key.json**ファイルに保存されます。

```sh
$ gcloud iam --configuration=NAME service-accounts keys create mackerel-key.json --iam-account=ACCOUNT@PROJECT-ID.iam.gserviceaccount.com
```

MackerelのGoogle Cloudインテグレーション設定画面にて、Google CloudのプロジェクトIDを`プロジェクトID`に、ここで発行した**mackerel-key.json**の内容を`サービスアカウントキー`に設定してください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200902/20200902154643.png)

連携ホストを確認した上で、設定を保存してください。しばらくすると、ホスト一覧にGoogle Cloudインテグレーションによって作成されたホストが表示されます。

## Cloud Consoleを用いた連携方法

### Cloud Consoleへログイン
https://console.cloud.google.com にアクセスし、ログインしてください。

### Cloud APIの有効化
[APIライブラリ](https://console.cloud.google.com/apis/library)に移動し、以下の2つを有効にします。

- Cloud Resource Manager API
- Stackdriver Monitoring API

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200902/20200902154922.png)
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200902/20200902155023.png)

必要に応じて、以下のAPIも有効にしてください。

- Compute Engine API (Compute Engineインスタンスの監視が必要な場合)
- Cloud SQL Admin API (Cloud SQLインスタンスの監視が必要な場合)
- App Engine Admin API (App Engineサービスの監視が必要な場合)

### サービスアカウントキーの作成
Mackerelがメトリックを取得するためのサービスアカウントを作成して、ロールを割り当ててください。安全のため、viewer以上の権限またはグローバルな権限が付与されている場合はMackerelで利用することができませんので、ownerなど強力な権限を付与しないようにご注意ください。

[サービス アカウント](https://console.cloud.google.com/iam-admin/serviceaccounts)に移動し、Mackerelと連携するサービスアカウントを作成します。以下の例では **example** アカウントとしました。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200902/20200902155152.png)

続けて、必要なロールを追加して「続行」します。

- 参照者(必須)
- モニタリング閲覧者(必須)
- Compute 閲覧者(Compute Engineインスタンスの監視が必要な場合)
- Cloud SQL 閲覧者(Cloud SQLインスタンスの監視が必要な場合)
- App Engine 閲覧者(App Engineサービスの監視が必要な場合)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200902/20200902155247.png)

サービスアカウントへのアクセス権は、特に必要ありませんのでそのまま「完了」してください。そうするとサービスアカウントが作られますが、Mackerelから連携する際に必要なサービスアカウントキーはまだ発行されていません。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200902/20200902155750.png)

先ほど作成したサービスアカウントの詳細ページを開いて、「新しい鍵を作成」します。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200902/20200902160116.png)

キーのタイプは「JSON」を選んでください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200902/20200902160209.png)

MackerelのGoogle Cloudインテグレーション設定画面にて、Google CloudのプロジェクトIDを`プロジェクトID`に、ここで発行したサービスアカウントキーの内容を`サービスアカウントキー`に設定してください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200902/20200902154643.png)

連携ホストを確認した上で、設定を保存してください。しばらくすると、ホスト一覧にGoogle Cloudインテグレーションによって作成されたホストが表示されます。
