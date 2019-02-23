---
Title: Monitoring Heroku with Mackerel
Date: 2015-10-23T18:20:10+09:00
URL: https://mackerel.io/docs/entry/advanced/monitoring-heroku
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6653458415125592212
---

[Heroku](https://www.heroku.com/) is a PaaS for running web applications.
Here we will introduce and talk about how to monitor applications on Heroku using Mackerel.

[:contents]

## Monitoring Dyno

Heroku automatically increases and decreases hosts (Dyno), so we’ll use the configurations in [Using Mackerel in Auto Scaling environments](https://mackerel.io/docs/entry/howto/auto-scaling) in arrangement with Heroku.

With the `Procfile` which launches your application in Heroku, we will make it so that the shell script `start.sh` is launched, and do a variety of configurations in `start.sh`.

Please fill in `<API-KEY>` , `<SERVICE>`, and `<ROLE>` in `start.sh` appropriately.
Replace `<RUN-APP>` with the command to launch your application (for example, `node index.js` if node.js, etc.).

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

## Posting service metrics

In Heroku, it’s possible to get a variety of logs including the access log by using [Log Drains](https://devcenter.heroku.com/articles/log-drains).

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20151022/20151022145204_original.png:image]

Here we will talk about how to calculate response time and error rate from the Rails log on Heroku, and how to post them to Mackerel’s service metrics.

Please also refer to the official guide [Getting Started with Rails 4.x on Heroku](https://devcenter.heroku.com/articles/getting-started-with-rails4).

### Outputting JSON format logs with Rails

We will use [lograge](https://github.com/roidrage/lograge).

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

### Configuring rsyslog

Next we will prepare the host that will run rsyslog and fluentd. First we’ll configure rsyslog. We will receive the log from Heroku on port 5140, so please open port 5140 in your firewall settings.

```
# /etc/rsyslog.conf
$ModLoad imtcp
$InputTCPServerRun 5140
```

### Setting up Heroku Drains

Replace `<IPADDR>` with the IP address of the host that will run rsyslog and fluentd.

```
# heroku drains:add syslog://<IPADDR>:5140
Successfully added drain syslog://<IPADDR>:5140
```

### Configuring fluentd

First we will install td-agent. Please follow the instructions in the [official guide](http://docs.fluentd.org/v0.12/categories/installation).

Next we will install the necessary plugins.
```
# /usr/sbin/td-agent-gem install fluent-plugin-mackerel fluent-plugin-numeric-monitor fluent-plugin-datacounter
```

We will append the following in the settings file for td-agent. Replace `<API-KEY>` with the API key and `<APP>` with the name of the service it will be posted in.
Refer to [Posting Service Metrics with fluentd](https://mackerel.io/docs/entry/advanced/fluentd) for how to use the fluent-plugin-mackerel which is used in the second half of the settings file below.

```
# /etc/td-agent/td-agent.conf
<source>
  type tail
  path /var/log/heroku
  pos_file /var/log/td-agent/posfile_heroku.pos
  format /^(?<time>[^ ]*\s+[^ ]*\s+[^ ]*) (?<host>[^ ]*) (?<ident>[a-zA-Z0-9_\/\.\-]*)(?:\[(?<pid>[a-zA-Z0-9\.]+)\])? *(?<message>.*)$/
  time_format %b %d %H:%M:%S
  tag heroku.syslog
</source>

<match heroku.syslog>
  type rewrite_tag_filter
  rewriterule1 message ^{" raw.heroku.access_log
</match>

<match raw.heroku.access_log>
  type parser
  remove_prefix raw
  key_name message
  format json
  reserve_data true
</match>

<match heroku.access_log>
  type copy
  <store>
    type numeric_monitor
    count_interval 60s
    monitor_key duration
    output_per_tag yes
    percentiles 90,99
    tag_prefix aggregated
    input_tag_remove_prefix heroku
  </store>
  <store>
    type datacounter
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
  type copy
  <store>
    type rewrite_tag_filter
    capitalize_regex_backreference yes
    rewriterule1 2xx_count      .+ status_count.access_log
    rewriterule2 3xx_count      .+ status_count.access_log
    rewriterule3 4xx_count      .+ status_count.access_log
    rewriterule4 5xx_count      .+ status_count.access_log
  </store>
  <store>
    type rewrite_tag_filter
    capitalize_regex_backreference yes
    rewriterule3 4xx_percentage .+ error_status_percentage.access_log
    rewriterule4 5xx_percentage .+ error_status_percentage.access_log
  </store>
</match>

<match status_count.access_log>
  type mackerel
  flush_interval 60s
  api_key <API-KEY>
  out_keys 2xx_count,3xx_count,4xx_count,5xx_count
  remove_prefix
  service <APP>
  metrics_name access_num.${out_key}
</match>

<match aggregated.access_log>
  type mackerel
  flush_interval 60s
  api_key <API-KEY>
  out_keys avg,percentile_90,percentile_99
  remove_prefix
  service <APP>
  metrics_name access_latency.${out_key}
</match>

<match error_status_percentage.access_log>
  type mackerel
  flush_interval 60s
  api_key <API-KEY>
  out_keys 4xx_percentage,5xx_percentage
  remove_prefix
  service <APP>
  metrics_name error_access_rate.${out_key}
</match>
```

### Adding service metric monitoring rules

[https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20151022/20151022145201_original.png:image]

Finally, let’s add monitoring rules for each of the response time and error rate service metrics we’ve configured.

For specific instructions please refer to [Setting up monitoring and alerts](https://mackerel.io/docs/entry/howto/alerts) and [Using the mkr CLI tool](https://mackerel.io/docs/entry/advanced/cli).
