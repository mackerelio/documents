---
Title: Azureインテグレーション
Date: 2017-05-15T12:16:43+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/azure
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/10328749687246501097
---

Azureインテグレーションを用いるとAzureクラウド製品をMackerelのホストとして管理し、メトリックを監視できます。本機能はTrialプランとStandardプランのみの提供となります。

Azureのクラウド製品1台が、Mackerelで1ホストとして登録され、Mackerelの課金対象のホスト数としてカウントされます。
ホストの種類は、Virtual Machinesについてはスタンダードホスト、その他の製品についてはマイクロホストとなります。
また、5分ごとに取得対象となるメトリックの数だけAzureのAPIをコールして値を取得します。そのため[Azure Monitor API利用の料金](https://azure.microsoft.com/ja-jp/pricing/details/monitor/)が発生する場合がありますのでご注意ください。

Azureインテグレーションは現在は以下のAzureクラウド製品に対応しています。取得メトリックなどについてはそれぞれのドキュメントを参照ください。[^1]

[SQL Database](https://mackerel.io/ja/docs/entry/integrations/azure/sql-database)・[Cache for Redis](https://mackerel.io/ja/docs/entry/integrations/azure/redis-cache)・[Virtual Machines](https://mackerel.io/ja/docs/entry/integrations/azure/virtual-machine)・[App Service](https://mackerel.io/ja/docs/entry/integrations/azure/app-service)・[Functions](https://mackerel.io/ja/docs/entry/integrations/azure/functions)・[Load Balancer](https://mackerel.io/ja/docs/entry/integrations/azure/load-balancer)・[Database for MySQL](https://mackerel.io/ja/docs/entry/integrations/azure/database-for-mysql)・[Database for PostgreSQL](https://mackerel.io/ja/docs/entry/integrations/azure/database-for-postgresql)・[Application Gateway](https://mackerel.io/ja/docs/entry/integrations/azure/application-gateway)・[Blob Storage](https://mackerel.io/ja/docs/entry/integrations/azure/blob-storage)・[Azure Files](https://mackerel.io/ja/docs/entry/integrations/azure/azure-files)・[Azure NetApp Files](https://mackerel.io/ja/docs/entry/integrations/azure/netapp-files)

[^1]: Azureメトリックスエクスプローラーの仕様に伴い、1メトリックのディメンションが50を超えた分は無視されます。

## 連携方法について
Azureインテグレーションはサービスプリンシパルを使って連携をします。

サービスプリンシパルとは、特定のAzureリソースにアクセスするのに使用するIDであり、ユーザーのIDを使うのと比べて、限られた権限を持つのでセキュリティ的により安心です。

Azureインテグレーションの設定の際に、「Microsoft Entra ID[^2]でアプリケーションを作成する」「サービスプリンシパルにロールを割り当てる」の両方の操作をするためのアクセス許可が必要となります。それぞれのアクセス許可を持っているかどうか確認するには以下の公式ドキュメントの「Microsoft Entra ID のアクセス許可を確認する」と「Azure サブスクリプションのアクセス許可を確認する」の項目をご確認ください。

[^2]: Azure Active DirectoryはMicrosoft Entra IDに改称されました。本ページに掲載している一部の画像は改称前のものとなっております。

[https://docs.microsoft.com/ja-jp/azure/azure-resource-manager/resource-group-create-service-principal-portal:embed:cite]

このヘルプドキュメントではAzure CLI 2.0とAzure PortalでのAzureインテグレーションの設定方法を紹介します。

[:contents]

## Azureアカウントの認証情報を設定する

Azure CLI 2.0 もしくは Azure Portal のいずれかの方法で認証情報を作成して、Azureインテグレーションに設定します。

### Azure CLI 2.0を用いて認証情報を取得する
このセクションでは、Azure CLIを用いた連携方法について説明します。

#### Azure CLI 2.0 インストール
[https://docs.microsoft.com/en-us/cli/azure/install-azure-cli:embed:cite]

#### ログイン、サービスプリンシパルの設定
まず以下のコマンドでAzureにログインしてください。
```console
$ az login
```

サービスプリンシパルの設定にはサブスクリプションIDが必要になるので、次のコマンドでサブスクリプションIDを事前に取得してください。

```console
$ az account show --query id --output tsv
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

次に以下のコマンドを利用して、Azureインテグレーションのためのサービスプリンシパルを作成し、同時に閲覧者権限も付与します。

`--years` は `password` の有効期限を設定するもので、デフォルトでは1年となります。有効期限が切れると再び設定するまでメトリック取得が不可能となりますのでご注意ください。

```console
$ az ad sp create-for-rbac --role Reader --scope /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --years <YEARS>
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
Mackerelの設定画面にてテナントID、クライアントID、シークレットキーが正しく設定されていることを確認してください。認証が失敗する場合は[こちら](#認証情報が正しいか確認する)をご確認ください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170621/20170621114150.png)

問題がなければ、[連携するサービスとサブスクリプションの選択](#連携するサービスとサブスクリプションを選択する)に進みます。

### Azure Portalを用いて認証情報を取得する

このセクションでは、Azure Portalを用いた連携方法について説明します。

#### Azure Portalにログイン
https://portal.azure.com にアクセスし、ログインしてください。

### テナントID取得
サイドバーからMicrosoft Entra IDを選択し、「プロパティ」を選択してください。

選択すると表示される「テナント ID」をMackerelのAzureインテグレーション設定画面の `テナントID` に入力してください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230722/20230722114731.png)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170621/20170621114147.png)

### Entra ID アプリケーションの作成
Mackerelとの連携の際にユーザーの権限ではなくアプリケーションの権限でメトリックの取得をするために、Microsoft Entra IDでアプリケーションの作成をします。

先ほどのEntra IDの画面から「アプリの登録」を選択してください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230722/20230722115601.png)

次に「新規登録」を選ぶと以下の様な画面になります。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230722/20230722115857.png)

Mackerel と連携するためのアプリケーションを登録してください。 アプリケーションの表示名とリダイレクトURIはMackerel側では利用しません。リダイレクトURIの項目で「パブリッククライアント/ネイティブ」「Web」「シングルページアプリケーション（SPA）」から選ぶ場面では「Web」を選択してください。

#### クライアントID、シークレットキーの取得
登録が完了したら、先ほどの画面から登録したアプリケーションを選択してください。以下の様な画面に移ります。「アプリケーション (クライアント) ID」をMackerelの画面の `クライアントID` の欄に入力してください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230722/20230722121556.png)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170621/20170621114144.png)

このアプリケーションの画面で次に「証明書とシークレット」を選択してください。ここではクライアントシークレットを作成できます。「新しいクライアントシークレット」をクリックし、シークレットの説明や有効期限を設定して保存するとシークレットの値が表示されます。こちらをコピーしMackerel側の `シークレットキー` の欄に入力してください。(権限設定がされていない間はMackerelの画面で「無効」と表示されます。「権限設定」の項目に進み、閲覧権限を付与してください）

**注意** こちらのシークレットの有効期限が切れますとその時点からMackerelのAzureインテグレーションでもメトリック取得が不可能となりますので、その際はシークレットを新たに作り直してください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230722/20230722122645.png)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230722/20230722122823.png)


#### 権限設定
上記でシークレットの取得と設定は完了ですが、最後に権限の付与も必要です。メトリック値を読み取るための権限を付与します。

Portal画面のサイドバーの「サブスクリプション」を選択してください。その後対象のサブスクリプションを選び、「アクセス制御 (IAM)」を選択します。ここではユーザーやサービスプリンシパルに対する権限の設定ができます。

今回は先ほど作成したEntra IDのアプリケーションに対して権限を設定します。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230722/20230722131806.png)

「追加」から「ロールの割り当ての追加」を押し、「役割」は「閲覧者」を選択して次に進んでください。（仮にここで閲覧者以外の権限を選択してしまった場合、安全のためMackerelのAzureインテグレーションはメトリック取得をしないようになっています）

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230722/20230722133143.png)

「アクセスの割り当て先」で「ユーザー、グループ、またはサービス プリンシパル」にチェックを付け、メンバーには先ほど作成したアプリケーションを選択してください。（注意: アプリケーション名の検索は前方一致なので先頭から入力しないと表示されません）

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230722/20230722132636.png)

「レビューと割り当て」をクリックしてしばらくすると、権限がアプリケーションに付与されます。

「ロールの割り当て」タブで「閲覧者」の欄の下に表示されているアプリケーションが正しいことを確認してください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230722/20230722134139.png)

Azureインテグレーションは複数サブスクリプションに対応していますので、上記の権限設定を監視したい全てのサブスクリプションで繰り返してください。

Mackerelの設定画面にてテナントID、クライアントID、シークレットキーが正しく設定されていることを確認してください。認証が失敗する場合は[こちら](#認証情報が正しいか確認する)をご確認ください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170621/20170621114150.png)


### 認証情報が正しいか確認する

設定に不備があり連携に失敗する場合、次のような理由が表示されます。

- 認証情報の利用に失敗しました。入力した認証情報を確認してください。
- 不必要に強い権限が付与されています。必要最低限の権限のみ付与してください。
- 現在、一時的に認証が行えない状態です。しばらく時間をおいてから再度お試しください。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230722/20230722134538.png)

認証情報や権限に不備がないか確認の上、設定の見直しを行ってください。

## 連携するサービスとサブスクリプションを選択する

リージョンを選択して、連携するサービスにチェックをします。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230830/20230830092148.png)


さらに連携するサブスクリプションを選択する場合は、任意のサブスクリプションにチェックを入れます。選択しない場合は、すべてのサブスクリプションが対象となります。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20230830/20230830092209.png)

連携ホストを確認した上で、設定を保存してください。しばらくすると、ホスト一覧にAzureインテグレーションによって作成されたホストが表示されます。


## タグで絞り込む

Mackerelの設定画面でタグを指定します。連携ホスト数を確認し、保存してください。

タグを `service:foo, service:bar` のように指定すると、キーがserviceで値がfooまたはキーがserviceで値がbarであるタグが付与されているインスタンスが対象となります。 キーや値にカンマ `,` などを含む場合は、クォート ( `"` または `'` ) で囲ってください。例えば、キーが `service,role` で値が `foo,bar` である場合は、 `"service,role": "foo,bar"` のように指定します。

## 新規メトリックの自動追加を設定する

Azureインテグレーションでは、Azureサービスのアップデートや新機能追加時に新たなメトリックが追加された場合、Mackerelが自動的にそのメトリックを検出し、監視対象に追加します。

この「新規メトリックを自動的に追加する」オプションをオンにすると、追加されたメトリックが自動的に取得対象となり、手動で設定を変更する手間を省けます。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20251030/20251030151129.png" width="1277" height="249" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

## 取得するメトリックを制限する

MackerelのAzureインテグレーション設定画面で取得するメトリックを選択します。不要なメトリックのチェックを外して、保存してください。

例えば`virtual_machine.cpu.percent`を取得しないようにする場合には以下のようにチェックボックスを外します。

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20251030/20251030151102.png" width="525" height="638" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>
