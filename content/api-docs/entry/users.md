---
Title: Users
Date: 2016-03-24T18:54:36+09:00
URL: https://mackerel.io/api-docs/entry/users
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/10328537792368377732
---

<ul class="internal-nav">
  <li><a href="#list">Listing Users</a></li>
  <li><a href="#delete">Deleting Users</a></li>
</ul>


<h2 id="list">Listing Users</h2>

This will get a list of the users belonging to an organization.

<p class="type-get">
  <code>GET</code>
  <code>/api/v0/users</code>
</p>

### Required permissions for API key

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
| `joinedAt`                | *number*        | Date and time the user joined the organization (in epoch seconds)                                                |

----------------------------------------------

<h2 id="delete">Deleting Users</h2>

This will delete users belonging to an organization.

<p class="type-delete">
  <code>DELETE</code>
  <code>/api/v0/users/<em>&lt;userId&gt;</em></code>
</p>

### Required permissions for API key

<ul class="api-key">
  <li class="label-read">Read</li>
  <li class="label-write">Write</li>
</ul>

### Response

#### Success

The information of the user that was deleted from the organization will be returned.
The format will be the same as when getting the [list of users](#list).

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
      <td>when the API doesn’t have the required permissions / when the organization’s creator is specified as <code><em>&lt;userId&gt;</em></code> / when accessing from outside the <a href="https://mackerel.io/docs/entry/faq/organization/ip-restriction" target="_blank">permitted IP address range</a></td>
    </tr>
    <tr>
      <td>404</td>
      <td>when the <code><em>&lt;userId&gt;</em></code> that was designated doesn't belong to the organization</td>
    </tr>
  </tbody>
</table>
