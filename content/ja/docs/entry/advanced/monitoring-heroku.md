---
Title: HerokuをMackerelで監視する
Date: 2015-10-22T15:12:51+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/monitoring-heroku
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6653458415125235971
---

**Herokuでのご利用は公式サポートの対象外です。**

[Heroku](https://www.heroku.com/)はウェブアプリケーションを動かすためのPaaSです。
ここでは、Mackerelを利用してHeroku上のアプリケーションを監視する方法を紹介します。

ただし、公式サポート対象とはなっておりませんので、使用上のご質問等にお答えできかねます。なお、対応環境については[こちら](https://mackerel.io/ja/docs/entry/overview)をご確認ください。

[:contents]

## Dynoを監視する

Herokuは自動でホスト(Dyno)が増減しますので、[Auto Scaling環境で使う](https://mackerel.io/ja/docs/entry/howto/auto-scaling)の設定をHerokuに合わせて利用します。

Herokuでアプリケーションを起動するための `Procfile` では、シェルスクリプト `start.sh` を起動するようにし、`start.sh` の中で各種設定を行います。

`start.sh` の `<API-KEY>` , `<SERVICE>`, `<ROLE>` は適切に書き換えてください。
`<RUN-APP>`はアプリケーションの起動コマンド(例えば、node.jsであれば `node index.js` など)に書き換えてください。

- Procfile
```
web: ./start.sh
```
- start.sh
```sh
#!/bin/sh
set -x
# install mackerel-agent
mackerel_agent=/app/mackerel-agent/mackerel-agent
mackerel_agent_conf=/app/mackerel-agent/mackerel-agent.conf

cd /app/ && \
  curl -O http://file.mackerel.io/agent/tgz/mackerel-agent-latest.tar.gz && \
  tar xvfz mackerel-agent-latest.tar.gz
sh << SCRIPT
cat > $mackerel_agent_conf <<'EOF';
pidfile = "/app/mackerel-agent/mackerel-agent.pid"
root = "/app/mackerel-agent"
apikey = "<API-KEY>"
roles = [ "<SERVICE>:<ROLE>" ]
EOF
SCRIPT
trap "$mackerel_agent retire -conf $mackerel_agent_conf -force" TERM
$mackerel_agent -conf $mackerel_agent_conf -v &

<RUN-APP>
```

## サービスメトリックを投稿する

Herokuでは[Log Drains](https://devcenter.heroku.com/articles/log-drains)を使うことでアクセスログを含む各種ログを取得できます。

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20151022/20151022145204_original.png:image]

ここではHeroku上のRailsのログからレスポンスタイムとエラーレートを計算し、Mackerelのサービスメトリックに投稿する方法を紹介します。

HerokuでRailsを動かす方法は[公式ガイド](https://devcenter.heroku.com/articles/getting-started-with-rails4)を参照してください。

### RailsでJSON形式のログを出力する

[lograge](https://github.com/roidrage/lograge)を利用します。

```
# Gemfile
gem 'lograge'
```

```
# config/application.rb
module Testapp
  class Application < Rails::Application
    ...
    config.log_level = :info
    config.lograge.enabled = true
    config.lograge.formatter = Lograge::Formatters::Json.new
    ...
  end
end
```

### rsyslogを設定する

rsyslogとfluentdを動かすホストを用意します。まずはrsyslogの設定を行います。5140番ポートでHerokuからのログを受けつけますので、ファイアウォールの設定で5140番ポートを空けるようにしてください。

```
# /etc/rsyslog.conf
$ModLoad imtcp
$InputTCPServerRun 5140
```

### Heroku Drainsを設定する

`<IPADDR>` はrsyslogとfluentdを動かすホストのIPアドレスに置換してください。

```
# heroku drains:add syslog://<IPADDR>:5140
Successfully added drain syslog://<IPADDR>:5140
```

### fluentdを設定する

まずはtd-agentをインストールします。[公式ガイド](http://docs.fluentd.org/v0.12/categories/installation)に従ってください。

次に必要なプラグインをインストールします。
```
# /usr/sbin/td-agent-gem install fluent-plugin-mackerel fluent-plugin-numeric-monitor fluent-plugin-datacounter
```

td-agentの設定ファイルに以下を追記します。 `<API-KEY>` はAPIキーを、 `<APP>` は投稿先のサービス名に置換してください。
後半のfluent-plugin-mackerelの利用方法の詳細は[fluentdでサービスメトリックを投稿する](https://mackerel.io/ja/docs/entry/advanced/fluentd)をあわせて参照してください。

```
# /etc/td-agent/td-agent.conf
<source>
  @type tail
  path /var/log/heroku
  pos_file /var/log/td-agent/posfile_heroku.pos
  format /^(?<time>[^ ]*\s+[^ ]*\s+[^ ]*) (?<host>[^ ]*) (?<ident>[a-zA-Z0-9_\/\.\-]*)(?:\[(?<pid>[a-zA-Z0-9\.]+)\])? *(?<message>.*)$/
  time_format %b %d %H:%M:%S
  tag heroku.syslog
</source>

<match heroku.syslog>
  @type rewrite_tag_filter
  rewriterule1 message ^{" raw.heroku.access_log
</match>

<match raw.heroku.access_log>
  @type parser
  remove_prefix raw
  key_name message
  format json
  reserve_data true
</match>

<match heroku.access_log>
  @type copy
  <store>
    @type numeric_monitor
    count_interval 60s
    monitor_key duration
    output_per_tag yes
    percentiles 90,99
    tag_prefix aggregated
    input_tag_remove_prefix heroku
  </store>
  <store>
    @type datacounter
    count_interval 60s
    count_key status
    output_per_tag yes
    pattern1 2xx ^2\d\d$
    pattern2 3xx ^3\d\d$
    pattern3 4xx ^4\d\d$
    pattern4 5xx ^5\d\d$
    outcast_unmatched yes
    tag_prefix status_aggregated
    input_tag_remove_prefix heroku
  </store>
</match>

<match status_aggregated.access_log>
  @type copy
  <store>
    @type rewrite_tag_filter
    capitalize_regex_backreference yes
    rewriterule1 2xx_count      .+ status_count.access_log
    rewriterule2 3xx_count      .+ status_count.access_log
    rewriterule3 4xx_count      .+ status_count.access_log
    rewriterule4 5xx_count      .+ status_count.access_log
  </store>
  <store>
    @type rewrite_tag_filter
    capitalize_regex_backreference yes
    rewriterule3 4xx_percentage .+ error_status_percentage.access_log
    rewriterule4 5xx_percentage .+ error_status_percentage.access_log
  </store>
</match>

<match status_count.access_log>
  @type mackerel
  flush_interval 60s
  api_key <API-KEY>
  out_keys 2xx_count,3xx_count,4xx_count,5xx_count
  remove_prefix
  service <APP>
  metrics_name access_num.${out_key}
</match>

<match aggregated.access_log>
  @type mackerel
  flush_interval 60s
  api_key <API-KEY>
  out_keys avg,percentile_90,percentile_99
  remove_prefix
  service <APP>
  metrics_name access_latency.${out_key}
</match>

<match error_status_percentage.access_log>
  @type mackerel
  flush_interval 60s
  api_key <API-KEY>
  out_keys 4xx_percentage,5xx_percentage
  remove_prefix
  service <APP>
  metrics_name error_access_rate.${out_key}
</match>
```

### サービスメトリックの監視ルールを追加する

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20151022/20151022145201_original.png:image]

最後に設定したレスポンスタイムとエラーレートのサービスメトリックそれぞれに対して、監視ルールを追加しましょう。

具体的な方法は[監視・通知を設定する](https://mackerel.io/ja/docs/entry/howto/alerts)や[CLIツール mkr を使う](https://mackerel.io/ja/docs/entry/advanced/cli)を参照してください。
