---
Title: Auto Scaling環境で使う
Date: 2015-10-22T15:12:49+09:00
URL: https://mackerel.io/ja/docs/entry/howto/auto-scaling
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653458415125235326
---

[AWS Auto Scaling](https://aws.amazon.com/jp/autoscaling/)や[Heroku](https://www.heroku.com/)など、負荷に応じて自動的にサーバーを増減させるAuto Scalingの仕組みは徐々に普及しつつあります。
ここではAuto Scaling環境でMackerelをうまく使うための方法を紹介します。Herokuを利用している場合は、[HerokuをMackerelで監視する](https://mackerel.io/ja/docs/entry/advanced/monitoring-heroku)も合わせて参照してください。

[:contents]

# 自動退役処理についての注意点

mackerel-agentからの自動退役処理は、mackerel-agentがさまざまな環境にあわせてOSの終了を検知し、Mackerel本体に退役を知らせるリクエストを送ります。退役のリクエストが失敗した場合、mackerel-agentはリトライを行いますが、OSのシャットダウンプロセス内で実行される処理であるため、リクエストが成功する前にOSのシャットダウンが完了するなどの状況が起こりえます。

そのため、mackerel-agentによる自動退役処理は、実行環境との相性などによって成功しない場合があることをご承知おきください。

# mackerel-agentのAuto Scaling環境向け設定

まずはmackerel-agentのAuto Scaling環境に向けた推奨設定を紹介します。

## ホスト起動時にホストステータスを`working`にする

Auto Scaling環境で起動したホストが、自動的に監視・通知対象となるように起動時にホストのステータスを `working` とすることを推奨します。
これは、mackerel-agentの設定ファイルに以下のような指定をすることで実現できます。

```
# /etc/mackerel-agent/mackerel-agent.conf
[host_status]
on_start = "working"
```

上記の設定をしなくてもオーガニゼーションの設定でデフォルトのステータスが `working` となっていれば同様の挙動となります。
詳しくは[アラートのヘルプ](https://mackerel.io/ja/docs/entry/howto/alerts)を参照してください。

## ホスト縮退時に自動退役させる

Mackerelでは、ホストが停止しmackerel-agentが終了すると、connectivityアラートを発生させます。
これは通常のホストでは突然のホスト停止を検出できて有益です。

一方、Auto Scaling環境ではホスト停止は異常動作ではありませんので、
ホスト停止時にMackerel上でもホストの退役処理を合わせて行うことでアラートが発生しないようにします。
mackerel-agentの起動方法や利用OSによって次の3つの方法がありますので、ご利用の方法に合わせて設定してください。

### rpm/debでインストールしている

パッケージインストールした場合は環境変数設定ファイルに一行追記するだけで簡単に自動退役を実現できます。

環境変数設定ファイルはyum/rpmの場合は/etc/sysconfig/mackerel-agent、deb/aptの場合は、/etc/default/mackerel-agent となっており、そのファイルに次の1行を追記します。

```
AUTO_RETIREMENT=1
```

この指定をしておくと、シャットダウン時(例えば `/etc/init.d/mackerel-agent stop` 時)にホストが自動的に退役されます。

**注意** 手動で `/etc/init.d/mackerel-agent stop` を打った場合でもこの指定がされていた場合にはホストは退役されてしまいます。エージェントのバージョンアップ時など、エージェントをstop/startさせたい場合には、 `/etc/init.d/mackerel-agent reload` を使うようにして下さい。

詳細は[mackerel-agent仕様](https://mackerel.io/ja/docs/entry/spec/agent)及び[自動退役や自動ステータス変更 - オートスケールやスタンバイ機の管理のための自動化](https://mackerel.io/ja/blog/entry/2015/07/31/105300)を参照ください。

### シェルスクリプトで起動している

パッケージインストールではない場合や、パッケージインストールをしていてもinitスクリプト経由ではなく独自のシェルスクリプトによって起動している場合、シェルスクリプトの終了時に退役処理が自動実行されるようにします。

退役処理の自動実行は、`mackerel-agent retire [-force]` という退役用のサブコマンドとtrapコマンドを利用することで実現できます。
次のようにmackerel-agentを起動するシェルスクリプトの中でtrapコマンドを利用した退役コマンドをシグナルハンドラとして仕込んでおきます。

```
trap 'mackerel-agent retire -conf /etc/mackerel-agent/mackerel-agent.conf -force' INT TERM
mackerel-agent -conf /etc/mackerel-agent/mackerel-agent.conf
```

これにより、SIGINTまたはSIGTERMが送られてきた場合に自動退役処理をしてから終了しますので、herokuやAWSのAuto Scaling環境下でホスト増減に合わせてホスト登録・退役処理が自動実行されるようになります。

### Windows版mackerel-agentを利用している

Windowsでmackerel-agentを利用している場合は、システム環境変数に以下の値を設定してください。

```
MACKEREL_AUTO_RETIREMENT=1
```

GUIを利用するか、またはコマンドプロンプトやPowerShellで以下のコマンドを実行することで設定できます。

```
setx MACKEREL_AUTO_RETIREMENT 1 -m
```

# Auto Scaling対応のグラフを見る

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150730/20150730191150_original.png:image]

MackerelではAuto Scaling環境でホストが増減したり入れ替わった際に過去のホストも、上の例のようにロールグラフ上に表示されます。
ロールグラフとは、各サービス画面のロールごとにホストがまとめられたグラフのことです。

Auto Scaling環境下でも、ロールを指定することで見易いグラフを自動生成できますので、うまくサービス・ロールを指定してみてください。
具体的な設定方法は[mackerel-agent仕様のヘルプ](https://mackerel.io/ja/docs/entry/spec/agent#setting-services-and-roles)を参照ください。

# mackerel-agent がインストール済みのマシンイメージを作成する際の注意点

Mackerel では、管理対象のホストを一意に識別するために、各ホスト毎に「ホストID」を発行し、それに関連付ける形で管理をしています（[参照](https://mackerel.io/ja/docs/entry/spec/agent#faq)）。
このID情報は、Linux系OSのホストであれば「/var/lib/mackerel-agent/id」ファイルに、Windows OS の場合はインストールフォルダ内の「id」ファイルに、記録されています。

オートスケールによるスケールアウト時に起動させるインスタンスのベースイメージとして、mackerel-agent がインストール済みのカスタムマシンイメージを作成する際には、このホストIDを記録したファイルが含まれた状態でイメージ化をしないよう、注意してください。
（削除せずにイメージ化し、それを元にスケールアウトがおこなわれた場合、Mackerel は起動した全てのサーバを同一のサーバとして識別します。）
