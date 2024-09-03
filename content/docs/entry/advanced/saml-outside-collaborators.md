---
Title: Managing outside collaborators
Date: 2024-08-02T14:00:00+09:00
URL: https://mackerel.io/docs/entry/advanced/saml-outside-collaborators
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802340630902355628
CustomPath: advanced/saml-outside-collaborators
---

**A contract for a higher-tier Mackerel plan which includes SAML authentication is required to use this feature.**

For information on basic setup for SAML authentication, please see [Setting up SAML](https://mackerel.io/docs/entry/advanced/saml-settings).

Outside collaborators are users in an organization group with Require SAML authentication enabled who, as an exception, can be affiliated with several organizations without performing SAML authentication.
You can use this feature if there are users you want to allow to access organizations due to the circumstances of the work despite being outside the Identity Provider's control.

The organization group Manager can access the Manage Outside Collaborators screen from Outside Collaborators on the sidebar.

![Outside Collaborators List screen. Displays a list of outside collaborators in table format. There is an Invite button at the top right of the screen. On the right side of the row for a valid outside collaborator, there is a Change Permissions button and a Delete button. Invalid outside collaborators are grayed out, with the message “This user is invalid because they are signed in using SAML authentication” and a Delete button on the right.](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240829/20240829171239_original.png)

## Inviting outside collaborators

To invite an outside collaborator, first click the Invite button on the Manage Outside Collaborators screen.

![Add outside collaborators screen. At the top of the screen is an input box for email addresses. At the bottom of the screen, there is a list of organizations and permissions to which the collaborator belongs, and “Add” and “Cancel” buttons.](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240829/20240829163147_original.png)

Enter the email address of the user you wish to invite, select the [user permissions](https://mackerel.io/docs/entry/spec/authority) for all affiliated organizations in the organization group, and click the Add button.

If their email address falls under any of the following, they cannot be invited as an outside collaborator.

- An email address not registered as a Mackerel user.
- An email address that has already been added as an outside collaborator.
- An email address of a user already SAML-authenticated by the relevant organization group.
- An email address of the Owner of an affiliated organization of the relevant organization group.

If an outside collaborator has not yet signed up as a Mackerel user, please first ask them to complete user sign up for Mackerel ([Mackerel Sign-up](https://mackerel.io/signup)) (It is not necessary to create an organization).
If you wish to specify the email address of an organization Owner, please transfer the organization's Owner permissions to another user ([About organization Owner permissions](https://support.mackerel.io/hc/ja/articles/360039701852-%E3%82%AA%E3%83%BC%E3%82%AC%E3%83%8B%E3%82%BC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AE%E3%82%AA%E3%83%BC%E3%83%8A%E3%83%BC%E6%A8%A9%E9%99%90%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6))

Additionally, if you select Unassigned as the user permissions for the organization, the outside collaborator will not join that organization.

## Changing/deleting outside collaborator permissions

An outside collaborator's user permissions in each organization can be changed with the Change Permissions button to the right of the relevant outside collaborator on the Manage Outside Collaborators screen.
An outside collaborator's user permissions in the organization can also be changed from the relevant organization's Manage Members screen.

To delete an outside collaborator, click the Delete button to the right of the relevant outside collaborator on the Manage Outside Collaborators screen.
Users deleted from outside collaborators will leave all affiliated organization in the organization group.
Additionally, even if they are deleted from outside collaborators, they will not be deleted as a Mackerel user.

## SAML authentication of outside collaborators

If an outside collaborator performs SAML authentication, at that point in time, they will be stop being an outside collaborator, and be treated as an SAML-authenticated user.
The message “This user has been disabled because they signed in using SAML authentication.” will be displayed on the Manage Outside Collaborators screen, and it will only be possible to delete them.
