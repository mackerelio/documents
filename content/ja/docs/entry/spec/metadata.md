---
Title: Mackerelに関するメタデータAPI
Date: 2024-11-13T17:23:59+09:00
URL: https://mackerel.io/ja/docs/entry/spec/metadata
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398303666150
---

Mackerelに関する情報を提供するメタデータAPIについて解説します。

ベースURLは<code>https://mackerel.io/meta/</code>です。
<br>
オーガニゼーションのAPIキーは必要ありません。

[:contents]

## IPアドレス範囲

Mackerelが使用するIPアドレス範囲を表します。

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
|`incoming`|*array[string]*|Mackerelが受信に使用するIPアドレス範囲です<br><ul><li>[サービスがホストされているIPアドレス範囲](https://support.mackerel.io/hc/ja/articles/360039633271)</li></ul>|
|`outgoing`|*array[string]*|Mackerelが送信に使用するIPアドレス範囲です<br><ul><li>[Webhook通知や外形監視などの通知元IPアドレス範囲](https://support.mackerel.io/hc/ja/articles/360039701332)</li></ul>|
