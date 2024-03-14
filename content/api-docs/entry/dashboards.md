---
Title: Dashboards
Date: 2016-03-24T18:50:47+09:00
URL: https://mackerel.io/api-docs/entry/dashboards
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/10328537792368377375
---

<ul class="internal-nav">
  <li><a href="#create">Create Dashboards</a></li>
  <li><a href="#get">Get Dashboards</a></li>
  <li><a href="#update">Update Dashboards</a></li>
  <li><a href="#delete">Delete Dashboards</a></li>
  <li><a href="#list">List Dashboards</a></li>
</ul>

<h2 id="create">Create Dashboards</h2>

This section covers creating dashboards.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/dashboards</code>
</p>

### Required permissions for the API key
<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input
Objects that hold the following keys:

| KEY       | TYPE            | DESCRIPTION                                       |
| --------- | --------------- | ------------------------------------------------- |
| `title`   | *string*        | the name of the dashboard                         |
| `memo`    | *string*        | notes regarding the dashboard                     |
| `urlPath` | *string*        | the dashboard's URL path \*1                      |
| `widgets` | *array[object]* | a list of [objects that represent widgets](#widget) |

\*1 URL path should be as follows:

`^([A-Za-z0-9_][-A-Za-z0-9_]*)(/[A-Za-z0-9_][-A-Za-z0-9_]*)*$`

### Response
#### Success
The dashboard that was created is returned.

| KEY         | TYPE            | DESCRIPTION                                       |
| ----------- | --------------- | ------------------------------------------------- |
| `id`        | *string*        | the dashboard's ID \*1                            |
| `title`     | *string*        | the name of the dashboard                         |
| `memo`      | *string*        | notes regarding the dashboard                     |
| `urlPath`   | *string*        | the dashboard's URL path                          |
| `widgets`   | *array[object]* | a list of [objects that represent widgets](#widget) |
| `createdAt` | *number*        | the time at which created (in epoch seconds)                            |
| `updatedAt` | *number*        | the time at which last updated (in epoch seconds)                        |

\*1 `id` is the ID that was assigned to this dashboard. With future requests, this ID will identify the dashboard.

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
      <td>403</td>
      <td>when you have reached your dashboard limit / when the API key doesn't have the required permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">permitted IP address range</a></td>
    </tr>
    <tr>
      <td>409</td>
      <td>when you have specified an already existing URL path</td>
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
Same as [Create Dashboards](#create).

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
      <td>when the input is invalid</td>
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
      <td>when you have specified an already existing URL path</td>
    </tr>
  </tbody>
</table>

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

----------------------------------------------

<h2 id="list">List Dashboards</h2>
<p class="type-get">
  <code>GET</code>
  <code>/api/v0/dashboards</code>
</p>

### Required permissions for the API key
<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response
| KEY          | TYPE            | DESCRIPTION            |
| ------------ | --------------- | ---------------------- |
| `dashboards` | *array[object]* | a list of the dashboards |

Each dashboard is same format as [Create Dashboards](#create).

----------------------------------------------

<h2 id="widget">Widgets</h2>
Objects representing widgets have the following formats for the differing types.

### Graph widget
| KEY      | TYPE     | DESCRIPTION                                                   |
| -------- | -------- | ------------------------------------------------------------- |
| `type`   | *string* | fixed character string `"graph"`                                          |
| `title`  | *string* | the title of the widget                                        |
| `graph`  | *object* | [object representing a graph](#graph)                            |
| `range`  | *object* | [optional] [object representing the graph display range](#graph-range) |
| `valueRange` | *object* | [optional] [object representing the value range of vertical axis](#graph-value-range) |
| `referenceLines` | *array[object]* | [optional] array of [objects representing the reference line](#reference-line). If you want to remove the reference line setting, specify an empty array.<br /> If reference line is not set when get dashboards, an empty array is returned. Cannot specify more than one element in an array.|
| `layout` | *object* | [object representing the layout](#layout)                       |

### Value widget
| KEY            | TYPE     | DESCRIPTION                                               |
| -------------- | -------- | --------------------------------------------------------- |
| `type`         | *string* | fixed character string `"value"`                          |
| `title`        | *string* | the title of the widget                                   |
| `metric`       | *object* | [object representing a metric](#metric)                   |
| `fractionSize` | *number* | [optional] decimal places displayed on the widget  (0–16) |
| `suffix`       | *string* | [optional] the units to be displayed after the value      |
| `formatRules`  | *array[object]* | [optional] [array of objects representing the format rule](#format-rule). If you want to remove the format rule setting, specify an empty array.<br /> If format rule is not set when get dashboards, an empty array is returned. Cannot specify more than one element in an array.|
| `layout`       | *object* | [object representing the layout](#layout)                 |

### Markdown widget
| KEY        | TYPE     | DESCRIPTION                             |
| ---------- | -------- | --------------------------------------- |
| `type`     | *string* | fixed character string `"markdown"`                 |
| `title`    | *string* | the title of the widget                  |
| `markdown` | *string* | a character string in Markdown format                    |
| `layout`   | *object* | [object representing the layout](#layout) |

### Alert Status widget
| KEY        | TYPE     | DESCRIPTION                             |
| ---------- | -------- | --------------------------------------- |
| `type`     | *string* | fixed character string `"alertStatus"`                 |
| `title`    | *string* | the title of the widget                  |
| `roleFullname` | *string*  | the service name and role name linked by `:`<br /> However, if the relevant role or service has been deleted when the dashboard is retrieved, `roleFullname` will be set as `null`. |
| `layout`   | *object* | [object representing the layout](#layout) |

<h3 id="graph">Graphs</h3>
#### Host graph
| KEY      | TYPE     | DESCRIPTION                  |
| -------- | -------- | ---------------------------- |
| `type`   | *string* | fixed character string `"host"`          |
| `hostId` | *string* | the host's ID                   |
| `name`   | *string* | the name of the graph (`"loadavg"` etc.) |

#### Role graph
| KEY            | TYPE      | DESCRIPTION                                           |
| -------------- | --------- | ----------------------------------------------------- |
| `type`         | *string*  | fixed character string `"role"`                       |
| `roleFullname` | *string*  | the service name and role name linked with `:`        |
| `name`         | *string*  | the name of the graph (`"loadavg5"` etc.)             |
| `isStacked`    | *boolean* | [optional] whether the graph is stacked or line graph |

#### Service graph
| KEY           | TYPE     | DESCRIPTION            |
| ------------- | -------- | ---------------------- |
| `type`        | *string* | fixed character string `"service"` |
| `serviceName` | *string* | the name of the service             |
| `name`        | *string* | the name of the graph               |

#### Expression graph
| KEY          | TYPE     | DESCRIPTION               |
| ------------ | -------- | ------------------------- |
| `type`       | *string* | fixed character string `"expression"` |
| `expression` | *string* | expression representing a graph           |

#### Query graph
| KEY          | TYPE     | DESCRIPTION               |
| ------------ | -------- | ------------------------- |
| `type`       | *string* | fixed character string `"query"` |
| `query` | *string* | PromQL-style queries           |
| `legend` | *string* | query legend. by specifiying the label key  in `{{ }}`, it can be expanded to the value of the label. e.g. if you have a label `{ http.method: "GET" }` and you specify `method: {{http.method}}` in the `legend`, it will expand to `method: GET`.|


In addition to these, if the role/service graph definition connected to obtaining the dashboard is deleted, the following result may be returned.

| KEY    | TYPE     | DESCRIPTION            |
| ------ | -------- | ---------------------- |
| `type` | *string* | fixed character string `"unknown"` |

<h3 id="graph-range">Graph display range</h3>
If left unspecified, the display range becomes variable and can be changed from the controller displayed at the top of the dashboard.

#### Relative
| KEY      | TYPE     | DESCRIPTION             |
| -------- | -------- | ----------------------- |
| `type`   | *string* | fixed character string `"relative"` |
| `period` | *number* | length of the period (in seconds)        |
| `offset` | *number* | difference from the current time (in seconds)  |

A range from (current time + `offset` - `period`) to (current time + `offset`) is displayed.
By specifying a negative value for `offset`, you can display a graph of the past specified range.

#### Absolute
| KEY     | TYPE     | DESCRIPTION             |
| ------- | -------- | ----------------------- |
| `type`  | *string* | fixed character string `"absolute"` |
| `start` | *number* | start time (in epoch seconds)  |
| `end`   | *number* | end time (in epoch seconds)  |

The range is displayed from `start` to` end`.

<h3 id="graph-value-range">Fix the value range of the vertical axis</h3>

If not specified, the vertical axis is automatically set according to the metric values.

| KEY    | TYPE     | DESCRIPTION             |
| ------ | -------- | ----------------------- |
| `min`  | *number* | [optional] minimum value on vertical axis |
| `max`  | *number* | [optional] maximum value on vertical axis |

<h3 id="reference-line">Reference line</h3>

If an reference line is specified for the graph, a horizontal line is drawn based on the specified value.

| KEY      | TYPE     | DESCRIPTION             |
| -------- | -------- | ----------------------- |
| `label`  | *string* | label of the reference line |
| `value`  | *number* | value of the reference line |

#### About the label

The label must comply with the following conditions.

- Minimum length: 1 character (excluding spaces)
- Maximum length: 32 characters

#### About the value

The value must comply with the following conditions.

- Must be greater than or equal to 0

<h3 id="format-rule">Format Rule</h3>

If a format rule is specified for the widget, the value will be changed to an emphasized style when the set conditional expression is satisfied.
For example, if the `threshold` is set to `10` and the `operator` is set to `>`, the value will be emphasized when it is greater than 10.

| KEY      | TYPE     | DESCRIPTION             |
| -------- | -------- | ----------------------- |
| `name`  | *string* | [optional] Name of the format rule |
| `threshold`  | *number* | Base value of the conditional expression |
| `operator` | *string* | Specify the conditions under which the emphasized style will be applied. `">"` or `"<"`. |

#### About the name

The name must comply with the following conditions.

- Maximum length: 32 characters

<h3 id="metric">Metrics</h3>
#### Host metrics

| KEY      | TYPE     | DESCRIPTION                       |
| -------- | -------- | --------------------------------- |
| `type`   | *string* | fixed character string `"host"`               |
| `hostId` | *string* | the host's ID                      |
| `name`   | *string* | the name of the metric (`"loadavg5"` etc.) |

#### Service metrics

| KEY           | TYPE     | DESCRIPTION            |
| ------------- | -------- | ---------------------- |
| `type`        | *string* | fixed character string `"service"` |
| `serviceName` | *string* | the name of the service            |
| `name`        | *string* | the name of the metric           |

#### Expressions

| KEY          | TYPE     | DESCRIPTION               |
| ------------ | -------- | ------------------------- |
| `type`       | *string* | fixed character string `"expression"` |
| `expression` | *string* | an expression that represents a metric       |

#### Query

| KEY          | TYPE     | DESCRIPTION               |
| ------------ | -------- | ------------------------- |
| `type`       | *string* | fixed character string `"query"` |
| `query` | *string* | PromQL-style queries           |
| `legend` | *string* | query legend. by specifiying the label key  in `{{ }}`, it can be expanded to the value of the label. e.g. if you have a label `{ http.method: "GET" }` and you specify `method: {{http.method}}` in the `legend`, it will expand to `method: GET`.|

In order to properly display values of a metric specified by an expression, the result of the expression must represent a single series.

In addition to these, if the service connected to obtaining the dashboard is deleted, the following result may be returned.

| KEY    | TYPE     | DESCRIPTION            |
| ------ | -------- | ---------------------- |
| `type` | *string* | fixed character string `"unknown"` |

<h3 id="layout">Layout</h3>
| KEY      | TYPE     | DESCRIPTION         |
| -------- | -------- | ------------------- |
| `x`      | *number* | x coordinate of widget |
| `y`      | *number* | y coordinate of widget |
| `width`  | *number* | width of widget   |
| `height` | *number* | height of widget  |

Coordinates are specified using the upper left corner of the widget display area as the origin (`x` = 0,` y` = 0), the right direction as the x axis, and the downward direction as the positive y axis. Also, each numerical value must comply with the following conditions. If these conditions are not met, the widget may not display in the position expected.

- each value must be a positive integer or 0
- widgets can not protrude from the widget display area (24 width)
- widgets can not overlap
- widgets must be at least the minimum size and at most the maximum size, as described below for each type.

| Widget   | Minimum value `width` | Minimum value `height` | Maximum value `width` | Maximum value `height` |
| -------- | --------------------- | ---------------------- | --------------------- | ---------------------- |
| Graph    | 6                     | 6                      | 24                    | 32                     |
| Value    | 4                     | 4                      | 24                    | 32                     |
| Markdown | 4                     | 2                      | 24                    | 80                     |
| Alert Status | 4             | 3                 | 24               | 32                |
