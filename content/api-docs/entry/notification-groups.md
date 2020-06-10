---
Title: Notification groups
Date: 2017-10-18T18:40:47+09:00
URL: https://mackerel.io/api-docs/entry/notification-groups
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/8599973812309161198
---

<ul class="internal-nav">
  <li><a href="#create">Registering Notification Groups</a></li>
  <li><a href="#get">Getting Notification Groups</a></li>
  <li><a href="#update">Updating Notification Groups</a></li>
  <li><a href="#delete">Deleting Notification Groups</a></li>
</ul>


<h2 id="create">Registering Notification Groups</h2>

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/notification-groups</code>
</p>

### Required permissions for the API key

<ul class="api-key border-none">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

| KEY                         | TYPE             | DESCRIPTION                              |
| --------------------------- | ---------------- | ---------------------------------------- |
| `name`                      | *string*         | the name of the notification group                       |
| `notificationLevel`         | *string*         | the level of notification (`"all"` or `"critical"`) |
| `childNotificationGroupIds` | *array[string]*  | an array of notification group IDs           |
| `childChannelIds`           | *array[string]*  | an array of notification channel IDs         |
| `monitors`                  | *array[monitor]* | [optional] an array of notification target monitor rules    |
| `services`                  | *array[service]* | [optional] an array of notification target services      |

`monitor` is an object with the following keys

| KEY                         | TYPE             | DESCRIPTION                                    |
| --------------------------- | ---------------- | ---------------------------------------------- |
| `id`                        | *string*         | the monitor rule ID                                 |
| `skipDefault`               | *boolean*        | If true, send notifications to this notification group only |

`service` is an object with the following keys

| KEY                         | TYPE             | DESCRIPTION                              |
| --------------------------- | ---------------- | ---------------------------------------- |
| `name`                      | *string*         | the name of the service                          |

### Example input

```json
{
  "name": "Example notification group",
  "notificationLevel": "all",
  "childNotificationGroupIds": [],
  "childChannelIds": [
    "2vh7AZ21abc"
  ],
  "monitors": [
    {
      "id": "2qtozU21abc",
      "skipDefault": false
    }
  ],
  "services": [
    {
      "name": "Example-Service-1"
    },
    {
      "name": "Example-Service-2"
    }
  ]
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
      <td>when the input is in a format that can’t be received</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when creating fails</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

<h2 id="get">Get the notification group list</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/notification-groups</code>
</p>

### Required permissions for the API key

<ul class="api-key border-none">
  <li class="label-read">Read</li>
</ul>

### Response

```json
{
  "notificationGroups": [<notification-group>, <notification-group>, ...]
}
```

`<notification-group>` is the same as the registration API response

<h2 id="update">Updating Notification Groups</h2>

<p class="type-put">
  <code>PUT</code>
  <code>/api/v0/notification-groups/<em>&lt;notificationGroupId&gt</em></code>
</p>

### Required permissions for the API key

<ul class="api-key border-none">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

The same object as the registration API entry

### Response

The same object as registration API response

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
      <td>400</td>
      <td>when an attempt to change the name of the default notification group is made</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when updating fails</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the specified notification group does not exist</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

<h2 id="delete">Deleting Notification Groups</h2>
The organization’s default notification group can not be deleted

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/notification-groups/<em>&lt;notificationGroupId&gt</em></code>
</p>

### Required permissions for the API key

<ul class="api-key border-none">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Response

The notification group before deletion is returned

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
      <td>when the specified notification group is the default group</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when deleting fails</td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the specified notification group does not exist</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>
