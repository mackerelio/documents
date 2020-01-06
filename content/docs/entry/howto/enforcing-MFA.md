---
Title: Mandatory 2-Step Authentication
Date: 2019-12-19T14:00:00+09:00
URL: https://mackerel.io/docs/entry/howto/enforcing-MFA
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613488505656
---

## What is mandatory 2-Step Authentication?

Enabling mandatory 2-Step Authentication in a Mackerel organization will require the user to set up [2-Step Authentication](https://mackerel.io/docs/entry/howto/MFA) in order to access the organization. This will strengthen the security of the organization and help protect customer resources.

## Enabling mandatory 2-Step Authentication

1. The organization owner must have 2-Step Authentication enabled. If not yet enabled, you can follow the steps described in [Using 2-Step Authentication](https://mackerel.io/docs/entry/howto/MFA).
2. Go to the [Organization's Settings](https://mackerel.io/my?tab=setting) page and turn on the Mandatory 2-Step Authentication option. This can be done by any user with administrative privileges or above.

Once configured, users who do not have 2-Step Authentication enabled will not be able to access the organization until it is enabled.

### What do you lose when 2-Step Authentication is mandatory?

Organizations that require 2-Step Authentication are subject to some restrictions in order to prevent the organization from becoming impossible to manage.

- Ownership of an organization that requires 2-Step Authentication can not be transfered to a user who does not have 2-Step Authentication configured.
- If one or more organization requires 2-Step Authentication, the owner will not be able to cancel their 2-Step Authentication.
- An owner can not delete their login password
- The Password option cannot be removed as an Accessible authentication method of an organization that requires 2-Step Authentication.

These restrictions can be avoided by turning off mandatory 2-Step Authentication. Additionally, if you have multiple organizations, this will not impact organizations where 2-Step Authentication is not required.
