---
Title: Managing attribute mappings
Date: 2024-08-02T14:00:00+09:00
URL: https://mackerel.io/docs/entry/advanced/saml-attribute-mappings
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802340630902353938
CustomPath: advanced/saml-attribute-mappings
---

**A contract for a higher-tier Mackerel plan which includes SAML authentication is required to use this feature.**

For information on basic setup for SAML authentication, please see [Setting up SAML](https://mackerel.io/docs/entry/advanced/saml-settings).

Using attribute mappings allows you to map Identity Provider attributes with Mackerel [user permissions](https://mackerel.io/docs/entry/spec/authority).

- Permissions granted by attribute mapping cannot be changed on the Organization Member List screen.
- The Owner of an organization is not affected by attribute mapping.

When you enable attribute mappings in [Organization Group Settings](https://mackerel.io/docs/entry/advanced/saml-settings), affiliated organizations' members and permissions will be immediately updated according to the attribute mappings.

## Caution

- If you turn on Attribute Mapping without creating attribute mapping settings on Mackerel and properly setting up the attributes with the Identity Provider, members of affiliated organizations may be considered to have no assigned permissions, and only the organization Owner will be able to access it.
- We recommend creating the attribute mapping settings in advance, and turning on Attribute Mapping after setting up the attributes with the Identity Provider.

## Managing attribute mappings

The organization group Manager can check/change settings from Attribute Mappings on the sidebar.

![Attribute Mappings List screen. A list of the attribute mapping names, attribute names, attribute values, and notes are displayed in table format. There is an Add New button at the top right of the screen, and an Edit button on the right end of each row.](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240822/20240822183125_original.png)

## Adding attribute mapping settings

You can add settings with the Add New button on the Attribute Mappings List screen.

![Add Attribute Mapping screen. At the top are the mapping names, attribute names, attribute values, and a form for entering notes. There is a table for selecting affiliated organizations and permissions at the bottom. On each row of the table, the organization's management name is displayed, and there is a horizontal row of radio buttons to the right for selecting Manager, Collaborator, Viewer, and Unassigned. There is an Add button and a Cancel button below the table.](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240822/20240822183315_original.png)

| Setting                 | Description                                                                                                                                                                                       |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Mapping Name            | Enter the name to use for managing attribute mapping settings.                                                                                                                                    |
| Attribute Name          | The SAML attribute name. Enter the attribute name set by your Identity Provider.                                                                                                                        |
| Value                   | The SAML attribute value. Enter the attribute value set by your Identity Provider.                                                                                                                      |
| Memo                    | Freely configurable text describing the attribute mapping setting.                                                                                                                                |
| Affiliated Organization | Select the user permissions in the organization for the user corresponding to the attribute name and attribute value. If you selected Unassigned, they will not be a member of that organization. |

*If attributes which can be assigned multiple permissions have been set up by the Identity Provider, the strongest permissions will be applied.

## Editing/deleting attribute mapping settings

Attribute mapping settings can be edited by pressing the Edit button to the right of each attribute mapping setting on the Attribute Mapping List screen.
The settings when editing are the same as when [adding](#adding-attribute-mapping-settings) them.

Attribute mapping settings can be deleted by pressing the Edit button to open the editing screen, then pressing the delete button at the bottom of the screen.

![Edit Attribute Mapping screen. At the top are the mapping names, attribute names, attribute values, and a form for entering notes. There is a table for selecting affiliated organizations and permissions at the bottom. On each row of the table, the organization's management name is displayed, and there is a horizontal row of radio buttons to the right for selecting Manager, Collaborator, Viewer, and Unassigned. There are Add, Cancel, and Delete buttons below the table.](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240822/20240822183440_original.png)
