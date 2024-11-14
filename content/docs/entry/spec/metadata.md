---
Title: Metadata API for Mackerel
Date: 2024-11-13T17:23:55+09:00
URL: https://mackerel.io/docs/entry/spec/metadata
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398303666139
---

This section describes the Metadata API that provides information about Mackerel.

The base URL is <code>https://mackerel.io/meta/</code>.
<br>
The API key of the organization is not necessary.

[:contents]

## IP address ranges

It represents the range of IP addresses Mackerel uses.

<code>GET https://mackerel.io/meta/ip-ranges.json</code>

```json
{
  "incoming": [
    "13.113.31.156/32",
    ...
  ],
  "outgoing": [
    "52.193.111.118/32",
    ...
  ]
}
```

|KEY|TYPE|DESCRIPTION|
|--|--|--|
|`incoming`|*array[string]*|IP address ranges that Mackerel receives<br><ul><li>[Mackerel's IP address ranges](https://support.mackerel.io/hc/en-us/articles/360039633271)</li></ul>|
|`outgoing`|*array[string]*|IP address ranges that Mackerel sends<br><ul><li>[Source IP address ranges for Webhook and other services’ alert notifications from Mackerel](https://support.mackerel.io/hc/en-us/articles/360039701332)</li></ul>|
