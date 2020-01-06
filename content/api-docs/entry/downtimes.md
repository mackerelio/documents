---
Title: Downtime
Date: 2019-07-08T13:00:00+09:00
URL: https://mackerel.io/api-docs/entry/downtimes
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/
---

<ul class="internal-nav">
  <li><a href="#create">Registering Downtimes</a></li>
  <li><a href="#list">Listing Downtimes</a></li>
  <li><a href="#update">Updating Downtimes</a></li>
  <li><a href="#delete">Deleting Downtimes</a></li>
</ul>


<h2 id="create">Registering Downtimes</h2>

This section covers registering downtime in Mackerel.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/downtimes</code>
</p>

### Required permissions for API key
<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input
Objects that hold the following keys:

| KEY | TYPE | DESCRIPTION |
| --- | ---- | ----------- |
| `name` | *string* | the name of the downtime |
| `memo` | *string* | [optional] notes for the downtime |
| `start` | *number* | the starting time(in epoch seconds) |
| `duration` | *number* | the duration of downtime (in minutes) |
| `recurrence` | *recurrence* | [optional] configuration for repeating occurences |
| `serviceScopes` | *array[string]* | [optional] scope of target services. service name array[*](#service-name-and-role-fullname) |
| `serviceExcludeScopes` | *array[string]* | [optional] scope of exluded services. service name array[*](#service-name-and-role-fullname) |
| `roleScopes` | *array[string]* | [optional] scope of target roles. role fullname array[*](#service-name-and-role-fullname) |
| `roleExcludeScopes` | *array[string]* | [optional] scope of exluded roles. role fullname array[*](#service-name-and-role-fullname) |
| `monitorScopes` | *array[string]* | [optional] scope of target monitor configurations. `<monitor id>` array |
| `monitorExcludeScopes` | *array[string]* | [optional] scope of excluded monitor configurations. `<monitor id>` array |

`<recurrence>` is an object that hold the following keys:

| KEY | TYPE | DESCRIPTION |
| --- | ---- | ----------- |
| `type` | *string* | recurrence options (`hourly`, `daily`, `weekly`, `monthly`, `yearly`) |
| `interval` | *number* | recurrence interval |
| `weekdays` | *array[string]* | [optional] configuration for the day of the week (`Sunday`, `Monday`, `Tuesday`, `Wednesday`, `Thursday`, `Friday`, `Saturday`). Only available when the `type` is set to `weekly` |
| `until` | *number* | [optional] the time at which recurrence ends (in epoch seconds) |

### Response
#### Success
The input is returned along with an ID.

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
      <td>when the input is invalid</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the name or memo is too long</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the downtime duration is invalid</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the recurrence configuration is invalid</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the target service/role/monitor setting of the scope is redundant or does not exist</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the service/role/monitor setting of the scope does not exist</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

<h4 id="service-name-and-role-fullname">* Service name and Role service name</h4>

Service name as well as role service name are character strings in the format `<service name>` and `<service name>:<role name>`.

<table class="eg-table">
  <tbody>
  <tr>
    <th>e.g.</th>
    <td>If the service name for Hatena-Bookmark is <code>Hatena-Bookmark </code> then the db-master role in the service Hatena-Bookmark would be <code>Hatena-Bookmark:db-master</code> </td>
    </tr>
  </tbody>
</table>

Usable characters are /^[A-Za-z0-9][A-Za-z0-9_-]+$/.

----------------------------------------------

<h2 id="list">Listing Downtimes</h2>
<p class="type-get">
  <code>GET</code>
  <code>/api/v0/downtimes</code>
</p>

### Required permissions for API key
<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response
| KEY         | TYPE            | DESCRIPTION             |
| ----------- | --------------- | ----------------------- |
| `downtimes` | *array[object]* | a list of the downtime |


----------------------------------------------

<h2 id="update">Updating Downtimes</h2>
<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/downtimes/<em>&lt;downtimeId&gt;</em></code>
</p>

### Required permissions for API key
<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input
Same as [Registering Downtimes](#create).

### Response
#### Success
The updated downtime is returned. Same format as [Registering Downtimes](#create).

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
      <td>when the input is invalid</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the name or memo is too long</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the downtime duration is invalid</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the recurrence configuration is invalid</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the target service/role/monitor setting of the scope is redundant or does not exist</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the service/role/monitor setting of the scope does not exist</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the downtime corresponding to the designated ID can't be found</td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="delete">Deleting Downtimes</h2>

This will delete the downtime corresponding to the designated ID.

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/downtimes/<em>&lt;downtimeId&gt;</em></code>
</p>

### Required permissions for API key
<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Response
#### Success
The downtime before deletion is returned, same as [Registering Downtimes](#create).

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
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the downtime corresponding to the designated ID can't be found</td>
    </tr>
  </tbody>
</table>
