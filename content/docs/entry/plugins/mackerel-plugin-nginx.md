---
Title: Metric plugins - mackerel-plugin-nginx
Date: 2023-09-04T12:04:56+09:00
URL: https://mackerel.io/docs/entry/plugins/mackerel-plugin-nginx
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/820878482964568695
---

mackerel-plugin-nginx is a plugin to visualize nginx status based on nginx's stub_status.

[:contents]

<h2 id="metrics">Metrics you can monitor</h2>

#### Nginx Connections

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ------------------ | ------------------------------------ | - | - | ------------------------------------------------------------------- |
| Active connections | custom.nginx.connections.connections | | | Number of current connections from clients including Waiting status |

#### Nginx requests

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| -------------------- | ------------------------------ | - | - | -------------------------------------------- |
| Accepted connections | custom.nginx.requests.accepts  | ✓ | | Total number of client connections accepted  |
| Handled connections  | custom.nginx.requests.handled  | ✓ | | Total number of client connections processed |
| Handled requests     | custom.nginx.requests.requests | ✓ | | Total number of client requests              |

#### Nginx connection status

| Metric Display Name | Metric Name | Diff | Stacked | Description |
| ------- | -------------------------- | - | - | ------------------------------------------------------------------ |
| Reading | custom.nginx.queue.reading | | | Number of current connections reading request headers              |
| Writing | custom.nginx.queue.writing | | | Number of current connections writing responses back to the client |
| Waiting | custom.nginx.queue.waiting | | | Number of current idle client connections waiting for requests     |

<h2 id="options">Options that can be specified</h2>

The options that can be specified for the plugin are as follows.

| Option | Environment Variables | Description | Default |
| --------- | - | ------------------------------------------------------------------------------------------------------------- | ------------- |
| -uri      |   | URI of the connection destination                                                                             |               |
| -scheme   |   | HTTP scheme for connection (http or https)                                                                    | http          |
| -host     |   | hostname of the connection destination                                                                        | localhost     |
| -port     |   | port of the connection destination                                                                            | 8080          |
| -path     |   | path where stab_status is provided                                                                            | /nginx_status |
| -tempfile |   | path to save temporary files                                                                                  |               |
| -header   |   | Additional headers to be given when connecting (`-header` is required for each if more than one is specified) |               |

<h2 id="config">Example configuration for an agent</h2>

To monitor nginx serving stub_status as `/stub_status` on port 8081 on the localhost, configure as follows.

```toml
[plugin.metrics.nginx]
command = ["/path/to/mackerel-plugin-nginx","--port","8081","--path","/stab_status"]
```

In the above configuration, you can verify the operation by executing the following:

```
$ mackerel-plugin-nginx --port 8081 --path /stab_status
```

<h2 id="repository">Repository</h2>

https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-nginx

<h2 id="reference">Reference</h2>

See the description of the ngx_http_stub_status_module below for details on how to configure stub_status to be enabled and each variable referenced by the plugin.

[Module ngx_http_stub_status_module](http://nginx.org/en/docs/http/ngx_http_stub_status_module.html)
