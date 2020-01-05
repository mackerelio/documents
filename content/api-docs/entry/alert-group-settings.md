---
Title: Alert Group Settings
Date: 2019-09-05T16:54:26+09:00
URL: https://mackerel.io/api-docs/entry/alert-group-settings
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/26006613422198590
CustomPath: alert-group-settings
---

<ul class="internal-nav">
  <li><a href="#list">List of alert group settings</a></li>
  <li><a href="#create">Create alert group settings</a></li>
  <li><a href="#get">Get alert group settings</a></li>
  <li><a href="#update">Update alert group settings</a></li>
  <li><a href="#delete">Delete alert group settings</a></li>
</ul>

<h2 id="list">List of alert group settings</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/alert-group-settings</code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

#### Success

```json
{
  "alert_group_settings": [<alertGroupSetting>, <alertGroupSetting>, ...]
}
```

<i>`<alertGroupSetting>`</i>: an object that holds the following keys.

| KEY                    | TYPE            | DESCRIPTION                                                            |
| --------               | ------          | -----------                                                            |
| `id`                   | *string*        | the alert group setting ID                                                 |
| `name`                 | *string*        | the name of alert group setting                                             |
| `memo`                 | *string*        | [optional] notes related to the alert group setting                        |
| `serviceScopes`        | *array[string]* | [optional] scope for the target service. an array of the service name                     |
| `roleScopes`           | *array[string]* | [optional] scope for the target role. an array of the role fullname [*1](#role-fullname) |
| `monitorScopes`        | *array[string]* | [optional] scope for the target monitor. an array of the monitor ID                      |
| `notificationInterval` | *number*        | [optional] the time interval (in minutes) for  re-sending notifications                    |

<h4 id="role-fullname">*1 role fullname</h4>

A role's full name is an array in the format of `<service name>:<role name>`.

<table class="eg-table">
  <tbody>
  <tr>
    <th>e.g.</th>
    <td>if a Hatena-Bookmark service db-master role <code>Hatena-Bookmark:db-master</code></td>
    </tr>
  </tbody>
</table>

available strings are `/^[A-Za-z0-9][A-Za-z0-9_-]+$/`

<h2 id="create">Create alert group settings</h2>

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/alert-group-settings</code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

| KEY                    | TYPE            | DESCRIPTION                                                            |
| --------               | ------          | -----------                                                            |
| `name`                 | *string*        | the name of alert group setting                                             |
| `memo`                 | *string*        | [optional] notes related to the alert group setting                        |
| `serviceScopes`        | *array[string]* | [optional] scope for the target service. an array of the service name                      |
| `roleScopes`           | *array[string]* | [optional] scope for the target role. an array of the role's fullname [*1](#role-fullname) |
| `monitorScopes`        | *array[string]* | [optional] scope for the target monitor. an array of the monitor ID                      |
| `notificationInterval` | *number*        | [optional] the time interval (in minutes) for  re-sending notifications                    |

### Response

#### Success

Information for the created alert group setting is returned. The format is the same as that which can be obtained with [List of alert group settings](#list).

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
      <td>when the input is in a format that can’t be received</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>	
    </tr>
  </tbody>
</table>

<h2 id="get">Get alert group settings</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/alert-group-settings/<em>&lt;alertGroupSettingId&gt;</em></code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

#### Success

Information for the alert group setting is returned. The format is the same as that which can be obtained with [List of alert group settings](#list).

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
      <td>when the input is in a format that can’t be received</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the specified alert group setting does not exist</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>	
    </tr>
  </tbody>
</table>

<h2 id="update">Update alert group settings</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/alert-group-settings/<em>&lt;alertGroupSettingId&gt;</em></code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

The same format as [Create alert group settings](#create).

### Response

#### Success

Information for the updated alert group setting is returned. The format is the same as that which can be obtained with [List of alert group settings](#list).

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
      <td>when the input is in a format that can’t be received</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the specified alert group setting does not exist</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>	
    </tr>
  </tbody>
</table>

<h2 id="delete">Delete alert group settings</h2>

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/alert-group-settings/<em>&lt;alertGroupSettingId&gt;</em></code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Response

#### Success

Information for the deleted alert group setting is returned. The format is the same as that which can be obtained with [List of alert group settings](#list).

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
      <td>when the input is in a format that can’t be received</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the specified alert group setting does not exist</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>	  
    </tr>
  </tbody>
</table>
