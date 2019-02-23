---
Title: Hosts
Date: 2016-03-24T18:14:13+09:00
URL: https://mackerel.io/api-docs/entry/hosts
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/10328537792368373510
---

<ul class="internal-nav">
  <li><a href="#create">Registering host information</a></li>
  <li><a href="#get">Getting host information</a></li>
  <li><a href="#update-information">Updating host information</a></li>
  <li><a href="#update-status">Updating host status</a></li>
  <li><a href="#update-roles">Updating host roles</a></li>
  <li><a href="#retire">Retiring hosts</a></li>
  <li><a href="#list">List of hosts</a></li>
  <li><a href="#metric-names">List of metric names</a></li>
</ul>


<h2 id="create">Registering host information</h2>

Registering an agent-running host to Mackerel.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/hosts</code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

By designating a host’s service and role in the `rollFullnames` input to the API, the host will be assigned to that role. If service and role names that don’t yet exist are entered, they will be created.

Objects that hold the following keys:

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `name`   | *string* | host name |
| `meta`   | *object* |host information [*1](#meta) |
| `interfaces` | *array[hash[string]]* | [optional] network interface information [*2](#interfaces) |
| `roleFullnames`  | *array[string]* | [optional] array of full names of the roles to which this host belongs [*3](#roleFullnames) |
| `checks`         | *array[hash[string]]* | [optional] array of check monitorings that monitor this host [*4](#checks) |
| `displayName`    | *string* | [optional] this host’s management name |
| `customIdentifier`    | *string*        | [optional] user-specific identifier for this host |

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
    <td>when the API doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
  </tr>
</tbody>
</table>

<h4 id="meta" class="annotation">*1 meta</h4>

`meta` contains various information. 

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

<h4 id="interfaces">*2 interfaces</h4>

One element of `interfaces` is an object that holds the following keys.

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `name` | *string* | interface name, e.g. "eth0" |
| `ipAddress` | *string* | interface IP address |
| `macAddress` | *string* | interface MAC address |
| `ipv4Addresses` | *array[string]* | interface IPv4 addresses |
| `ipv6Addresses` | *array[string]* | interface IPv6 addresses |

<h4 id="roleFullnames">*3 roleFullnames</h4>
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

<h4 id="checks">*4 checks</h4>
One element of `checks` is an object that holds the following keys.

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `name` | *string* | name of the monitor (`[a-zA-Z0-9_-]+`). |
| `memo` | *string* | [optional] a memo about the monitor. |

----------------------------------------------

<h2 id="get">Getting host information</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em></code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

```json
{
  "host": <host>
}
```

<i>`<host>`</i> is an object that holds the contents of [Registering host information](#create) with the addition of the below key. `roleFullnames` does not exist.

  | KEY     | TYPE   | DESCRIPTION |
  | -------- | ------ | ----------- |
  | `createdAt` | number | host registration timestamp (Unix time) |
  | `id` | string | |
  | `status` | string | the status of the host. (e.g. `"working"`, `"standby"`) |
  | `memo` | string | the memo regarding this host; can be edited in the web UI. |
  | `roles` | hash[array[string]] | the host’s role. the array of “key is the service name”, “variable is the name of the role in that service.” |
  | `interfaces` | *array[hash[string]]* | host network interface information |
  | `isRetired`  | *boolean* | whether or not the host is retiring  |
  | `displayName`| *string* | this host’s management name |
  | `meta` | *hash[string]* | the meta information of cpu, memory, etc. |

----------------------------------------------

<h2 id="update-information">Updating host information</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em></code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

Same as [Registering host information](#create).

A host remains to belong to the roles which you didn't specify by `roleFullnames`.
If you want to Un-assign roles from a host, please use [Updating host roles](#update-roles) API.

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
    <td>when the API doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
  </tr>
</tbody>
</table>

----------------------------------------------

<h2 id="update-status">Updating host status</h2>

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/status</code>
</p>

### Required permissions for API key

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
    <td>when the API doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
  </tr>
</tbody>
</table>

----------------------------------------------

<h2 id="update-roles">Updating host roles</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/role-fullnames</code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-write">Write</li>
</ul>

### Input

```json
{
  "roleFullnames": [ <string>, <string>, … ]
}
```

`roleFullnames` is the same as  `roleFullnames` object in [Registering host information](#create).

### Response

#### Success

```javascript
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
      <td>when the API doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="retire">Retiring hosts</h2>

This will retire a registered host.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/retire</code>
</p>

### Required permissions for API key

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
    <td>when the API doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
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

### Required permissions for API key

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
| `customIdentifier`    | *string*        | [optional] user-specific identifier for the host (registered at [Registering host information](#create) or [Updating host information](#update-information) API) |

### Response

```json
{
  "hosts": [ <host>, <host>, …]
}
```
<i>`<host>`</i> is the same type as the object that changes in [Getting host information](#get)

----------------------------------------------

<h2 id="metric-names">List of metric names</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metric-names</code>
</p>

### Required permissions for API key

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
