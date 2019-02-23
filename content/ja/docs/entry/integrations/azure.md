---
Title: Azureインテグレーション
Date: 2017-05-15T12:16:43+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/azure
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10328749687246501097
---

Azureインテグレーションを用いるとAzureクラウド製品をMackerelのホストとして管理し、メトリックを監視することができます。本機能はTrialプランとStandardプランのみの提供となります。

Azureのクラウド製品1台が、Mackerelで1ホストとして登録され、Mackerelの課金対象のホスト数としてカウントされます。 また、5分ごとに取得対象となるメトリックの数だけAzureのAPIをコールして値を取得します。そのため[Azure Monitor API利用の料金](https://azure.microsoft.com/ja-jp/pricing/details/monitor/)が発生する場合がありますのでご注意ください。

Azureインテグレーションは現在は以下のAzureクラウド製品に対応しています。取得メトリックなどについてはそれぞれのドキュメントを参照ください。

[SQL Database](https://mackerel.io/ja/docs/entry/integrations/azure/sql-database)・[Redis Cache](https://mackerel.io/ja/docs/entry/integrations/azure/redis-cache)・[Virtual Machine](https://mackerel.io/ja/docs/entry/integrations/azure/virtual-machine)

# 連携方法
Azureインテグレーションはサービスプリンシパルを使って連携をします。

サービスプリンシパルとは、特定のAzureリソースにアクセスするのに使用するIDであり、ユーザーのIDを使うのと比べて、限られた権限を持つのでセキュリティ的により安心です。

Azureインテグレーションの設定の際に、「Azure Active Directoryでアプリケーションを作成する」「サービスプリンシパルにロールを割り当てる」の両方の操作をするためのアクセス許可が必要となります。それぞれのアクセス許可を持っているかどうか確認するには以下の公式ドキュメントの「Azure Active Directory のアクセス許可を確認する」と「Azure サブスクリプションのアクセス許可を確認する」の項目をご確認ください。

[https://docs.microsoft.com/ja-jp/azure/azure-resource-manager/resource-group-create-service-principal-portal:embed:cite]

このヘルプドキュメントではAzure CLI 2.0とAzure PortalでのAzureインテグレーションの設定方法を紹介します。

[:contents]

## Azure CLI 2.0を用いた連携方法
このセクションでは、Azure CLIを用いた連携方法について説明します。

### Azure CLI 2.0 インストール
[https://docs.microsoft.com/en-us/cli/azure/install-azure-cli:embed:cite]

### ログイン、サービスプリンシパルの設定
まず以下のコマンドでAzureにログインしてください。
```console
$ az login
```

次に以下のコマンドを利用して、Azureインテグレーションのためのサービスプリンシパルを作成し、同時に閲覧者権限も付与します。

`--years` は `password` の有効期限を設定するもので、デフォルトでは1年となります。有効期限が切れると再び設定するまでメトリック取得が不可能となりますのでご注意ください。

```console
$ az ad sp create-for-rbac --role Reader --years <YEARS>
{
  "appId": "abcdefgh-abcd-efgh-abcd-abcdefghijkl",
  "displayName": "azure-cli-2017-01-23-45-67-89",
  "name": "http://azure-cli-2017-01-23-45-67-89",
  "password": "foofoo-bar-bar-foo-foobarbaz",
  "tenantId": "12345678-1234-5678-1234-123456781234",
}
```

`create-for-rbac` のオプションなどについては以下をご参照ください。

[https://docs.microsoft.com/en-us/cli/azure/ad/sp#create-for-rbac:embed:cite]

上記の結果のうち、 `tenantId` の値をMackerelの `テナントID` に、 `appId` の値を `クライアントID` に、 `password` を `シークレットキー` に設定してください。
Mackerelの設定画面にてテナントID、クライアントID、シークレットキーの他、リージョンとサービスとタグが正しく設定されていることを今一度ご確認ください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170621/20170621114150.png)
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170719/20170719110914.png)

連携ホストを確認した上で、設定を保存してください。しばらくすると、ホスト一覧にAzureインテグレーションによって作成されたホストが表示されます。

Azure CLI 2.0を用いた連携方法は以上です。

## Azure Portalを用いた連携方法
このセクションでは、Azure Portalを用いた連携方法について説明します。

### Azure Portalにログイン
https://portal.azure.com にアクセスし、ログインしてください。

### テナントID取得
サイドバーからAzure Active Directoryを選択し、「プロパティ」を選択してください。

選択すると表示される「ディレクトリID」をMackerelのAzureインテグレーション設定画面の `テナントID` に入力してください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170621/20170621114134.png)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170621/20170621114147.png)

### Active Directory アプリケーションの作成
Mackerelとの連携の際にユーザの権限ではなくアプリケーションの権限でメトリックの取得をするために、Azure Active Directoryでアプリケーションの作成をします。

先ほどのActive Directoryの画面から「アプリの登録」を選択してください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170519/20170519111311.png)

次に「新しいアプリケーションの登録」を選ぶと以下の様な画面になります。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170519/20170519111414.png)

Mackerel と連携するためのアプリケーションを登録してください。 アプリケーションの名前とサインオンURLはMackerel側では利用しません。アプリケーションの種類は「Webアプリ/API」にしてください。

### クライアントID, シークレットキーの取得
登録が完了したら、先ほどの画面から登録したアプリケーションを選択してください。以下の様な画面に移ります。「アプリケーション ID」をMackerelの画面の `クライアントID` の欄に入力してください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170621/20170621114306.png)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170621/20170621114301.png)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170621/20170621114144.png)

このアプリケーションの画面で次に「キー」を選択してください。ここではキーを作成することができます。キーの説明や有効期限を設定し保存するとキーの値が表示されます。こちらをコピーしMackerel側の `シークレットキー` の欄に入力してください。(権限設定がされていない間はMackerelの画面で「無効」と表示されます。「権限設定」の項目に進み、閲覧権限を付与してください）

**注意** こちらのキーの有効期限が切れますとその時点からMackerelのAzureインテグレーションでもメトリック取得が不可能となりますので、その際はキーを新たに作り直してください。「期限なし」を選んだ場合は有効期限がすぐに切れることはありません。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170621/20170621114258.png)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170621/20170621114251.png)

### 権限設定
上記でキーの取得と設定は完了ですが、最後に権限の付与も必要です。メトリック値を読み取るための権限を付与します。

Portal画面のサイドバーの「サブスクリプション」を選択してください。その後対象のサブスクリプションを選びます。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170621/20170621114254.png)

「アクセス制御 (IAM)」を選択してください。ここではユーザやサービスプリンシパルに対する権限の設定ができます。今回は先ほど作成したActive Directoryのアプリケーションに対して権限を設定します。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170519/20170519115539.png)

「追加」を押し、「役割」は「閲覧者」を選択してください（仮にここで閲覧者以外の権限を選択してしまった場合、安全のためMackerelのAzureインテグレーションはメトリック取得をしないようになっています。）

「選択」の欄では先ほど作成したアプリケーションを選択してください。（注意: アプリケーション名の検索は完全一致なので間違えないように入力しないと表示されません）

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170519/20170519115536.png)

しばらくすると権限がアプリケーションに付与され、以下のように表示されます。「閲覧者」の欄の下に表示されているアプリケーションが正しいことを確認してください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170613/20170613163527.png)

Azureインテグレーションは複数サブスクリプションに対応していますので、上記の権限設定を監視したい全てのサブスクリプションで繰り返してください。

Mackerelの設定画面にてテナントID、クライアントID、シークレットキーの他、リージョンとサービスとタグが正しく設定されていることを今一度ご確認ください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170621/20170621114150.png)
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170719/20170719110914.png)

連携ホストを確認した上で、設定を保存してください。しばらくすると、ホスト一覧にAzureインテグレーションによって作成されたホストが表示されます。

Azure Portalを用いた連携方法は以上です。

## タグで絞り込む

Mackerelの設定画面でタグを指定します。連携ホスト数を確認し、保存してください。

タグを `service:foo, service:bar` のように指定すると、キーがserviceで値がfooまたはキーがserviceで値がbarであるタグが付与されているインスタンスが対象となります。 キーや値にカンマ `,` などを含む場合は、クォート ( `"` または `'` ) で囲ってください。例えば、キーが `service,role` で値が `foo,bar` である場合は、 `"service,role": "foo,bar"` のように指定します。

