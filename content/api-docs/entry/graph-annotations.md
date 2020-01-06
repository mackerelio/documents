---
Title: Graph Annotations
Date: 2017-01-25T18:03:05+09:00
URL: https://mackerel.io/api-docs/entry/graph-annotations
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/10328749687209882920
---

<ul class="internal-nav">
  <li><a href="#create">Creating graph annotations</a></li>
  <li><a href="#get">Obtaining graph annotations</a></li>
  <li><a href="#update">Updating graph annotations</a></li>
  <li><a href="#delete">Deleting graph annotations</a></li>
</ul>


<h2 id="create">Creating graph annotations</h2>
<p class="type-post">
  <code>POST</code>
  <code>/api/v0/graph-annotations</code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

| KEY             | TYPE            | DESCRIPTION                        |
| ------------    | --------------- | ---------------------------------- |
| `title`         | *string*        | Annotation title                   |
| `description`   | *string*        | [optional] Annotation details      |
| `from`          | *number*        | Starting time (epoch seconds)      |
| `to`            | *number*        | Ending time (epoch seconds)        |
| `service`       | *string*        | Service name                       |
| `roles`         | *array[string]* | [optional] Role name array (omit this field to register an annotation related to the service) |

#### Input example
```json
{
  "title": "deploy application",
  "description": "link: https://example.com/",
  "from": 1484000000,
  "to": 1484000030,
  "service": "ExampleService",
  "roles": [ "ExampleRole1", "ExampleRole2" ]
}
```

### Response
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
      <td>When the input is in a format that can’t be received</td>
    </tr>
    <tr>
      <td>400</td>
      <td>When the title exceeds 250 characters or the description exceeds 1024 characters</td>
    </tr>
    <tr>
      <td>400</td>
      <td>When the starting time exceeds the ending time</td>
    </tr>
    <tr>
      <td>404</td>
      <td>When the service and role do not exist</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

<h2 id="get">Obtaining graph annotations</h2>

Specify the service and interval and obtain the graph annotations list. Annotations with intervals intersecting with the specified interval will all be returned.

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/graph-annotations</code>
</p>

### Required permissions for API key

<ul class="api-key border-none">
  <li class="label-read">Read</li>
</ul>

### Input (Query parameters)

| PARAM     | TYPE     | DESCRIPTION                                  |
| --------- | -------- | -------------------------------------------- |
| `service` | *string* | Service name                                   |
| `from`    | *number* | the start of the specified interval (epoch seconds) |
| `to`      | *number* | the end of the specified interval (epoch seconds) |

### Response

```json
{
  "graphAnnotations": [
    {
      "id": "2UdH1QcZQaw",
      "title": "Deploy application",
      "description": "Deploy description",
      "from": 1484020459,
      "to": 1484020759,
      "service": "ExampleService"
    },
    {
      "id": "2UdH1QuiGgj",
      "title": "Release application",
      "description": "Release description",
      "from": 1484021239,
      "to": 1484021239,
      "service": "ExampleService",
      "roles": [ "ExampleRole1", "ExampleRole2" ]
    }
  ]
}
```

### Error

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
      <td>When the necessary query parameters are not specified</td>
    </tr>
    <tr>
      <td>404</td>
      <td>When the specified service does not exist</td>
    </tr>
  </tbody>
</table>

<h2 id="update">Updating graph annotations</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/graph-annotations/<em>&lt;annotationId&gt</em></code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

The same as [Creating graph annotations](#create).

### Response

The same as [Creating graph annotations](#create), the input is returned with an ID.

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
      <td>When the input is in a format that can’t be received</td>
    </tr>
    <tr>
      <td>400</td>
      <td>When the title exceeds 250 characters or the description exceeds 1024 characters</td>
    </tr>
    <tr>
      <td>400</td>
      <td>When the starting time exceeds the ending time</td>
    </tr>
    <tr>
      <td>404</td>
      <td>When the service or role do not exist</td>
    </tr>
    <tr>
      <td>404</td>
      <td>When the graph annotation does not exist</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

<h2 id="delete">Deleting graph annotations</h2>

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/graph-annotations/<em>&lt;annotationId&gt</em></code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Response

#### Success

The graph annotation before deletion will be returned.

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
      <td>When the graph annotation does not exist</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>
