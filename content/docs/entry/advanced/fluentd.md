---
Title: Posting Service Metrics with fluentd
Date: 2014-11-11T17:51:13+09:00
URL: https://mackerel.io/docs/entry/advanced/fluentd
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450073212101
---

"Service Metrics" is a construct used for visualizing and monitoring metrics that are not directly tied to any specific server, making it possible, for example, to visualize an entire web service's response time and status code distribution, and check if response time is slowing down or if the error rate is going up. 

Using [fluent-plugin-mackerel][] service metrics can be posted to Mackerel via [fluentd][].

Next we'll introduce two concrete examples of posting service metrics.

* <a href="#example-nginx">Post the number of requests for each status code from Nginx's access log to service metrics.</a>
* <a href="#example-elb">Post ELB-related metrics from AWS CloudWatch's metrics to service metrics</a>

[fluent-plugin-mackerel]: https://github.com/tksmd/fluent-plugin-mackerel
[fluentd]: http://www.fluentd.org/

## Setup

First we'll need to install fluentd and fluent-plugin-mackerel:

```
$ gem install fluentd fluent-plugin-mackerel
```

## Posting service metrics

Settings for posting service metrics with `fluent-plugin-mackerel` are as follows.

```
<match ...>
  type mackerel
  api_key <API key of the organization that will be posted to>
  service <name of the service that will be posted to>
  remove_prefix
  metrics_name http_status.${out_key}
  out_keys <keys of the metrics that will be posted, e.g. 2xx_count,3xx_count,4xx_count,5xx_count>
</match>
```

## Posting custom host metrics

With `fluent-plugin-mackerel` you can post custom host metrics. The settings for which are as follows.

### If directly specifying the host ID

If you will be writing the host ID [(view glossary)](https://mackerel.io/docs/entry/glossary#host) in the configuration file, your settings will need to be as follows.

```
<match ...>
  type mackerel
  api_key <API key of the organization that will be posted to>
  hostid xyz
  metrics_name http_status.${out_key}
  out_keys <keys of the metrics that will be posted, e.g. 2xx_count,3xx_count,4xx_count,5xx_count>
</match>
```

It's also possible to specify a file where the host ID is saved. This is convenient when posting to Mackerel directly from a host on which the mackerel-agent is operating. 

```
<match ...>
  type mackerel
  api_key <API key of the organization that will be posted to>
  hostid_path /var/lib/mackerel-agent/id
  metrics_name http_status.${out_key}
  out_keys <keys of the metrics that will be posted, e.g. 2xx_count,3xx_count,4xx_count,5xx_count>
</match>
```

## Example usages

Here we will introduce two concrete examples of posting service metrics.

* <a href="#example-nginx">Post the number of requests for each status code from Nginx's access log to service metrics.</a>
* <a href="#example-elb">Post ELB-related metrics from AWS CloudWatch's metrics to service metrics</a>

<h3 id="example-nginx">Post the number of requests for each status code from Nginx's access log to service metrics.</h3>

In this example we'll make a graph like the one shown below that counts the number of requests for each status code.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20140917/20140917011215.png" alt="f:id:mackerelio:20140917011215p:plain" title="f:id:mackerelio:20140917011215p:plain" class="hatena-fotolife" itemprop="image"></span></p>

First, we'll make it so that the access log from Nginx outputs in [LTSV](http://ltsv.org) format.

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

fluent-plugin-datacounter will be used so that the number of requests are counted for each status code, so we will need to install this plugin.

```
gem install fluent-plugin-datacounter
```

Then we will prepare the configurations for the fluentd that will read and aggregate Nginx's access log and post it to Mackerel.

```
# Reads LTSV format logs
<source>
  type tail
  format ltsv
  time_format %d/%b/%Y:%H:%M:%S %z
  path /var/log/nginx/access.log
  pos_file /var/log/nginx/access_log.pos
  tag access.nginx
</source>

# Aggregates for each status code with fluent-plugin-datacounter
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

# Post to service metrics with fluent-plugin-mackerel
<match nginx.status.**>
  type mackerel
  api_key <API key of the organization that will be posted to>
  service <name of the service that will be posted to>
  remove_prefix
  metrics_name access_num.${out_key}
  out_keys 2xx_count,3xx_count,4xx_count,5xx_count
</match>
```

And that's it! We just generated the graph shown below. Now by clicking the cog in the upper right-hand corner of the graph, you can configure the graph to your liking, for example changing it from a line graph to a stacked graph.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20140917/20140917011221.png" alt="f:id:mackerelio:20140917011221p:plain" title="f:id:mackerelio:20140917011221p:plain" class="hatena-fotolife" itemprop="image"></span></p>

<h3 id="example-elb">Post ELB-related metrics from AWS CloudWatch's metrics to service metrics</h3>

With AWS CloudWatch we can retrieve the number of ELB requests (in total as well as for each status code) and metric data such as response time. By posting that data as service metrics, the following graphs can be obtained.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20140923/20140923113609.png)

fluent-plugin-cloudwatch will be used to read the metric data from AWS CloudWatch, so we will need to install this plugin.

```
gem install fluent-plugin-cloudwatch
```

Next we will prepare the configurations for the fluentd that will retrieve the ELB data from CloudWatch and post it to Mackerel.

```
# Retrieves data from CloudWatch using fluent-plugin-cloudwatch
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

# Post them to Mackerel as service metrics
<match cloudwatch-status.*>
  type copy
  <store>
    type             mackerel
    api_key          <api-key>
    service          <service name to post metrics>
    remove_prefix
    metrics_name     ${[1]}-status.${out_key}
    out_keys         HTTPCode_Backend_2XX,HTTPCode_Backend_3XX,HTTPCode_Backend_4XX,HTTPCode_Backend_5XX
  </store>
  <store>
    type             mackerel
    api_key          <api-key>
    service          <service name to post metrics>
    remove_prefix
    metrics_name     ${[1]}-count.${out_key}
    out_keys         RequestCount
  </store>
</match>

<match cloudwatch-latency.*>
  type             mackerel
  api_key          <api-key>
  service          <service name to post metrics>
  remove_prefix
  metrics_name     ${[1]}-latency.${out_key}
  out_keys         Latency
</match>
```
-

If you have any questions please contact our support team at support@mackerel.io
