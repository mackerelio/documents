---
Title: Notification channels
Date: 2017-10-18T18:39:49+09:00
URL: https://mackerel.io/api-docs/entry/channels
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/8599973812309156743
---

<ul class="internal-nav">
  <li><a href="#get">Get Notification Channels</a></li>
  <li><a href="#create">Register Notification Channels</a></li>
  <li><a href="#delete">Delete Notification Channels</a></li>
</ul>

<h2 id="get">Get Notification channel list</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/channels</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

#### Success

```json
{
  "channels": [<channel>, <channel>, ...]
}
```

`<channel>` is an object with the following keys

| KEY             | TYPE     | DESCRIPTION                      |
| ------------    | -------- | -------------------------------- |
| `id`            | *string* | the notification channel ID               |
| `name`          | *string* | the name of the notification channel             |
| `type`          | *string* | the type of notification channel (`"email"`, `"slack"`, `"line"`, `"chatwork"`, `"typetalk"`, `"twilio"`, `"pagerduty"`, `"opsgenie"`, `"yammer"`, `"microsoft-teams`, `"webhook"`, `"amazon-event-bridge"`) |

 In addition to that above, the following detailed information returns if the `type` is `"email"`, `"slack"`, or `"webhook"`,

##### When the `type` is `email`

| KEY             | TYPE            | DESCRIPTION                      |
| ------------    | -------------   | --------------------------------------------- |
| `emails`        | *array[string]* | email addresses specified to receive notifications          |
| `userIds`       | *array[string]* | user IDs specified to receive notifications             |
| `events`        | *array[string]*        | notification events (`"alert"`, `"alertGroup"`, `"hostStatus"`, `"hostRegister"`, `"hostRetire"`, or `"monitor"`) |

##### When the `type` is `slack`

| KEY                 | TYPE            | DESCRIPTION                        |
| ------------        | -------------   | ---------------------------------- |
| `url`               | *string*        | Incoming Webhook URL for Slack     |
| `mentions`          | *hash[string]*  | the key is the condition (`ok`, `warning`, or `critical`), the value is the text accompanying the alert notification |
| `enabledGraphImage` | *boolean*       | whether or not the corresponding graph is posted to Slack |
| `events`            | *array[string]*        | notification events (`"alert"`, `"alertGroup"`, `"hostStatus"`, `"hostRegister"`, `"hostRetire"`, or `"monitor"`) |

##### When the `type` is `webhook`

| KEY             | TYPE            | DESCRIPTION                      |
| ------------    | -------------   | --------------------------------------------- |
| `url`           | *string*        | URL that will receive HTTP request            |
| `enabledGraphImage` | *boolean*       | whether or not the URL of the corresponding graph is included in the notification content |
| `events`        | *array[string]*        | notification events (`"alert"`, `"alertGroup"`, `"hostStatus"`, `"hostRegister"`, `"hostRetire"`, or `"monitor"`) |

----------------------------------------------

<h2 id="create">Register Notification Channels</h2>

※Currently supported for email, Slack, and Webhook.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/channels</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

<ul class="internal-nav create">
  <li><a href="#create-email-channel">email notifications</a></li>
  <li><a href="#create-slack-channel">Slack</a></li>
  <li><a href="#create-webhook-channel">Webhook</a></li>
</ul>

<h3 id="create-email-channel">email notifications</h3>

#### Input(email notifications)
| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `type`   | *string* | fixed character string `"email"` |
| `name`   | *string* | the name of the channel |
| `emails`        | *array[string]* | email addresses specified to receive notifications           |
| `userIds`       | *array[string]* | user IDs specified to receive notifications                |
| `events`        | *array[string]*        | notification events (`"alert"`, `"alertGroup"`, `"hostStatus"`, `"hostRegister"`, `"hostRetire"`, or `"monitor"`) |

#### Response(email notifications)

##### Success

```json
{
  "id": <channelId>,
  "type": "email",
  "name": "My Channel",
  "emails": ["myaddress@example.com"],
  "userIds": ["userId"],
  "events": ["alert"]
}
```

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
      <td>when the address in <code>emails</code> is invalid</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when a user that does not belong to the organization is included in <code>userIds</code></td>
    </tr>
    <tr>
      <td>400</td>
      <td>when <code>events</code> contains an event other than those listed above</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

<h3 id="create-slack-channel">Slack</h3>

#### Input(Slack)

The object contains the following keys

| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `type`   | *string* | fixed character string `"slack"` |
| `name`   | *string* | the name of the channel |
| `url`               | *string*        | Incoming Webhook URL for Slack      |
| `mentions`          | *hash[string]*  | the key is the condition (`ok`, `warning`, or `critical`), the value is the text accompanying the alert notification  |
| `enabledGraphImage` | *boolean*       | whether or not the corresponding graph is posted to Slack |
| `events`            | *array[string]*        | notification events (`"alert"`, `"alertGroup"`, `"hostStatus"`, `"hostRegister"`, `"hostRetire"`, or `"monitor"`) |

#### Response(Slack)

##### Success

```json
{
  "id": <channelId>,
  "type": "slack",
  "name": "My Channel",
  "url": "https://hooks.slack.com/services/TAAAA/BBBB/XXXXX",
  "mentions": {
    "ok": "ok message",
    "warning": "warning message"
  },
  "enabledGraphImage": true,
  "events": ["alert"]
}
```

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
      <td>when the <code>url</code> is invalid</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when <code>events</code> contains an event other than those listed above</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

<h3 id="create-webhook-channel">Webhook</h3>

#### Input(Webhook)
| KEY     | TYPE   | DESCRIPTION |
| -------- | ------ | ----------- |
| `type`   | *string* | fixed character string `"webhook"` |
| `name`   | *string* | the name of the channel |
| `url`           | *string*        | URL that will receive HTTP request            |
| `enabledGraphImage` | *boolean*       | whether or not the URL of the corresponding graph is included in the notification content |
| `events`        | *array[string]*        | notification events (`"alert"`, `"alertGroup"`, `"hostStatus"`, `"hostRegister"`, `"hostRetire"`, or `"monitor"`) |

#### Response(Webhook)

##### Success

```json
{
  "id": <channelId>,
  "type": "webhook",
  "name": "My Channel",
  "url": "http://example.com/webhook",
  "enabledGraphImage": true,
  "events": ["alert"]
}
```

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
      <td>when the <code>url</code> is invalid</td>
    </tr>
    <tr>
      <td>400</td>
      <td>when <code>events</code> contains an event other than those listed above</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

----------------------------------------------

<h2 id="delete">Delete Notification Channels</h2>

Delete the channel

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/channels/<em>&lt;channelId&gt;</em></code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Response

The status of the channel before deletion is returned

#### Success

The format is the same as the channel list API `<channel>`

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
      <td>when the supported channel can not be found in <code><em>&lt;channelId&gt;</em></code></td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

----------------------------------------------
