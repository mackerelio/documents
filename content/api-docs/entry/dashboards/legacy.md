---
Title: Dashboards (Legacy)
Date: 2018-11-19T10:59:40+09:00
URL: https://mackerel.io/api-docs/entry/dashboards/legacy
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/10257846132672301050
---

<ul class="internal-nav">
  <li><a href="#create">Create Dashboards</a></li>
  <li><a href="#get">Get Dashboards</a></li>
  <li><a href="#update">Update Dashboards</a></li>
  <li><a href="#delete">Delete Dashboards</a></li>
  <li><a href="https://mackerel.io/api-docs/entry/dashboards#list">List of Dashboards</a></li>
</ul>

This page explains the API for Custom Dashboards (Legacy Custom Dashboards) before the feature's renovation as of November 2018. For details regarding the API operating the new feature following its renovation, refer [here](https://mackerel.io/api-docs/entry/dashboards).

<h2 id="create">Create Dashboards</h2>

This section covers creating dashboards.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/dashboards</code>
</p>

It's not possible to specify a URL path that is already being used to create a new dashboard.

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

Objects that hold the following keys:

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `title`   | *string* | the dashboard's name |
| `bodyMarkdown`   | *string* | the particulars of the dashboard written in Markdown |
| `urlPath` | *string* | the dashboard's URL path *1 |

*1 URL path should be as follows:

`^([A-Za-z0-9_][-A-Za-z0-9_]*)(/[A-Za-z0-9_][-A-Za-z0-9_]*)*$`

### Response

#### Success

The dashboard that was created is returned.

```json
{
  "id": <dashboardId>,
  "title": "My Dashboard",
  "bodyMarkdown": "# A test dashboard",
  "urlPath": "2u4PP3TJqbu",
  "createdAt": 1439346145003,
  "updatedAt": 1439346145003
}
```

`<dashboardId>` : This is the ID that was assigned to this dashboard. With future requests, this ID will identify the dashboard.

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
      <td>when the input is in a format that can’t be accepted</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when you have reached your dashboard limit / when the API key doesn't have the required permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">permitted IP address range</a></td>
    </tr>
    <tr>
      <td>409</td>
      <td>when you have accidentally specified an already existing URL</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="get">Get Dashboards</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/dashboards/<em>&lt;dashboardId&gt;</em></code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

#### Success

```json
{
  "id": <dashboardId>,
  "title": "My Dashboard",
  "bodyMarkdown": "# A test dashboard",
  "urlPath": "2u4PP3TJqbu",
  "createdAt": 1439346145003,
  "updatedAt": 1439346145003
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
      <td>when the input is in a format that can’t be accepted</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the dashboard corresponding to the designated ID can't be found</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="update">Update Dashboards</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/dashboards/<em>&lt;dashboardId&gt;</em></code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

Same as [Create Dashboards](#create).

### Response

#### Success

The updated dashboard is returned. Same format as [Create Dashboards](#create).

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
      <td>when the input is in a format that can’t be accepted</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">permitted IP address range</a></td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the dashboard corresponding to the designated ID can't be found</td>
    </tr>
    <tr>
      <td>409</td>
      <td>when you have accidentally specified an already existing URL *1 </td>
    </tr>
  </tbody>
</table>

 *1 When you want to update a dashboard's URL, there is a possibility of redundancy with the URL of another existing dashboard. If that happens a 409 error code will be returned.

----------------------------------------------

<h2 id="delete">Delete Dashboards</h2>

This will delete the dashboard corresponding to the designated ID.

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/dashboards/<em>&lt;dashboardId&gt;</em></code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Response

#### Success

The dashboard before deletion is returned, same as [Create Dashboards](#create).

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
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">permitted IP address range</a></td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the dashboard corresponding to the designated ID can't be found</td>
    </tr>
  </tbody>
</table>
