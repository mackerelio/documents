---
Title: オーガニゼーション
Date: 2016-03-22T11:00:53+09:00
URL: https://mackerel.io/ja/api-docs/entry/organizations
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/10328537792368067612
---

<h2 id="get">オーガニゼーションの情報を取得</h2>

オーガニゼーションの情報を取得します。

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/org</code>
</p>

### APIキーに必要な権限

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### 応答

```json
{
  "name": <name>
}
```

| KEY            | TYPE     | DESCRIPTION |
| -------------- | -------- | ----------- |
| `name`         | *string* | オーガニゼーションの名前。 |
