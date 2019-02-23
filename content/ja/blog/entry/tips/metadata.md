---
Title: Mackerel メタデータ入門・変動する IP アドレスを Mackerel で管理する
Date: 2017-03-21T14:27:19+09:00
URL: https://mackerel.io/ja/blog/entry/tips/metadata
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-ja.hatenablog.mackerel.io/atom/entry/10328749687228968899
---

こんにちは。Mackerel セールスエンジニアの id:a-know:detail です。

主に毎週のアップデート内容をお知らせしているこのブログですが、今日は Mackerel のメタデータ機能について、その活用例を交えつつご紹介したいと思います。


## 当記事の概要
AWS EC2 に自動的に割り振られるパブリック IP アドレスは、（Elastic IP を指定しない限りは）インスタンスの再起動などで変動してしまいます。そのため、`.ssh/config` に記述する IP アドレスも、変更が発生するたびに書き換える必要があり煩雑です。

今回は Mackerel のメタデータ機能を使用し、パブリック IP アドレスをホストのメタデータとして登録しておくことで、Mackerel メタデータ API から常に最新の IP アドレスを取得することができる ＆ API と各種自動化ツール（今回は Capistrano です）とを組み合わせることで、作業の効率化を図ることができる、という一例をご紹介します。



<!-- more -->


## ターゲット

サーバ監視・管理サービスである Mackerel のセールスエンジニアを担当している、ということもあり、私自身でも勉強も兼ねて、サーバ（AWS EC2 インスタンス）を2台ほど、運用してみています（もちろん mackerel-agent をインストールしています）。そのサーバには Web アプリケーション（Rails）をデプロイしていて、サーバへのリクエストは前段にいる ELB が受け付け、2台に配分されるように構成しています。図に表すと以下のようなイメージになります。





[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170320/20170320192908.png?1490005786:image=https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170320/20170320192908.png?1490005786]





とてもシンプルな構成です。

また、これらへのサーバインスタンスへのアプリケーションのデプロイは、GitHub と CircleCI、そして Capistrano を用いて「（feature ブランチがマージされるなどして）master ブランチの CI が通ったら自動的にデプロイ」するようにしています。これも図にすると、以下のような感じになるでしょうか。





[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170320/20170320192905.png?1490005814:image=https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170320/20170320192905.png?1490005814]





CircleCI 上でのデプロイはどのように行っているかをかいつまんでご紹介すると、circle.yml の `deployment` セクションを以下のように指定して、

```yaml
deployment:
  production:
    branch: master
    commands:
      - sh script/deploy_production.sh:
          timeout: 1500
```

script/deploy_production.sh の中身は以下のようにしていました。

```sh
#!/bin/sh

set -ex

cat << EOF >> ~/.ssh/config
Host ec2-instance01
  HostName xx.xxx.xx.xxx

Host ec2-instance02
  HostName xx.xxx.xx.xxx
EOF

bundle exec cap production deploy
```

config/deploy/production.rb は以下の通りです。

```ruby
set :branch, 'master'

set :rails_env, :production
role :web, %w{a-know@ec2-instance01 a-know@ec2-instance02}
```


シンプルではありますが、`.ssh/config` に対象サーバの情報を追記していたりしていて、力技なところも否めません。


これら2つのインスタンスは、諸事情によりプライベートサブネット内には押し込めておらず、パブリックな IP アドレスを持っています。上述の Web アプリケーションをデプロイする際にはこのパブリックな IP アドレスをもとにデプロイを行なうわけなのですが、Elastic IP を割り当てているわけではないので、そのアドレスはインスタンスの再起動などで簡単に変わってしまいます。

つまり、パブリック IP アドレスが変わるたびに上記の script/deploy_production.sh の内容も書き換えなければならないわけです。ElasticIP を割り当てれば良いじゃないか、というとそれまでではあるのですが、今回はこの解決に Mackerel のメタデータを活用してみたいと思います。


## Mackerel で管理されている各ホストのメタデータにパブリック IP アドレスを記録する

Mackerel には「メタデータ」という機能があります。



[https://mackerel.io/ja/docs/entry/howto/metadata:embed:cite]



この機能は、Mackerel で管理されるホストなどの要素に対して、任意の key - value（JSON データ）を登録することができるものです。現在はホストに対してのみ登録することが可能です。

[メタデータ API](https://mackerel.io/ja/api-docs/entry/metadata) を使用して登録することももちろんできるのですが、先日のリリースで mackerel-agent 経由でのメタデータ登録にも対応しました。


[https://mackerel.io/ja/blog/entry/weekly/20170224:embed:cite]



メタデータ形式の JSON を標準出力で出力するようなコード・スクリプトを作り、それを mackerel-agent のプラグインとして conf に指定することによって、JSON データをそのホストのメタデータとして登録させることができるのです。

今回はこの機能を使って、まずは各 EC2 インスタンスのパブリック IP アドレスをメタデータとして登録させてみましょう。

`メタデータ形式の JSON を標準出力で出力するようなスクリプト` ですが、今回の場合は以下のようなものになります。Ruby で書いてあります。

```ruby
require 'json'

ip_addr = `curl -Ssq http://169.254.169.254/latest/meta-data/public-ipv4`
puts [ { public_ip_addr: ip_addr } ].to_json
```

`http://169.254.169.254/latest/meta-data/public-ipv4` というのは、AWS が提供しているインスタンスメタデータ API のエンドポイントです。こちらも「メタデータ」で少々ややこしいのですが・・・。詳細は[公式ドキュメント](http://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/ec2-instance-metadata.html)を参照してください。

試しにこの Ruby スクリプトを EC2 インスタンス内で手動実行してみましょう。

```sh
$ ruby /etc/mackerel-agent/instance_meta_data.rb
[{"public_ip_addr":"xx.xx.xxx.xx"}]
```

このとおり、JSON 形式のオブジェクトが標準出力されています。

これを、mackerel-agent にプラグインとして組み込みます。以下のような記述を mackerel-agent.conf に加えます。

```toml
[plugin.metadata.aws_instance_metadata]
command = "ruby /path/to/instance_meta_data.rb"
execution_interval = 60
```

ここの記述に関する詳細は[ヘルプページ](https://mackerel.io/ja/docs/entry/howto/metadata)を参考にしてください。


conf に記述を追記したら、エージェントを再起動して設定を反映させましょう。そして、きちんとメタデータとしてパブリック IP アドレスが登録されたかどうかを確認してみましょう。これは自分の PC からでも OK です。

```sh
$ curl -XGET https://mackerel.io/api/v0/hosts/<host_id>/metadata/aws_instance_metadata -H 'X-Api-Key:<MACKEREL_API_KEY>'
[{"public_ip_addr":"xx.xx.xxx.xx"}]
```

API エンドポイントの `aws_instance_metadata` の部分は、conf に追記した `[plugin.metadata.aws_instance_metadata]` のところと対応しています。

`<host_id>` は対象ホストを一意に識別するための ID で、対象サーバ内の `/var/lib/mackerel-agent/id` に記録されています。また `<MACKEREL_API_KEY>` は https://mackerel.io/my?tab=apikeys で確認できます。APIキーは Read Only なものを発行しておいてもいいかもしれませんね。

ここまでの手順で、きちんとメタデータが登録され、API で取得できるところまで確認できました。



## ホストがサービス・ロールに自動的に所属されるようにする

ここで少しだけ脇道に逸れますが。

Mackerel には「サービス」「ロール」という概念があります。あるサービスを提供するために存在しているホストを束ねるための概念が「サービス」であり、その「サービス」内での役割ごとに設定するものが「ロール」です。

サーバ内にインストールされた mackerel-agent が起動したら、そのサーバを「サービス」「ロール」に自動的に所属するように設定ファイルに追記をしておきます。

```toml
roles = [ "xxx-service:web" ]
```

`サービス名:ロール名` のフォーマットで指定します。この設定は後ほど活用します。


## `.ssh/config` を動的に生成する

このメタデータを活用するように、script/deploy_production.sh の処理を以下のように書き換えます。

```sh
#!/bin/sh

set -ex

ruby script/edit_ssh_config.rb

bundle exec cap production deploy
```

Ruby スクリプト `script/edit_ssh_config.rb` の中身は以下のようなものです。

```ruby
require 'json'

service_name = 'xxx-service'
role_name    = 'web'

hosts_json = `curl -Ss -XGET "https://mackerel.io/api/v0/hosts?service=#{service_name}&role=#{role_name}" -H 'X-Api-Key:#{ENV['MACKEREL_APIKEY_READONLY']}'`
hosts = JSON.parse(hosts_json)['hosts'].map{|n| n['id']}

hosts.each do |host|
  metadata_json = `curl -Ss -XGET https://mackerel.io/api/v0/hosts/#{host}/metadata/aws_instance_metadata -H 'X-Api-Key:#{ENV['MACKEREL_APIKEY_READONLY']}'`
  ip_addr = JSON.parse(metadata_json).find{|data| data.has_key?('public_ip_addr')}['public_ip_addr']
  host_json = `curl -Ss -XGET https://mackerel.io/api/v0/hosts/#{host} -H 'X-Api-Key:#{ENV['MACKEREL_APIKEY_READONLY']}'`
  host_name = JSON.parse(host_json)['host']['name']

  f = File.open("/home/ubuntu/.ssh/config", "a")

  f.print <<EOS
Host #{host_name}
  HostName #{ip_addr}
  User a-know

EOS

  f.close
end
```

ここでも結局やっていることは `.ssh/config` ファイルの作成なのですが、そのファイルの中身を以下のような流れで生成しています。

1. Mackerel のホスト一覧取得 API をリクエストして、デプロイ対象のホストIDを取得する
    * さきほど設定しておいたサービス・ロールを指定することで、デプロイ対象外のホストを取ってこないようにしています
2. 取得できたホストIDごとに、メタデータ API をリクエストしパブリック IP アドレスを取得する
3. パブリック IP アドレスと、同じく取得したホスト名とを一緒に `.ssh/config` に対して追記する


同じように config/deploy/production.rb にも手を加えて、修正は完了です。修正後の仕組みも図で表すとすると、以下のようなイメージになります。



[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170320/20170320192901.png?1490006002:image=https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20170320/20170320192901.png?1490006002]




このような仕組みを構築することで、インスタンスの再起動などによるパブリック IP アドレス変更への追従はもちろんのこと、一時的なサーバ台数の増減などにも自動的に対応できるようになりました（1台になったり3台になったりしても問題なく対応できます）。


## まとめ

今回ご紹介したような方法により、「今現在、どんなサーバが何台存在しているか」ということを開発者が全く意識しなくても良くなりました。さらなる改善案としては、サービス・ロールを外部から与えられるようにしたり、Mackerel で管理されているホストのステータス（参考：[https://mackerel.io/ja/docs/entry/howto/alerts:title]）も考慮できるようにする、などがあるかと思います。[Mackerel のコマンドラインツール・mkr](https://mackerel.io/ja/docs/entry/advanced/cli) を使うことで、API のリクエストを行っているところのあたりをすっきりさせることもできそうですね。



[https://mackerel.io/ja/docs/entry/advanced/cli:embed:cite]



Mackerel のメタデータ API と自動化ツール（今回は Capistrano）との相性はとても良いです。Mackerel ユーザーのみなさんもぜひ、便利な使い方を考えてみてください。
