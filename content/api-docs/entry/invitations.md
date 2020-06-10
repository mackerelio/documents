---
Title: Invitations
Date: 2017-05-17T16:34:37+09:00
URL: https://mackerel.io/api-docs/entry/invitations
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/10328749687247575586
---

<ul class="internal-nav">
  <li><a href="#list">Listing Invitations</a></li>
  <li><a href="#create">Creating Invitations</a></li>
  <li><a href="#revoke">Cancelling Invitations</a></li>
</ul>


<h2 id="list">Listing Invitations</h2>

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/invitations</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

#### Success

```json
{
  "invitations": [<invitation>, <invitation>, ...]
}
```

<i>`<invitation>`</i> is an object that contains the following keys.

| KEY         | TYPE     | DESCRIPTION                                                 |
| --------    | ------   | -----------                                                 |
| `email`     | *string* | the email address that sent the invitation                                |
| `authority` | *string* | the authority when joining from invitation (`manager`,`collaborator`,`viewer`) |
| `expiresAt` | *number* | the expiration date of the invitation (in epoch seconds)                                    |


<h2 id="create">Creating Invitations</h2>

Specify an email address and permission and invite a user to the organization.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/invitations</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

| KEY             | TYPE            | DESCRIPTION                                                    |
| ------------    | --------------- | -------------------------------------------------------------- |
| `email`         | *string*          | the email address to send the invitation                                       |
| `authority`     | *string*          | the default permission when joining from an invitation  (`manager`,`collaborator`,`viewer`) |

#### Example input
```json
{
  "email": "example@example.com",
  "authority": "viewer"
}
```

### Response

#### Success

The expiresAt field (in epoch seconds) is given and returned with the input. Invitations can not be used after the moment of expiresAt.

```json
{
  "email": "example@example.com",
  "authority": "viewer",
  "expiresAt": 1492393387
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
      <td>when JSON format is incorrect, when an email address that has already been invited or an organization member’s email address is specified</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">permitted IP address range</a></td>
    </tr>
  </tbody>
</table>

<h2 id="revoke">Cancelling Invitations</h2>

Specify the email address and cancel an invitation to the organization.

<p class="type-post">
  <code>POST</code>
  <code>/api/v0/invitations/revoke</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Input

| KEY             | TYPE            | DESCRIPTION                                                    |
| ------------    | --------------- | -------------------------------------------------------------- |
| `email`         | *string*        | email address to cancel invitation                                   |

#### Example input

```json
{
  "email": "example@example.com"
}
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
      <td>400</td>
      <td>when JSON format is incorrect</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API key doesn't have the required permissions / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">permitted IP address range</a></td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the specified email has not be sent an invitation</td>
    </tr>
  </tbody>
</table>

