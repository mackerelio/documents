---
Title: Dockerをモニタリングする
Date: 2015-09-17T18:19:54+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/docker
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653458415121696517
---

Docker( https://www.docker.com/ )をモニタリングする方法を紹介します。

Dockerコンテナの消費リソースを把握するには、Dockerが利用しているリソースの統計情報を参照します。
Dockerのリソースの統計情報はAPI( https://docs.docker.com/reference/api/engine/ )を利用して取得します。
また後方互換性のためにcgroupのファイルシステム経由でメトリックすることもできますが、現在は非推奨です。
技術的な詳細は公式ドキュメント( https://docs.docker.com/engine/containers/runmetrics/ )を参照してください。

## mackerel-plugin-dockerを利用する

Mackerelではmackerel-plugin-dockerを利用することで、各コンテナのリソース消費の統計情報を取得しカスタムメトリックグラフとして可視化できます。
mackerel-plugin-dockerは公式プラグイン集に含まれていますので、まずは公式プラグイン集をインストールします。具体的な手順は[ミドルウェアのメトリック可視化に公式プラグイン集を使う](https://mackerel.io/ja/docs/entry/howto/mackerel-agent-plugins)を参照してください。

公式プラグイン集がインストールできたら、以下の設定を`/etc/mackerel-agent/mackerel-agent.conf`に追記します。

```
[plugin.metrics.docker]
command = ["mackerel-plugin-docker", "-name-format", "name"]
```

これにより、そのホスト上で動作するDockerコンテナのCPU使用率、メモリ消費量、IO使用量(IOPS、転送バイト数とキュー長)が可視化されます。

コンテナを新規に起動すると自動的にグラフに項目が追加され、コンテナを終了すると数時間後に自動的にその項目が見れなくなります。

mackerel-plugin-dockerでは、`-name-format`でコンテナごとのメトリック名を指定しています。上の例では`name`を指定しており、各コンテナのNameを利用します。Nameはいくつかの方法で指定できますが、典型的には`docker run`の`--name`オプションで指定します( https://docs.docker.com/reference/cli/docker/container/run/#name )。

- -name-format
  - name .. コンテナのName
  - name_id .. コンテナのName + '_' + IDの先頭6文字
  - id .. コンテナのID
  - image .. コンテナのイメージ名
  - image_id .. コンテナのイメージ名 + '_' + IDの先頭6文字
  - image_name .. コンテナのイメージ名 + '_' + コンテナのName
  - label .. 指定したラベル( https://docs.docker.com/reference/dockerfile/#label )

`label` を指定した場合は、 `-label`オプションでどのkeyを利用するかを指定します。

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150917/20150917161045_original.png:image=http://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150917/20150917161045_original.png]
[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150917/20150917161041_original.png:image=http://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150917/20150917161041_original.png]

また `-method` オプションでAPIではなくファイルシステム経由でメトリック情報を収集させることもできます。その場合は、 `-method File`と指定してください。ただし非推奨であることに注意してください。

## mackerel-agentのDockerイメージ

mackerel-agentのDockerイメージを以下で提供しています。

https://hub.docker.com/r/mackerel/mackerel-agent/

これを利用することで、mackerel-agentを1つのコンテナとして起動することができ、下図のようにホストや他のコンテナを監視できます。

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150917/20150917161407.png:image=https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150917/20150917161407.png]

## mackerel-agentをコンテナとして起動する

mackerel-agentイメージによりコンテナを起動するには次のコマンドを実行します。
`<APIKEY>`を所属するオーガニゼーションのAPI KEYで置き換えます。
`enable_docker_plugin`は、mackerel-plugin-dockerを動かすかどうかのフラグとなります。
`auto_retirement`は、コンテナ終了時にMackerel上のサーバーを自動的に退役させるかどうかのフラグになり、デフォルトではオフとなっています。
`opts`は、mackerel-agentに渡される引数となります。
`--name`はコンテナ名となります。これらのオプションは必要に応じて変更してください。

```
docker run -h `hostname` \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /var/lib/mackerel-agent/:/var/lib/mackerel-agent/ \
  -e 'apikey=<APIKEY>' \
  -e 'enable_docker_plugin=1' \
  -e 'auto_retirement=0' \
  -e 'opts=-v' \
  --name mackerel-agent \
  -d \
  mackerel/mackerel-agent
```

### 他のコンテナのプロセスを監視する

他のコンテナのプロセスを監視するには、Dockerの`link`オプションを利用します。
`link` オプションを利用することで他のコンテナのIPアドレスやポート番号を環境変数経由で取得できます。
各環境変数は、prefixが `<name>_PORT_<port>_<protocol>` となり、prefixにIPアドレス(ADDR)、ポート番号(PORT)、プロトコル(PROTO)をそれぞれ加えた環境変数( `prefix_ADDR`, `prefix_PORT`, `prefix_PROTO` )で取得できます。

`link` オプションの詳細はDockerのドキュメントをご参照ください。 ( https://docs.docker.com/engine/network/links/ )


ここではmemcachedの例を紹介します。

まずmemcachedコンテナをmemcachedという名前で起動します。

```
docker run -d -P \
  --name memcached -p 11211:11211 \
  sylvainlasnier/memcached
```

次にMackerelのmemcachedプラグインの設定ファイルを準備します。
このファイルはホスト側に置きます。Dockerのlinkオプションにより、環境変数`MEMCACHED_PORT_11211_TCP_ADDR`でそのコンテナのIPアドレスが取得できます。

```
% cat /etc/mackerel-agent/conf.d/memcached.conf
[plugin.metrics.memcached]
command = "mackerel-plugin-memcached -host=$MEMCACHED_PORT_11211_TCP_ADDR"
```

最後にこのmemcachedコンテナとリンクしたmackerel-agentコンテナを起動します。
ここで`-v`オプションと`include`を利用して上の設定ファイルを読み込みます。

```
docker run -h `hostname` \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /var/lib/mackerel-agent/:/var/lib/mackerel-agent/ \
  -e 'apikey=<APIKEY>' \
  -e 'enable_docker_plugin=1' \
  -e 'auto_retirement=0' \
  -e 'opts=-v' \
  --link memcached:memcached \
  -v /etc/mackerel-agent/conf.d:/etc/mackerel-agent/conf.d:ro \
  -e 'include=/etc/mackerel-agent/conf.d/*.conf' \
  --name mackerel-agent \
  -d \
  mackerel/mackerel-agent
```

これでmemcachedコンテナを監視するmackerel-agentコンテナを起動できます。
