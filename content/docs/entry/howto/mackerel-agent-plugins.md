---
Title: Using the official plugin pack to visualize middleware metrics
Date: 2015-02-16T18:16:25+09:00
URL: https://mackerel.io/docs/entry/howto/mackerel-agent-plugins
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450083908463
---

By using the official plugin pack, which supports many different kinds of middleware, it’s easy to gather middleware metrics. Supported environments will be the same as `mackerel-agent`, so for more information about that please refer to our [outline of supported environments](https://mackerel.io/docs/entry/overview#support-environments).

In order to monitor metrics for AWS services like ELB or RDS, consider using [AWS Integration](https://mackerel.io/docs/entry/integrations/aws). As for the posting of service metrics, please use [fluentd](http://www.fluentd.org/). For more information please refer to [Posting metrics related to ELB from AWS CloudWatch to service metrics.](https://mackerel.io/docs/entry/advanced/fluentd#example-elb)

Check out the following README for the plugin package list.

[https://github.com/mackerelio/mackerel-agent-plugins:embed:cite]

##Installing the official plugin pack

For installation we recommend using either the ‘yum’ or ‘apt’ repository according to the environment which you will be using. For information on configuring the repository, please refer to [Installing mackerel-agent][]. If you would like to acquire the ‘rpm’ and ‘deb’ files directly, please check [GitHub Releases][].

[Installing mackerel-agent]: https://mackerel.io/my/instruction-agent
[GitHub Releases]: https://github.com/mackerelio/mackerel-agent-plugins/releases

### if rpm package

```
yum install mackerel-agent-plugins
```

### if deb package

```
apt-get install mackerel-agent-plugins
```

##Using the official plugin pack

Each plugin has been installed in `/usr/bin` ,so please configure the mackerel-agent settings file to meet with the plugin which you will be using as shown below.

if using plugin for Apache2
```
[plugin.metrics.apache2]
command = ["mackerel-plugin-apache2"]
```

For more on how to use each plugin, refer below to the README of each plugin in github.

##Plugin pack code

The official plugin pack is open to the public and can be found here [https://github.com/mackerelio/mackerel-agent-plugins:title].
We are always happy to receive Pull Requests, comments, etc. regarding plugins that support new middleware.

For creating plugins please refer to [Posting custom metrics](https://mackerel.io/docs/entry/advanced/custom-metrics).

-

If you have any questions please contact our support team at support@mackerel.io
