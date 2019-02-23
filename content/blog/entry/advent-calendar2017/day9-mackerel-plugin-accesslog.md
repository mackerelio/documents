---
Title: 'A thorough explanation of mackerel-plugin-accesslog '
Date: 2018-01-12T12:06:18+09:00
URL: https://mackerel.io/blog/entry/advent-calendar2017/day9-mackerel-plugin-accesslog
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio.hatenablog.mackerel.io/atom/entry/8599973812335877257
---

Mackerel Product Owner (and sub-producer) id:Songmu:detail here. This is an article that I published on the ninth day of our [2017 Mackerel Advent Calendar](https://qiita.com/advent-calendar/2017/mackerel).

This article is an introduction for the official plugin [mackerel-plugin-accesslog](https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-accesslog). We worked hard on this plugin this year and we think it’s really useful.

This plugin aggregates and visualizes web server access logs.

## How to install

mackerel-plugin-accesslog is included in the official metric plugin package and can be used once the package has been installed. For details on how to install the package, see the following help page.

[https://mackerel.io/docs/entry/howto/mackerel-agent-plugins:title]

`go get` can be used in Go environments as shown below.

```
% go get github.com/mackerelio/mackerel-agent-plugins/mackerel-plugin-accesslog
```

## How to configure

Specifying the access log file name as a parameter is extremely easy. Simply specify the following in mackerel-agent.conf. 

```
[plugin.metrics.accesslog]
command = "mackerel-plugin-accesslog /path/to/access.log"
```

## Supported formats

The following log formats are supported.

- Normal Apache format logs (Common/Combined) used in Apache and Nginx 
- LTSV format logs

In addition to major Apache logs, LTSV (Labeled Tab-separated Values) access log format, a format commonly used in the Japanese web industry and throughout Hatena[^1], is also supported.

The LTSV access log must be in a format that uses the Recommended Label defined in [ltsv.org](http://ltsv.org/)[^2]. Regarding configuration method, refer to the description in [ltsv.org](http://ltsv.org/) .

I wrote the access log parser[^3], and I am proud to say that most logs can be parsed. However, I do strongly recommend using the LTSV log because response latency can only be aggregated with LTSV. Moreover, LTSV seems to have better parse efficiency, though I think the difference is slight.

## Graph items

The following items are aggregated and graphed.

- Access count of each status code
- Percentage of each status code
- Latency (only for LTSV log)
  - Average
  - 90/95/99 Percentile

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20180110/20180110172951.png)

As you can see in the above screenshot, visualization of these metrics is fairly straight forward.

Incidentally, mackerel-plugin-accesslog records the position of the read log file at the time of last execution and continues to read the log from that time. Therefore, values are collected every 1 minute when regularly executed from the agent.

Even if the logs are rotated, mackerel-plugin-accesslog searches for potential files before being rotated and, if located, reads it to the end and then reads the new log file from the beginning. As a result, aggregation is more accurate. This can be suppressed with a library called postailer[^4].

## Combining expression graphs and aggregating in one role 

As it’s typical for web servers to have multiple configurations, you may want to have graphs aggregated by role.

Mackerel has an experimental feature that lets you display customized graphs using expressions (commonly called: expression graphs[^5]). With this feature, you can display access log graphs aggregated by role.

Here, let's try displaying the access count and ratio of each status code aggregated in one role in a stacked graph. Also, please be aware that expression graphs are still an experimental feature and must be enabled in the organization settings.[^6]

[https://mackerel.io/docs/entry/advanced/advanced-graph:embed]

### Displaying the status code access count in stacked graphs

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20180110/20180110172936.png)

The expression for displaying the above graph follows below. Please rewrite the role name and metric name as appropriate.

```
group(
  stack(alias(sum(
    role(SugoiService:web,custom.accesslog.access_num.2xx_count)),
    '2xx_count')),
  stack(alias(sum(
    role(SugoiService:web,custom.accesslog.access_num.3xx_count)),
    '3xx_count')),
  stack(alias(sum(
    role(SugoiService:web,custom.accesslog.access_num.4xx_count)),
    '4xx_count')),
  stack(alias(sum(
    role(SugoiService:web,custom.accesslog.access_num.5xx_count)),
    '5xx_count')))
```

### Displaying the status code access percentage in stacked graphs

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20180110/20180110172947.png)

The expression for displaying this graph is a bit complex, but follows below. Please rewrite role name and metric name as appropriate here as well.

```
group(
  stack(alias(scale(
    divide(sum(role(SugoiService:web,custom.accesslog.access_num.2xx_count)),
    sum(role(SugoiService:web,custom.accesslog.access_num.total_count))),100),
    '2xx_rate')),
  stack(alias(scale(
    divide(sum(role(SugoiService:web,custom.accesslog.access_num.3xx_count)),
    sum(role(SugoiService:web,custom.accesslog.access_num.total_count))),100),
    '3xx_rate')),
  stack(alias(scale(
    divide(sum(role(SugoiService:web,custom.accesslog.access_num.4xx_count)),
    sum(role(SugoiService:web,custom.accesslog.access_num.total_count))),100),
    '4xx_rate')),
  stack(alias(scale(
    divide(sum(role(SugoiService:web,custom.accesslog.access_num.5xx_count)),
    sum(role(SugoiService:web,custom.accesslog.access_num.total_count))),100),
    '5xx_rate')))
```

### Monitoring for expression

It’s also possible to monitor the value obtained by expression. Please refer to the Help page for more information.

[https://mackerel.io/docs/entry/expression-monitoring:embed]

Expression graphs is a powerful feature, and it is extremely satisfying if you can write a useful expression correctly, but I’m aware that this feature may still be difficult to use so I am working on improvements. Requests are always welcome.

## Comparing use of fluentd

The official help page "[Posting service metrics with fluentd](https://mackerel.io/docs/entry/advanced/fluentd)" introduces a method to aggregate access logs using fluentd.[^7]

mackerel-plugin-accesslog is extremely useful because it makes access log aggregation possible without the use of fluentd.

However, that does not mean that the method using fluentd is no longer necessary. mackerel-plugin-accesslog is specifically for aggregation of a single server. It may be more convenient to use fluentd for situations with multiple servers or when performing arbitrary aggregation. As a matter of fact, in recent years it’s increasingly common that logs are not written in the server local file system and to rely on a log collector such as fluentd. In such a case, mackerel-plugin-access log can not be used.

## Summary

Mackerel-plugin-accesslog can be a useful feature, by all means, give it a try. I’m also looking forward to your issues and pull requests for improvements and amendments.

https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-accesslog

[^1]: [Labeled Tab Separated Values (LTSV) ノススメ](http://blog.stanaka.org/entry/2013/02/05/214833) (Japanese only)
[^2]: http://ltsv.org/
[^3]: [恐らくそれなりに正確なGoのApacheログパーザーを書いた](http://www.songmu.jp/riji/entry/2017-06-24-axslogparser.html) (Japanese only)
[^4]: https://github.com/Songmu/postailer
[^5]: [Displaying customized graphs](https://mackerel.io/docs/entry/advanced/advanced-graph)
[^6]: [Using Experimental Features](https://mackerel.io/docs/entry/advanced/experimental-features-config)
[^7]: [Post the number of requests for each status code from Nginx's access log to service metrics](https://mackerel.io/docs/entry/advanced/fluentd#example-nginx)
