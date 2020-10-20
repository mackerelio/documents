---
Title: Services
Date: 2016-03-24T18:11:39+09:00
URL: https://mackerel.io/api-docs/entry/services
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/10328537792368373282
---

<ul class="internal-nav">
  <li><a href="#list">List Services</a></li>
  <li><a href="#create">Register Services</a></li>
  <li><a href="#delete">Delete Services</a></li>
  <li><a href="#rolelist">List Roles</a></li>
  <li><a href="#rolecreate">Register Roles</a></li>
  <li><a href="#roledelete">Delete Roles</a></li>
  <li><a href="#metric-names">List Metric Names</a></li>
</ul>

<h2 id="list">List Services</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/services</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

```json
{
  "services": [<service>, <service>, ...]
}
```

`<service>` is an object that holds the following keys.

| KEY      | TYPE            | DESCRIPTION                                       |
| -------- | ------          | -----------                                       |
| `name`   | *string*        | Name of the service.                                        |
| `memo`   | *string*        | Notes related to this service. Can be edited from the web UI. |
| `roles`  | *array[string]* | Array of role names belonging to the Service.                  |

Arrays will be shown in the order of Role names.

----------------------------------------------

<h2 id="create">Register Services</h2>

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/services</code>
</p>

### Required permissions for the API key

 <ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

| KEY      | TYPE            | DESCRIPTION               |
| -------- | ------          | -----------               |
| `name`   | *string*        | Name of the service.              |
| `memo`   | *string*        | Notes related to the service.|

#### About the service name

Usable characters for the service name include the alphabet (A - Za - z), numbers (0 - 9), hyphens (-), and underscores (_). In addition, the name must be within 2 to 63 characters and hyphens and underscores can not be used at the beginning.

Also, you can not create multiple services with the same name in one organization.

#### Example input

```json
{
  "name": "ExampleService",
  "memo": "This is an example."
}
```

### Response
The created service will be returned. The format will be the same as <i>`<service>`</i> objects of the <a href="#list">Service list</a> API.

#### Success

``` json
{
    "name": <serviceName>,
    "memo": <memo>,
    "roles": []
}
```

| KEY     | TYPE            | DESCRIPTION                          |
| ------- | --------------- | ------------------------------------ |
| `name`  | *string*        | Name of the service.                         |
| `memo`  | *string*        | Notes related to the service.              |
| `roles` | *array[string]* | Array of role names belonging to the service.  |

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
      <td>when the <code><em>&lt;serviceName&gt;</em></code> does not meet the above constraints</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the necessary permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">authorized IP address range</a></td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="delete">Delete Services</h2>

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em></code>
</p>

Roles, service metrics, monitoring configurations, and graph annotations associated with the service will also be deleted.

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Response
The state of the service just before being deleted will be returned.

#### Success

``` json
{
    "name": <serviceName>,
    "memo": <memo>,
    "roles": [<roleName>, <roleName>, ...]
}
```

| KEY     | TYPE            | DESCRIPTION                          |
| ------- | --------------- | ------------------------------------ |
| `name`  | *string*        | Name of the service.                         |
| `memo`  | *string*        | Notes related to the service.               |
| `roles` | *array[string]* | Array of role names belonging to the service. |

Arrays will be shown in the order of Role names.

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
      <td>when the Service corresponding to <code><em>&lt;serviceName&gt;</em></code> can't be found</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the necessary permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">authorized IP address range</a></td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="rolelist">List Roles</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

#### Success

```json
{
  "roles": [<role>, <role>, ...]
}
```

`<role>` is an object that holds the following keys.

| KEY      | TYPE     | DESCRIPTION                                     |
| -------- | ------   | -----------                                     |
| `name`   | *string* | Name of the role.                                        |
| `memo`   | *string* | Notes related to this role. Can be edited from the web UI. |

Arrays will be shown in the order of Role names.

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
      <td>when the Service corresponding to <code><em>&lt;serviceName&gt;</em></code> can't be found</td>
    </tr>
  </tbody>
</table>


----------------------------------------------

<h2 id="rolecreate">Register Roles</h2>
 
<p class="type-post">
  <code>POST</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles</code>
</p>

### Required permissions for the API key
 
<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

| KEY      | TYPE            | DESCRIPTION               |
| -------- | ------          | -----------               |
| `name`   | *string*        | Name of the role.              |
| `memo`   | *string*        | Notes related to this role.|

#### About the role name

Usable characters for the role name include the alphabet (A - Za - z), numbers (0 - 9), hyphens (-), and underscores (_). In addition, the name must be within 2 to 63 characters and hyphens and underscores can not be used at the beginning.

Also, you can not create multiple roles with the same name in one service.

#### Example input

```json
{
  "name": "ExampleRole",
  "memo": "This is an example."
}
```

### Response
The created role will be returned. The format will be the same as <i>`<role>`</i> objects of the <a href="#rolelist">Role list</a> API.

#### Success

``` json
{
    "name": <roleName>,
    "memo": <memo>
}
```

| KEY      | TYPE     | DESCRIPTION                                     |
| -------- | ------   | -----------                                     |
| `name`   | *string* | Name of the role.                                        |
| `memo`   | *string* | Notes related to this role. Can be edited from the web UI.         |


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
      <td>when the Service corresponding to <code><em>&lt;serviceName&gt;</em></code> can't be found</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the <code><em>&lt;roleName&gt;</em></code> does not meet the above constraints</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the necessary permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">authorized IP address range</a></td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="roledelete">Delete Roles</h2>

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em></code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>
 
### Response
The state of the role just before being deleted will be returned.

#### Success

``` json
{
    "name": <roleName>,
    "memo": <memo>
}
```

| KEY      | TYPE     | DESCRIPTION                                     |
| -------- | ------   | -----------                                     |
| `name`   | *string* | Name of the role.                                        |
| `memo`   | *string* | Notes related to this role.                               |

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
      <td>when the service/role corresponding to <code><em>&lt;serviceName&gt;</em></code>/<code><em>&lt;roleName&gt;</em></code> can't be found</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the necessary permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">authorized IP address range</a></td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="metric-names">List Metric Names</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metric-names</code>
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
| `names`  | *array[string]* | The name of the metrics being posted in the service. |

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
      <td>when the Service corresponding to <code><em>&lt;serviceName&gt;</em></code> can't be found</td>
    </tr>
  </tbody>
</table>
