---
Title: Organizations
Date: 2016-03-24T18:56:11+09:00
URL: https://mackerel.io/api-docs/entry/organizations
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/10328537792368377880
---

<ul class="internal-nav">
  <li><a href="#get">Get Organization Information</a></li>
  <li><a href="#ip-restriction-webconsole-post">Register Access Restriction of Web console Configurations</a></li>
  <li><a href="#ip-restriction-webconsole-get">Get Access Restriction of Web console Configurations</a></li>
  <li><a href="#ip-restriction-api-post">Register Access Restriction of API Configurations</a></li>
  <li><a href="#ip-restriction-api-get">Get Access Restriction of API Configurations</a></li>
</ul>

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

----------------------------------------------

<h2 id="ip-restriction-webconsole-post">Register Access Restriction of Web console Configurations</h2>

Register the access restriction settings for the Web console.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/settings/ip-restriction/web</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

| KEY | TYPE | DESCRIPTION |
| --- | --- | --- |
| `cidrs` | *array[string]* | A whitelist of IP ranges in CIDR notation that can access the organization's Web console[*1](#cidrs)  |

### Response

#### Success

```json
{
  "success": true
}
```

#### Error

<table class="default api-error-table">
  <thead>
    <tr>
      <th class="status-code">STATUS CODE</th>
      <th class="description">DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>400</td>
      <td>when the input is in an unacceptable format</td>    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

<h4 id="cidrs" class="annotation">*1 cidrs</h4>

To remove access restrictions, specify `[]` for <span class="table-code">cidrs</span>.

----------------------------------------------

<h2 id="ip-restriction-webconsole-get">Get Access Restriction of Web console Configurations</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/settings/ip-restriction/web</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

```json
{
  "cidrs": [<cidr>,<cidr>...]
}
```

<i>`<cidr>`</i> is an IP range in CIDR notation

----------------------------------------------

<h2 id="ip-restriction-api-post">Register Access Restriction of API Configurations</h2>

Register the access restriction settings for the API.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/settings/ip-restriction/api</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

| KEY | TYPE | DESCRIPTION |
| --- | --- | --- |
| `cidrs` | *array[string]* | A whitelist of IP ranges in CIDR notation that can access the organization's API[*2](#cidrs)  |

### Response

#### Success

```json
{
  "success": true
}
```

#### Error

<table class="default api-error-table">
  <thead>
    <tr>
      <th class="status-code">STATUS CODE</th>
      <th class="description">DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>400</td>
      <td>when the input is in an unacceptable format</td>    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

<h4 id="cidrs" class="annotation">*2 cidrs</h4>

To remove access restrictions, specify `[]` for <span class="table-code">cidrs</span>.

----------------------------------------------

<h2 id="ip-restriction-api-get">Get Access Restriction of API Configurations</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/settings/ip-restriction/api</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

```json
{
  "cidrs": [<cidr>,<cidr>...]
}
```

<i>`<cidr>`</i> is an IP range in CIDR notation
