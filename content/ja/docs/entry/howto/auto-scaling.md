---
Title: オートスケーリング環境で使う
Date: 2015-10-22T15:12:49+09:00
URL: https://mackerel.io/ja/docs/entry/howto/auto-scaling
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653458415125235326
---

このページでは、負荷に応じて自動的にサーバーを増減させるオートスケーリングが設定された環境で、Mackerelを便利に使うための方法を説明します。

[:contents]

## mackerel-agentの起動時にホストのステータスを`working`にする

オートスケーリング環境で起動したホストが、自動的に監視・通知対象となるように起動時にホストのステータスを `working` とすることを推奨します。
これは、mackerel-agentの設定ファイルに以下のような指定をすることで実現できます。

```
# /etc/mackerel-agent/mackerel-agent.conf
[host_status]
on_start = "working"
```

上記の設定をしなくてもオーガニゼーションの設定でデフォルトのステータスが `working` となっていれば同様の挙動となります。
詳しくは[アラートのヘルプ](https://mackerel.io/ja/docs/entry/howto/alerts)を参照してください。

## ホストを自動退役させる

Mackerelでは、ホストが停止しmackerel-agentが終了すると、connectivityアラートを発生させます。
これは通常のホストでは突然のホスト停止を検出できて有益です。

一方、オートスケーリング環境ではホスト停止は異常動作ではありませんので、ホスト停止時にMackerel上でもホストの退役処理を合わせて行うことで、アラートが発生しないようにできます。
ホストの自動退役の設定方法については以下を参照してください。

### 自動退役処理についての注意点

- 自動退役が有効な場合、OSのシャットダウン時に限らず、手動でmackerel-agentを停止した場合にもホストが退役されます。
- mackerel-agentによる自動退役は、mackerel-agentの停止時に、Mackerelへ退役リクエストを送信する仕組みです。OSのシャットダウン処理が優先され、リクエストが完了する前にエージェントが停止してしまった場合などは、正常に退役処理が行われないことがあります。
  - AWS環境をご利用の場合は、AWSインテグレーションによる自動退役もあわせて利用できますので、必要に応じてご検討ください。

### Linuxの場合

環境変数設定ファイルに以下の行を追記します。設定ファイルの場所は、mackerel-agent仕様の[環境変数を適用する](https://mackerel.io/ja/docs/entry/spec/agent#environment-variables)を参照してください。

```
AUTO_RETIREMENT=1
```

**シェルスクリプトで起動している場合**

mackerel-agentをパッケージでインストールしていない場合や、パッケージでインストールをしていても、initスクリプト経由ではなく独自のシェルスクリプトによって起動している場合、シェルスクリプトの終了時に退役処理が自動実行されるようにします。

退役処理の自動実行は、`mackerel-agent retire [-force]` という退役用のサブコマンドとtrapコマンドを利用することで実現できます。
次のようにmackerel-agentを起動するシェルスクリプトの中でtrapコマンドを利用した退役コマンドをシグナルハンドラとして仕込んでおきます。

```
trap 'mackerel-agent retire -conf /etc/mackerel-agent/mackerel-agent.conf -force' INT TERM
mackerel-agent -conf /etc/mackerel-agent/mackerel-agent.conf
```

これにより、SIGINTまたはSIGTERMが送られてきた場合に自動退役処理をしてから終了しますので、インスタンスの終了時にホストの退役処理が自動実行されるようになります。

### Windowsの場合

Windowsでmackerel-agentを利用している場合は、システム環境変数に以下の値を設定してください。

```
MACKEREL_AUTO_RETIREMENT=1
```

GUIを利用するか、またはコマンドプロンプトやPowerShellで以下のコマンドを実行することで設定できます。

```
setx MACKEREL_AUTO_RETIREMENT 1 -m
```

### AWSインテグレーションの場合

[AWSインテグレーション](https://mackerel.io/ja/docs/entry/integrations/aws)の[EC2連携](https://mackerel.io/ja/docs/entry/integrations/aws/ec2)を利用してホスト登録をおこなっている場合、AWSインテグレーションにより自動的に退役させることができます。

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200413/20200413124408.png:image]

この機能を有効にすると、インスタンスの終了がおこなわれた際に、自動的に退役処理が実行されるようになります。
この機能は、上述の mackerel-agent による自動退役と併用することができます。これは、自動退役をより確実に実行させるためにも効果的です。

## オートスケーリング対応のグラフを見る

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150730/20150730191150_original.png:image]

Mackerelでは、オートスケーリング環境でホストが増減したり入れ替わったりした際に、過去のホストも上の例のようにロールグラフ上に表示されます。ロールグラフとは、各サービス画面のロールごとにホストがまとめられたグラフのことです。

オートスケーリング環境下でも、ロールを指定することで見易いグラフを自動生成できますので、うまくサービス・ロールを指定してみてください。
具体的な設定方法は[mackerel-agent仕様のヘルプ](https://mackerel.io/ja/docs/entry/spec/agent#setting-services-and-roles)を参照ください。

## mackerel-agent がインストール済みのマシンイメージを作成する際の注意点

Mackerel では、管理対象のホストを一意に識別するために、各ホスト毎に「ホストID」を発行し、それに関連付ける形で管理をしています（[参照](https://mackerel.io/ja/docs/entry/spec/agent#faq)）。
このID情報は、Linux系OSのホストであれば「/var/lib/mackerel-agent/id」ファイルに、Windows OS の場合はインストールフォルダ内の「C:\Program Files\Mackerel\mackerel-agent\id」ファイルに記録されています。

オートスケールによるスケールアウト時に起動させるインスタンスのベースイメージとして、mackerel-agent がインストール済みのカスタムマシンイメージを作成する際には、このホストIDを記録したファイルが含まれた状態でイメージ化をしないよう、注意してください。
（削除せずにイメージ化し、それを元にスケールアウトがおこなわれた場合、Mackerel は起動した全てのサーバを同一のサーバとして識別します。）
