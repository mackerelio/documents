---
Title: Organizations
Date: 2016-03-24T18:56:11+09:00
URL: https://mackerel.io/api-docs/entry/organizations
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/10328537792368377880
---

<h2 id="get">Get Organization Information</h2>

This will get the information of the organization.

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/org</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

```json
{
  "name": <name>,
  "displayName": <displayName>
}
```

| KEY            | TYPE     | DESCRIPTION |
| -------------- | -------- | ----------- |
| `name`         | *string* | The name of the organization |
| `displayName`  | *string* / *null* | The display name of the organization |
