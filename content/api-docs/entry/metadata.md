---
Title: Metadata
Date: 2016-10-21T15:23:13+09:00
URL: https://mackerel.io/api-docs/entry/metadata
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/10328749687190542028
---

<ul class="internal-nav">
  <li><a href="#hostget">Getting Host Metadata</a></li>
  <li><a href="#hostput">Registering/Updating Host Metadata</a></li>
  <li><a href="#hostdelete">Deleting Host Metadata</a></li>
  <li><a href="#hostlist">Listing Host Metadata</a></li>
  <li><a href="#serviceget">Getting Service Metadata</a></li>
  <li><a href="#serviceput">Registering/Updating Service Metadata</a></li>
  <li><a href="#servicedelete">Deleting Service Metadata</a></li>
  <li><a href="#servicelist">Listing Service Metadata</a></li>
  <li><a href="#roleget">Getting Role Metadata</a></li>
  <li><a href="#roleput">Registering/Updating Role Metadata</a></li>
  <li><a href="#roledelete">Deleting Role Metadata</a></li>
  <li><a href="#rolelist">Listing Role Metadata</a></li>
</ul>

<h2 id="metadata">Metadata</h2>

With this API, you can register arbitrary JSON data to hosts, services, and roles. You can use it to register a variety of information such as the management ID and/or other data to help you manage efficiently.

The namespace is used as an identifier for the metadata. We recommend using a consistent identifier such as `project` or `environment`. The endpoint of retrieving, creating, updating, and deleting metadata are consistent having the following URL.

<code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metadata/<em>&lt;namespace&gt;</em></code>

You can obtain and register JSON (object, array, string, number, boolean, null) to this endpoint.

```json
{
  "type": 12345,
  "region": "jp",
  "env": "staging",
  "instance_type": "c4.xlarge"
}
```

Numeric characters,` _`, and `-` can be used with letters of the alphabet (`[-a-zA-Z0-9_]+`). However, a namespace that begins with `mackerel` will be used by the Mackerel system. 

<h2 id="hostget">Getting Host Metadata</h2>

Obtain registered metadata.

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
</p>

### Required permissions for API key

<ul class="api-key border-none">
  <li class="label-read">Read</li>
</ul>

#### Response

JSON of the registered data is returned. The date and time of metadata’s last update is configured in the response header’s `Last-Modified`.

##### Error

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
      <td>when more than a week has passed since the host retired</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the host does not exist</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the specified metadata does not exist for the host</td>
    </tr>
  </tbody>
</table>

<h2 id="hostput">Registering/Updating Host Metadata</h2>

Create and update arbitrary JSON as metadata of the host.

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

Any JSON can be specified. However, the size of the data is limited to 100KB.

### Response

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
      <td>when the host has already been retired</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when the namespace has an invalid value</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when metadata limit (50 per 1 host) has been exceeded and you try to register</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the host does not exist</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
    <tr>
      <td>413</td>
      <td>when the metadata exceeds 100KB</td>
    </tr>
  </tbody>
</table>

<h2 id="hostdelete">Deleting Host Metadata</h2>

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

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
      <td>when the host has already been retired</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the host does not exist</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the specified metadata does not exist for the host</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

<h2 id="hostlist">Listing Host Metadata</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metadata</code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

#### Success

```json
{
  "metadata": [<metadata>, <metadata>, ...]
}
```

`<metadata>`: an object that holds the following keys.

<table>
  <thead>
    <tr>
      <th>KEY</th> 
      <th>TYPE</th>
      <th>DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>namespace</code></td>
      <td><em>string</em></td>
      <td>The namespace of each metadata</td>
    </tr>
  </tbody>
</table>

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
      <td>when more than a week has passed since the host retired</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the host does not exist</td>
    </tr>
  </tbody>
</table>

<h2 id="serviceget">Getting Service Metadata</h2>

Obtain registered metadata.

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
</p>

### Required permissions for API key

<ul class="api-key border-none">
  <li class="label-read">Read</li>
</ul>

#### Response

JSON of the registered data is returned. The date and time of metadata’s last update is configured in the response header’s `Last-Modified`.

##### Error

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
      <td>when the service does not exist</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when metadata specified for the service does not exist</td>
    </tr>
  </tbody>
</table>

<h2 id="serviceput">Registering/Updating Service Metadata</h2>

Create and update arbitrary JSON as metadata of the service.

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

Any JSON can be specified. However, the size of the data is limited to 100KB.

### Response

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
      <td>when the namespace is invalid</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when trying to register while exceeding the limit of metadata per service (50 per service)</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the service does not exist</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
    <tr>
      <td>413</td>
      <td>when metadata exceeds 100 KB</td>
    </tr>
  </tbody>
</table>

<h2 id="servicedelete">Deleting Service Metadata</h2>

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

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
      <td>when the service does not exist</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when metadata specified for the service does not exist</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

<h2 id="servicelist">Listing Service Metadata</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metadata</code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

#### Success

```json
{
  "metadata": [<metadata>, <metadata>, ...]
}
```

`<metadata>`: an object that contains the following keys

<table>
  <thead>
    <tr>
      <th>KEY</th> 
      <th>TYPE</th>
      <th>DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>namespace</code></td>
      <td><em>string</em></td>
      <td>namespace</td>
    </tr>
  </tbody>
</table>

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
      <td>when the service does not exist</td>
    </tr>
  </tbody>
</table>

<h2 id="roleget">Getting Role Metadata</h2>

Obtain registered metadata.

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
</p>

### Required permissions for API key

<ul class="api-key border-none">
  <li class="label-read">Read</li>
</ul>

#### Response

JSON of the registered data is returned. The date and time of metadata’s last update is configured in the response header’s `Last-Modified`.

##### Error

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
      <td>when the service/role does not exist</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the metadata specified for the role does not exist</td>
    </tr>
  </tbody>
</table>

<h2 id="roleput">Registering/Updating Role Metadata</h2>

Create and update arbitrary JSON as metadata of the role.

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

Any JSON can be specified. However, the size of the data is limited to 100KB.

### Response

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
      <td>when the namespace is invalid</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when trying to register and exceeding the limit of metadata per role (10 per role)</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the service/role does not exist</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
    <tr>
      <td>413</td>
      <td>when metadata exceeds 100 KB</td>
    </tr>
  </tbody>
</table>

<h2 id="roledelete">Deleting Role Metadata</h2>

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

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
      <td>when the service/role does not exist</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the metadata specified for the role does not exist</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

<h2 id="rolelist">Listing Role Metadata</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em>/metadata</code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

#### Success

```json
{
  "metadata": [<metadata>, <metadata>, ...]
}
```

`<metadata>`: an object that contains the following keys

<table>
  <thead>
    <tr>
      <th>KEY</th> 
      <th>TYPE</th>
      <th>DESCRIPTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>namespace</code></td>
      <td><em>string</em></td>
      <td>namespace</td>
    </tr>
  </tbody>
</table>

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
      <td>when the service/role does not exist</td>
    </tr>
  </tbody>
</table>
