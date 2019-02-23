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
- Manager
    - Users who can perform all configurations associated with the organization (excluding plan and payment method)
    - The Manager can create, view, edit, delete an API key, add and delete members, and configure AWS/Azure Integrations
- Collaborator
    - Users who can perform various settings, changes, deletions, etc. within the organization
    - The Collaborator can create/update/delete services and roles, monitor rules, notification channels, and various configurations
    - The Collaborator can not view or create API keys
- Viewer
    - Users who can only view items within the organization

User permission settings can be configured at the time of user invitation or from the members' tab of the organization (Manager authority or higher is required).

The operations that are possible / impossible for each permission are as follows.

|                                                            | Owner  | Manager  | Collaborator  | Viewer  |
|-----------------------------------------------------------:|----------:|--------:|--------------:|--------:|
| Change/cancel plan                                          |         ○|       ×|             ×|       ×|
| Change payment info                                           |         ○|       ×|             ×|       ×|
| Delete organization                                   |         ○|       ×|             ×|       ×|
| Create/view/edit/delete API key                              |         ○|       ○|             ×|       ×|
| View payment info                                           |         ○|       ○|             ×|       ×|
| Edit AWS/Azure Integration settings                            |         ○|       ○|             ×|       ×|
| Add/edit/delete members                                   |         ○|       ○|             ×|       ×|
| View/edit organization settings                          |         ○|       ○|             ×|       ×|
| Configure monitor notifications’ on/off settings                                  |         ○|       ○|             ×|       ×|
| Host configurations (status changes or retirement)                      |         ○|       ○|             ○|       ×|
| Create/edit/delete services and roles                           |         ○|       ○|             ○|       ×|
| Create/edit/delete monitor rules and notification channels                 |         ○|       ○|             ○|       ×|
| Close alerts                                           |         ○|       ○|             ○|       ×|
| Create/edit/delete custom dashboards                     |         ○|       ○|             ○|       ×|
| Create/edit/delete graph settings and graph annotations           |         ○|       ○|             ○|       ×|
| Create/edit/delete graphboards                                    |         ○|       ○|             ○|       ×|
| Create/delete graph sharing URLs                         |         ○|       ○|             ○|       ×|
| View Services/Roles/Hosts                             |         ○|       ○|             ○|       ○|
| View monitor rules/notification channels/alerts                 |         ○|       ○|             ○|       ○|
| View graphs/custom dashboards/graph annotations |         ○|       ○|             ○|       ○|
| View graphboards                                |         ○|       ○|             ○|       ○|
| View graph sharing URLs                         |         ○|       ○|             ○|       ○|
| View AWS/Azure Integration settings/member list              |         ○|       ○|             ○|       ○|
