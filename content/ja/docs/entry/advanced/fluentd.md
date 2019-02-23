---
Title: fluentdでサービスメトリックを投稿する
Date: 2014-09-06T00:11:23+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/fluentd
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815732107307
---

サービスメトリックは、特定のサーバとは直接紐付かないメトリックを可視化、監視するための仕組みです。例えば、あるウェブサービス全体のレスポンスタイムや、ステータスコードの分布を可視化し、レスポンスが遅くなったり、エラー率が上昇していないか確認することができます。

このサービスメトリックは[fluent-plugin-mackerel][]を利用することで[fluentd][]経由で投稿することができます。

また具体的なサービスメトリックへの投稿例を2つ紹介しています。

* <a href="#example-nginx">Nginxのアクセスログからステータスコードごとにリクエスト数をサービスメトリックに投稿する</a>
* <a href="#example-elb">AWSのCloudWatchのメトリックからELB関連のメトリックをサービスメトリックに投稿する</a>

[fluent-plugin-mackerel]: https://github.com/tksmd/fluent-plugin-mackerel
[fluentd]: http://www.fluentd.org/

## セットアップ

まずfluentdとfluent-plugin-mackerelをセットアップします。

```
$ gem install fluentd fluent-plugin-mackerel
```

## サービスメトリックを投稿する

`fluent-plugin-mackerel`でサービスメトリックを投稿するための設定は以下のようになります。

```
<match ...>
  type mackerel
  api_key <投稿先オーガニゼーションのAPIキー>
  service <投稿先のサービス名>
  remove_prefix
  metrics_name http_status.${out_key}
  out_keys <投稿するメトリックのキー。例: 2xx_count,3xx_count,4xx_count,5xx_count>
</match>
```

## ホストのカスタムメトリックを投稿する

`fluent-plugin-mackerel`はホストのカスタムメトリックを投稿することもできます。その場合の設定は以下のようになります。

### ホストIDを直接指定する場合

ホストID（[用語集参照](https://mackerel.io/ja/docs/entry/glossary#host)）を設定ファイルに書く場合は次のように設定します。

```
<match ...>
  type mackerel
  api_key <投稿先オーガニゼーションのAPIキー>
  hostid xyz
  metrics_name http_status.${out_key}
  out_keys <投稿するメトリックのキー。例: 2xx_count,3xx_count,4xx_count,5xx_count>
</match>
```

またホストIDが格納されているファイルを指定することもできます。mackerel-agentが動作しているホストから直接Mackerelに投稿する場合に便利です。

```
<match ...>
  type mackerel
  api_key <投稿先オーガニゼーションのAPIキー>
  hostid_path /var/lib/mackerel-agent/id
  metrics_name http_status.${out_key}
  out_keys <投稿するメトリックのキー。例: 2xx_count,3xx_count,4xx_count,5xx_count>
</match>
```

## 使用例

ここでは、サービスメトリックへの投稿例を2つ紹介します。

* <a href="#example-nginx">Nginxのアクセスログからステータスコードごとにリクエスト数をサービスメトリックに投稿する</a>
* <a href="#example-elb">AWSのCloudWatchのメトリックからELB関連のメトリックをサービスメトリックに投稿する</a>

<h3 id="example-nginx">Nginxのアクセスログからステータスコードごとにリクエスト数をサービスメトリックに投稿する</h3>

ここでは次のようなステータスコード別にリクエスト数がカウントされたグラフを作ります。
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20140917/20140917011215.png" alt="f:id:mackerelio:20140917011215p:plain" title="f:id:mackerelio:20140917011215p:plain" class="hatena-fotolife" itemprop="image"></span></p>

まずNginxからアクセスログを[LTSV形式](http://ltsv.org)で出力するようにします。

```
log_format ltsv "time:$time_local"
                "\thost:$remote_addr"
                "\tforwardedfor:$http_x_forwarded_for"
                "\treq:$request"
                "\tstatus:$status"
                "\tsize:$body_bytes_sent"
                "\treferer:$http_referer"
                "\tua:$http_user_agent"
                "\treqtime:$request_time"
                "\tcache:$upstream_http_x_cache"
                "\truntime:$upstream_http_x_runtime"
                "\tvhost:$host";

access_log  /var/log/nginx/access.log  ltsv;
```

またステータスコードごとにリクエスト数を数えるためにfluent-plugin-datacounterを利用しますので、このプラグインをインストールします。
```
gem install fluent-plugin-datacounter
```

そしてNginxのアクセスログを読みこみ集計、Mackerelへの投稿を行うfluentdの設定を用意します。

```
# LTSV形式のログファイルを読み込む
<source>
  type tail
  format ltsv
  time_format %d/%b/%Y:%H:%M:%S %z
  path /var/log/nginx/access.log
  pos_file /var/log/nginx/access_log.pos
  tag access.nginx
</source>

# fluent-plugin-datacounterでステータスコード別に集計する
<match access.nginx>
  type datacounter
  count_interval 1m
  count_key status
  aggregate all
  tag nginx.status
  pattern1 2xx ^2\d\d$
  pattern2 3xx ^3\d\d$
  pattern3 4xx ^4\d\d$
  pattern4 5xx ^5\d\d$
</match>

# fluent-plugin-mackerelによりサービスメトリックを投稿する
<match nginx.status.**>
  type mackerel
  api_key <投稿先オーガニゼーションのAPIキー>
  service <投稿先サービス名>
  remove_prefix
  metrics_name access_num.${out_key}
  out_keys 2xx_count,3xx_count,4xx_count,5xx_count
</match>
```

これにより冒頭で示したようなグラフが描画されるようになります。またグラフの右上のギアマークからグラフの設定を行うことができます。例えば、重ね合わせグラフから積み重ねグラフに変えることができます。
<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20140917/20140917011221.png" alt="f:id:mackerelio:20140917011221p:plain" title="f:id:mackerelio:20140917011221p:plain" class="hatena-fotolife" itemprop="image"></span></p>

<h3 id="example-elb">AWSのCloudWatchのメトリックからELB関連のメトリックをサービスメトリックに投稿する</h3>

AWSのCloudWatchではロードバランサーのELBのリクエスト数(全体とステータスコード別)、応答時間といったメトリックデータを取得できます。それらをサービスメトリックとして投稿すると次のようなグラフが得られます。

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20140923/20140923113609.png)

AWSのCloudWatchからメトリックデータを読み込むためにfluent-plugin-cloudwatchを利用しますので、このプラグインをインストールします。
```
gem install fluent-plugin-cloudwatch
```

そしてCloudWatchからELBの情報を取り出し、Mackerelへの投稿を行うfluentdの設定を用意します。


```
# fluent-plugin-cloudwatchでCloudWatchからデータを取得
<source>
  type cloudwatch
  tag  cloudwatch-latency.elb01
  aws_key_id <aws-key>
  aws_sec_key <aws-sec-key>
  cw_endpoint monitoring.ap-northeast-1.amazonaws.com

  namespace AWS/ELB
  metric_name Latency
  dimensions_name LoadBalancerName
  dimensions_value elb01
  interval         60
  period           60
  delayed_start    true
</source>

<source>
  type cloudwatch
  tag  cloudwatch-status.elb01
  aws_key_id <aws-key>
  aws_sec_key <aws-sec-key>
  cw_endpoint monitoring.ap-northeast-1.amazonaws.com

  namespace AWS/ELB
  statistics Sum
  metric_name HTTPCode_Backend_2XX,HTTPCode_Backend_3XX,HTTPCode_Backend_4XX,HTTPCode_Backend_5XX,RequestCount
  dimensions_name LoadBalancerName
  dimensions_value elb01
  interval         60
  period           60
  delayed_start    true
</source>

# Mackerelへサービスメトリックとして投稿する
<match cloudwatch-status.*>
  type copy
  <store>
    type             mackerel
    api_key          <api-key>
    service           <投稿先サービス名>
    remove_prefix
    metrics_name     ${[1]}-status.${out_key}
    out_keys         HTTPCode_Backend_2XX,HTTPCode_Backend_3XX,HTTPCode_Backend_4XX,HTTPCode_Backend_5XX
  </store>
  <store>
    type             mackerel
    api_key          <api-key>
    service           <投稿先サービス名>
    remove_prefix
    metrics_name     ${[1]}-count.${out_key}
    out_keys         RequestCount
  </store>
</match>

<match cloudwatch-latency.*>
  type             mackerel
  api_key          <api-key>
  service         <投稿先サービス名>
  remove_prefix
  metrics_name     ${[1]}-latency.${out_key}
  out_keys         Latency
</match>
```

<!--
### Nginxのレスポンスタイムをグラフ化
-->
