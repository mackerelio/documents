---
Title: Hosts
Date: 2016-03-24T18:14:13+09:00
URL: https://mackerel.io/api-docs/entry/hosts
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/10328537792368373510
---

<ul class="internal-nav">
  <li><a href="#create">Registering Host Information</a></li>
  <li><a href="#get">Getting Host Information</a></li>
  <li><a href="#update-information">Updating Host Information</a></li>
  <li><a href="#update-status">Updating Host Status</a></li>
  <li><a href="#update-roles">Updating Host Roles</a></li>
  <li><a href="#retire">Retiring Hosts</a></li>
  <li><a href="#list">Listing Hosts</a></li>
  <li><a href="#metric-names">Listing Metric Names</a></li>
</ul>


<h2 id="create">Registering Host Information</h2>

Registering an agent-running host to Mackerel.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/hosts</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

By designating the role to which a host belongs in the input, the host will be assigned to that role. If a service or role name that does not yet exist is entered, it will be created.

This object holds the following keys:

| KEY | TYPE | DESCRIPTION |
| --- | --- | --- |
| `name` | *string* | name of the host |
| `displayName` | *string* | [optional] host management name |
| `customIdentifier` | *string* | [optional] a user-specific identifier for the host[*1](#customIdentifier) |
| `meta` | *object* | host metadata[*2](#meta). Empty objects can also be registered. |
| `interfaces` | *array[object]* | [optional] host network interface information[*3](#interfaces) |
| `roleFullnames` | *array[string]* | [optional] an array of the full name of the role to which the host belongs[*4](#roleFullnames) |
| `checks` | *array[hash[string]]* | [optional] an array of the check monitoring item that monitors the host[*5](#checks) |

### Response

#### Success

```json
{
  "id": <hostId>
}
```

<i>`<hostId>`</i> : The host ID that was assigned to this host. With this request the host will be identified by this ID. 

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
    <td>when the input is in a format that can’t be accepted.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
  </tr>
</tbody>
</table>

<h4 id="customIdentifier" class="annotation">*1 customIdentifier</h4>

If `null` or `""` (an empty string) is specified  <span class="table-code">customIdentifier</span>, the following will occur depending on the HTTP request method.

**For `POST`**

It is registered as `null`.

**For `PUT`**

The registered value is overwritten with `null`.

<h4 id="meta" class="annotation">*2 meta</h4>

`meta` contains various information. For example, if the host is registered by mackerel-agent or Cloud Integration, the following information will be registered.

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `agent-name` | *string* | agent name e.g. "mackerel-agent/0.27.0 (Revision dfbccea)" |
| `agent-revision` | *string* | agent revision (Git commit SHA1) e.g. "2f531c6" | 
| `agent-version` | *string* | agent version, e.g. "0.4.2" |
| `block_device` | *hash[object]* | block device info. object that contains key name, variables like `size` etc. |
| `cpu` | *array[object]* | each CPU’s core info. each object holds `mhz` and `model_name` |
| `filesystem` | *hash[object]* | filesystem information. an object that holds “key is a name of filesystem”, `kb_available`, `kb_used`, etc. |
| `kernel` | *hash[string]* | kernel information; can be got with the command `uname` |
| `memory` | *hash[string]* | memory information |
| `cloud`  | *hash[object]* | Exists if the host is registered through Cloud Integration such as [AWS Integration](https://mackerel.io/docs/entry/integrations/aws). An object that contains keys like `provider` and `metadata`. |

<h4 id="interfaces">*3 interfaces</h4>

One element of `interfaces` is an object that holds the following keys.

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `name` | *string* | interface name, e.g. "eth0" |
| `macAddress` | *string* | [optional] interface MAC address |
| `ipv4Addresses` | *array[string]* | [optiona] interface IPv4 addresses |
| `ipv6Addresses` | *array[string]* | [optiona] interface IPv6 addresses |
| `ipAddress` | *string* | [optional] interface IPv4 address |
| `ipv6Address` | *string* | [optional] interface IPv6 address |

However, if `ipAddress` and `ipv4Addresses` or `ipv6Address` and `ipv6Addresses` are specified at the same time, then `ipv4Addresses` and `ipv6Addresses` will take precedence.

<h4 id="roleFullnames">*4 roleFullnames</h4>
The full names of roles are in this character string format: `<service name>:<role name>`

<table class="eg-table">
  <tbody>
    <tr>
      <th>e.g.</th>
      <td>A role named db-master in a service named Hatena-Bookmark would be <code>Hatena-Bookmark:db-master</code></td>
    </tr>
  </tbody>
</table>

Usable characters include `/^[A-Za-z0-9][A-Za-z0-9_-]+$/`

<h4 id="checks">*5 checks</h4>
One element of `checks` is an object that holds the following keys.

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `name` | *string* | name of the monitor (`[a-zA-Z0-9_-]+`). |
| `memo` | *string* | [optional] a memo about the monitor. |

----------------------------------------------

<h2 id="get">Getting Host Information</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em></code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

```json
{
  "host": <host>
}
```

<i>`<host>`</i> is an object that holds the following keys:

| KEY | TYPE | DESCRIPTION |
| --- | --- | --- |
| `createdAt` | *number* | the host's registration timestamp（in epoch seconds） |
| `id` | *string* | the host's ID |
| `name` | *string* | the name of the host |
| `displayName` | *string* | [optional] the host's management name |
| `customIdentifier` | *string* | [optional] a user-specific identifier for the host |
| `meta` | *object* | host metadata |
| `interfaces` | *array[object]* | host network interface information |
| `type` | *string* | classification of the host（`"agent"`, `"container-agent"`, `"cloud"` etc.） |
| `status` | *string* | status of the host（`"working"`, `"standby"` etc.） |
| `memo` | *string* | notes related to the host; can be edited from the management screen |
| `isRetired` | *boolean* | whether or not the host is retired |
| `retiredAt` | *number* | [optional] the host's retirement timestamp（in epoch seconds） |
| `roles` | *hash[array[string]]* | List of the roles to which the host belongs. The keys are service names, and the values are arrays of role names. |

----------------------------------------------

<h2 id="update-information">Updating Host Information</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em></code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

Same as [Registering Host Information](#create).

A host remains to belong to the roles which you didn't specify by `roleFullnames`.
If you want to Un-assign roles from a host, please use [Updating Host Roles](#update-roles) API.

### Response

#### Success

```json
{
  "id": <hostId>
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
    <td>404</td>
    <td>when the host that corresponds to the <code><em>&lt;hostId&gt;</em></code> can’t be located</td>
  </tr>
  <tr>
    <td>400</td>
    <td>when JSON format is incorrect</td>
  </tr>
  <tr>
    <td>403</td>
    <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
  </tr>
</tbody>
</table>

----------------------------------------------

<h2 id="update-status">Updating Host Status</h2>

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/status</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

```json
{
  "status": <hostStatus>
}
```

`<hostStatus>` is one of these: "standby", "working", "maintenance", or "poweroff"

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
    <td>404</td>
    <td>when the host that corresponds to the <code><em>&lt;hostId&gt;</em></code> can’t be located</td>
  </tr>
  <tr>
    <td>400</td>
    <td>when JSON format is incorrect</td>
  </tr>
  <tr>
    <td>403</td>
    <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
  </tr>
</tbody>
</table>

----------------------------------------------

<h2 id="update-roles">Updating Host Roles</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/role-fullnames</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-write">Write</li>
</ul>

### Input

```json
{
  "roleFullnames": [ <string>, <string>, … ]
}
```

`roleFullnames` is the same as  `roleFullnames` object in [Registering Host Information](#create).

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
      <td>404</td>
      <td>when the host that corresponds to the hostId can’t be located</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when JSON format is incorrect</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the format of <code>roleFullnames</code> is incorrect</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="retire">Retiring Hosts</h2>

This will retire a registered host.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/retire</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

This will POST empty JSON.


```json
{}
```

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
    <td>404</td>
    <td>when the host that corresponds to the <code><em>&lt;hostId&gt;</em></code> can’t be located</td>
  </tr>
  <tr>
    <td>400</td>
    <td>when JSON format is incorrect</td>
  </tr>
  <tr>
    <td>403</td>
    <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
  </tr>
</tbody>
</table>

----------------------------------------------

<h2 id="list">list of hosts</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/hosts</code>
</p>

`/api/v0/hosts.json` returns the same contents with the same query parameters.

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Input (Query parameter)

The following parameter will extract hosts. If nothing has yet been assigned, all hosts will be returned.


| PARAM     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `service` | *string* | [optional] service name |
| `role` | *string* | [optional] role names in the service, multiple assignments possible (result will be a unit of the hosts that belong to each role) if `service` has not been assigned it will be ignored.|
| `name` | *string* | [optional] host name |
| `status` | *string* | [optional] extract host status, multiple assignments possible, defaults are `working` and `standby`. |
| `customIdentifier`    | *string*        | [optional] user-specific identifier for the host (registered at [Registering Host Information](#create) or [Updating Host Information](#update-information) API) |

### Response

```json
{
  "hosts": [ <host>, <host>, …]
}
```
<i>`<host>`</i> is the same type as the object that changes in [Getting Host Information](#get)

----------------------------------------------

<h2 id="metric-names">Listing Metric Names</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metric-names</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

#### Success

```json
{
  "names": [<metricName>, <metricName>, ...]
}
```

| KEY      | TYPE            | DESCRIPTION                              |
| -------- | ------          | -----------                              |
| `names`  | *array[string]* | The name of the metrics being posted in the Host. |

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
      <td>404</td>
      <td>when the Host corresponding to <code><em>&lt;hostId&gt;</em></code> can't be found</td>
    </tr>
  </tbody>
</table>
