---
Title: Setting up SAML
Date: 2024-08-02T14:00:00+09:00
URL: https://mackerel.io/docs/entry/advanced/saml-settings
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802340630902355647
CustomPath: advanced/saml-settings
---

**A contract for a higher-tier Mackerel plan which includes SAML authentication is required to use this feature.**

This document explains the settings for linking Mackerel with an Identity Provider.
For information on the SAML authentication process, please see [Authentication with SAML](https://mackerel.io/docs/entry/advanced/saml-authentication).
For instructions on other extended uses of SAML, please see the pages below.

- [Managing attribute mappings](https://mackerel.io/docs/entry/advanced/saml-attribute-mappings)
- [Managing outside collaborators](https://mackerel.io/docs/entry/advanced/saml-outside-collaborators)

## SAML and Organization Groups

When using SAML, you can sign in to Mackerel with credentials from your Identity Provider.
In Mackerel, you can create an organization group made up of multiple organizations, and link them with an Identity Provider.

## Setting up SAML

Set up is required for both your Identity Provider and Mackerel.

### Settings to configure for the Identity Provider

Please refer to the table below for setting up your Identity Provider.

| Setting             | Value                                                    |
| ------------------- | -------------------------------------------------------- |
| SP Entity ID        | `https://mackerel.io/saml/metadata.xml`                  |
| ACS URL             | `https://mackerel.io/saml/acs`                           |
| Request Binding     | `POST`                                                   |
| NameIDPolicy Format | `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` |

For information on settings for a specific Identity Provider, please refer to the Identity Provider's documentation.

- [Entra ID](https://learn.microsoft.com/ja-jp/entra/identity-platform/single-sign-on-saml-protocol)
- [Okta](https://help.okta.com/oie/ja-jp/content/topics/apps/apps_app_integration_wizard_saml.htm)
- [Keycloak](https://www.keycloak.org/docs/latest/server_admin/index.html#assembly-managing-clients_server_administration_guide)

## Basic setup for organization groups

The organization group Manager can access the Organization Group List screen from the User menu.  
After that, the Manger can select an organization group from the Organization Group List screen to check or change its settings.

![The User menu. From the top are “Account Settings”, “Organization Groups Settings”, and “Sign Out”.](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240925/20240925112722.png)

![Edit Organization Group screen. Detailed information about the organization group is displayed in a table. The organization group's name is at the top of the table. Following in order are the entry forms for Details, IdP Metadata XML, IdP Entity ID, Single Sign On URL, IdP X509 Certificate, Require SAML Authentication, Attribute Mappings, and Join all organizations as viewer by default, with an Update button and Cancel button at the end.](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20250702/20250702182410.png)

| Setting                                     | Description                                                                                                                                                                                                         |
| ------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Details                                     | Freely configurable text that describes the organization group.                                                                                                                                                     |
| IdP Metadata XML                            | XML provided by the Identity Provider. Uploading this will allow you to set up IdP Entity ID, Single Sign On URL, and IdP X509 Certificate.                                                                         |
| IdP Entity ID                               | (Required) Identifier provided by the Identity Provider. You cannot specify an IdP Entity ID which has already been registered.                                                                                     |
| Single Sign On URL                          | Identity Provider endpoint used in the SP initiated flow.                                                                                                                                                           |
| IdP X509 Certificate                        | (Required) Certificate provided by the Identity Provider.                                                                                                                                                           |
| Require SAML authentication                 | When enabled, only users and [outside collaborators](https://mackerel.io/docs/entry/advanced/saml-outside-collaborators) authenticated with SAML can view the organization.                                         |
| Attribute mapping                           | When enabled, permissions are assigned based on [attribute mappings](https://mackerel.io/docs/entry/advanced/saml-attribute-mappings). You can only enable this setting when forced SAML authentication is enabled. |
| Join all organizations as viewer by default | When enabled, SAML-authenticated users join as viewers of all organizations affiliated with the organization group. You can only enable this setting when attribute mapping is disabled.                            |

<h2 id="managing-orgs">Managing affiliated organizations</h2>

The organization group Manager can access the Manage Affiliated Organizations screen from Affiliated Organizations on the sidebar.

![Affiliated Organizations List screen. Displays a list of organizations in table format. There is an Add Affiliated Organization button at the top right of the screen. There is a Delete button on the right end of the row for each organization.](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20250702/20250702182415.png)

On the Manage Affiliated Organizations screen, the organizations affiliated with the organization group are displayed.

You can add organizations with the Add Affiliated Organization button. You can add organizations that meet the following requirements.

- A currently active higher-tier Mackerel plan is applied.
- Not affiliated with another organization group.
- Owner is not an [outside collaborator](https://mackerel.io/docs/entry/advanced/saml-outside-collaborators) of the organization group to which they are to be added.

To remove an affiliated organization, click the Delete button to the right of the relevant organization's name.
The members and permissions of the organization will remain the same.

## Removing users from an organization group

To remove a SAML user from an organization group, you must first deactivate the user on the identity provider side, and then manually remove the user from the Mackerel organization group (if SCIM integration is not used).

The organization group Manager can access the member management screen from SAML Members on the sidebar.

![Member management screen. Displays a list of users related to the organization group in table format. For users who are not the owner or managers, there is a Delete button at the right end of the row.](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20250702/20250702182404.png)

The member management screen displays a list of the organization group’s owner, managers, and users who have logged in with SAML.

To remove a user from the organization group, click the Delete button to the right of the user’s name. However, the following users cannot be removed:

- The owner of the organization group
- Managers of the organization group
- Users using SCIM integration

If “Require SAML authentication” and “Enable attribute mapping” are both enabled in the organization group settings, users removed from the organization group will also be automatically removed from all affiliated organizations. In other cases, to remove SAML users from an organization, you must manually remove the users from each organizations after removing them from the organization group.