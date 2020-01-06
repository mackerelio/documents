---
Title: Alert Group Settings
Date: 2019-09-05T16:54:26+09:00
URL: https://mackerel.io/api-docs/entry/alert-group-settings
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/26006613422198590
CustomPath: alert-group-settings
---

<ul class="internal-nav">
  <li><a href="#list">Listing alert group settings</a></li>
  <li><a href="#create">Creating alert group settings</a></li>
  <li><a href="#get">Getting alert group settings</a></li>
  <li><a href="#update">Updating alert group settings</a></li>
  <li><a href="#delete">Deleting alert group settings</a></li>
</ul>

<h2 id="list">Listing alert group settings</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/alert-group-settings</code>
</p>

### Required permissions for the API key

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
| `serviceScopes`        | *array[string]* | [optional] scope for the target service. an array of service names                     |
| `roleScopes`           | *array[string]* | [optional] scope for the target role. an array of role fullnames [*1](#role-fullname) |
| `monitorScopes`        | *array[string]* | [optional] scope for the target monitor. an array of monitor IDs                      |
| `notificationInterval` | *number*        | [optional] the time interval (in minutes) for  resending notifications                    |

<h4 id="role-fullname">*1 role fullname</h4>

A role's fullname is an array in the format of `<service name>:<role name>`.

<table class="eg-table">
  <tbody>
  <tr>
    <th>e.g.</th>
    <td>if you have <code>Hatena-Bookmark</code> as the service and <code>db-master</code> as the role, its role fullname is <code>Hatena-Bookmark:db-master</code></td>
    </tr>
  </tbody>
</table>

available strings for the role fullname must match the following regular expression: `/^[A-Za-z0-9][A-Za-z0-9_-]+$/`

<h2 id="create">Creating alert group settings</h2>

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/alert-group-settings</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

| KEY                    | TYPE            | DESCRIPTION                                                            |
| --------               | ------          | -----------                                                            |
| `name`                 | *string*        | the name of the alert group setting                                             |
| `memo`                 | *string*        | [optional] notes related to the alert group setting                        |
| `serviceScopes`        | *array[string]* | [optional] scope for the target service. an array of service names                      |
| `roleScopes`           | *array[string]* | [optional] scope for the target role. an array of the role's fullnames [*1](#role-fullname) |
| `monitorScopes`        | *array[string]* | [optional] scope for the target monitor. an array of monitor IDs                      |
| `notificationInterval` | *number*        | [optional] the time interval (in minutes) for  resending notifications                    |

### Response

#### Success

The created alert group setting is returned. The response format is the same as that which can be obtained with [Listing alert group settings](#list).

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
      <td>when the input is in a format that can't be received</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>	
    </tr>
  </tbody>
</table>

<h2 id="get">Getting alert group settings</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/alert-group-settings/<em>&lt;alertGroupSettingId&gt;</em></code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

#### Success

The alert group setting is returned. The response format is the same as that which can be obtained with [Listing alert group settings](#list).

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
      <td>when the input is in a format that can't be received</td>
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

<h2 id="update">Updating alert group settings</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/alert-group-settings/<em>&lt;alertGroupSettingId&gt;</em></code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

The same format as [Creating alert group settings](#create).

### Response

#### Success

The updated alert group setting is returned. The response format is the same as that which can be obtained with [Listing alert group settings](#list).

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
      <td>when the input is in a format that can't be received</td>
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

<h2 id="delete">Deleting alert group settings</h2>

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/alert-group-settings/<em>&lt;alertGroupSettingId&gt;</em></code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Response

#### Success

The deleted alert group setting is returned. The response format is the same as that which can be obtained with [Listing alert group settings](#list).

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
      <td>when the input is in a format that can't be received</td>
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
