---
Title: User permissions
Date: 2017-05-12T18:37:19+09:00
URL: https://mackerel.io/docs/entry/spec/authority
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10328749687245582827
---

In Mackerel, there are four permissions that vary in strength. In order of strength, they are: Owner, Manager, Collaborator, and Viewer. 

- Owner
    - User with the highest authority in the organization
    - In addition to the authority of Manager, the Owner can also make changes to the plan and payment method.
    - Each organization has one Owner (limited to one position)
    - When an owner leaves the management of the organization due to retirement, transfer, or other reasons, please ensure that another member is designated as the owner in advance
- Manager
    - Users who can perform all configurations associated with the organization (excluding plan and payment method)
    - The Manager can create, view, edit, delete an API key, invite and delete members, and configure AWS/Azure/Google Cloud Integrations
- Collaborator
    - Users who can perform various settings, changes, deletions, etc. within the organization
    - The Collaborator can create/edit/delete services and roles, monitor rules, notification channels, and various configurations
    - The Collaborator can not view or create API keys
- Viewer
    - Users who can only view items within the organization

User permission settings can be configured at the time of user invite or from the [members](https://mackerel.io/my?tab=members) tab of the organization (Manager authority or higher is required).

The operations that are possible / impossible for each permission are as follows.

|                                                              | Owner  | Manager | Collaborator | Viewer |
|-------------------------------------------------------------:|-------:|--------:|-------------:|-------:|
| Change/cancel plan                                           |       ○|        ×|             ×|       ×|
| Change payment info                                          |       ○|        ×|             ×|       ×|
| Delete organization                                          |       ○|        ×|             ×|       ×|
| Create/view/edit/delete API key                              |       ○|        ○|             ×|       ×|
| View payment info                                            |       ○|        ○|             ×|       ×|
| Create/edit/delete AWS/Azure/Google Cloud Integration settings |       ○|        ○|             ×|       ×|
| invite/edit/delete members                                   |       ○|        ○|             ×|       ×|
| Edit organization settings                                   |       ○|        ○|             ×|       ×|
| Disable Notification at Organizations                        |       ○|        ○|             ×|       ×|
| Host configurations (status changes or retirement)           |       ○|        ○|             ○|       ×|
| Create/edit/delete services and roles                        |       ○|        ○|             ○|       ×|
| Create/edit/delete monitor rules and downtime                |       ○|        ○|             ○|       ×|
| Create/edit/delete notification channels                     |       ○|        ○|             ○|       ×|
| Make a note to alert, Close alerts                           |       ○|        ○|             ○|       ×|
| Write notes to APM                                           |       ○|        ○|             ○|       ×|
| Comment/react on issues and change assignees                |       ○|        ○|             ○|       ×|
| Create/edit/delete custom dashboards                         |       ○|        ○|             ○|       ×|
| Edit graph settings, Delete graph                            |       ○|        ○|             ○|       ×|
| Create/edit/delete graph annotations                         |       ○|        ○|             ○|       ×|
| Create/edit/delete graphboards                               |       ○|        ○|             ○|       ×|
| Post the graph to the channel, create/delete graph sharing URLs |       ○|        ○|             ○|       ×|
| View Services/Roles/Hosts                                    |       ○|        ○|             ○|       ○|
| View monitor rules/notification channels/alerts              |       ○|        ○|             ○|       ○|
| View graphs/custom dashboards/graph annotations              |       ○|        ○|             ○|       ○|
| View graphboards                                             |       ○|        ○|             ○|       ○|
| View graph sharing URLs                                      |       ○|        ○|             ○|       ○|
| View AWS/Azure/Google Cloud Integration settings, member list, organization settings |       ○|        ○|             ○|       ○|
