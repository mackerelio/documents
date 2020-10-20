---
Title: Users
Date: 2016-03-24T18:54:36+09:00
URL: https://mackerel.io/api-docs/entry/users
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/10328537792368377732
---


<ul class="internal-nav">
  <li><a href="#list">List Users that are Organization Members</a></li>
  <li><a href="#delete">Delete Users that are Organization Members</a></li>
</ul>


<h2 id="list">List Users that are Organization Members</h2>

This will retrieve a list of the users that are members of the organization.

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/users</code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
</ul>

### Response

```json
{
  "users": [<user>, <user>, ...]
}
```

`<user>` is an object that holds the following keys

| KEY                       | TYPE            | DESCRIPTION                                                                                         |
| --------------            | --------        | -----------                                                                                         |
| `id`                      | *string*        | user ID                                                                                             |
| `screenName`              | *string*        | user account's display name                                                                         |
| `email`                   | *string*        | user's email address                                                                                |
| `authority`               | *string*        | user's authority (`owner`, `manager`, `collaborator`, `viewer`)                                     |
| `isInRegistrationProcess` | *boolean*       | Whether or not the user is in the process of registration.                                                                          |
| `isMFAEnabled`            | *boolean*       | Whether or not the user has enabled 2-step verification.                                                                 |
| `authenticationMethods`   | *array[string]* | Authentication methods used (`password`, `github`, `idcf`, `google`, `nifty`, `yammer`, `kddi`) |
| `joinedAt`                | *number*        | Date and time the user became a member of the organization (in epoch seconds)                                                |

----------------------------------------------

<h2 id="delete">Delete Users that are Organization Members</h2>

This will delete a user that is a member of the organization.

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/users/<em>&lt;userId&gt;</em></code>
</p>

### Required permissions for the API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Response

#### Success

User information is returned for the user that was deleted as a member of the organization.
The format is the same as [List Users that are Organization Members](#list).

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
      <td>when the JSON format is incorrect</td>
    </tr>
    <tr>
      <td>403</td>
      <td>when the API doesn’t have the required permissions / when the organization’s creator is specified as <code><em>&lt;userId&gt;</em></code> / when accessing from outside the <a href="https://support.mackerel.io/hc/en-us/articles/360039701952" target="_blank">permitted IP address range</a></td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the specified <code><em>&lt;userId&gt;</em></code> is not a member of the organization</td>
    </tr>
  </tbody>
</table>
