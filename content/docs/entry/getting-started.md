---
Title: Getting Started
Date: 2014-11-03T18:15:20+09:00
URL: https://mackerel.io/docs/entry/getting-started
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450072239698
---

## What's with the name Mackerel?

The name Mackerel comes from a play on words based on the Japanese pronunciation of the word "server," which when spoken in Japanese sounds like "saabaa" which sounds kind of like "saba," the Japanese word for mackerel. Sounds tasty! 

## What is Mackerel?

Mackerel is a collaborative monitoring, alert resolution, metrics visualization, and server management platform used by providers of quality web and mobile applications.

In Mackerel, hosts are registered by installing a program called mackerel-agent on the server. Hosts can be managed via "Services" (a categorization of cooperatively functioning hosts) and "Roles" (a further categorization of the host inside of the Service) and host information collected by the agent can be viewed on the Web.

In Mackerel, users create and belong to organizations ([see glossary](https://mackerel.io/docs/entry/glossary#organization)) where groups of users can collectively manage a wide span of server operations as a team.

## Register, and Create a New Organization

To start using Mackerel, an organization must first be created.

1. Access the [sign-up page](https://mackerel.io/signup).
2. Enter an e-mail address and submit the form.
3. Follow the instructions on the screen to create an organization. A confirmation e-mail will be sent to the previously registered e-mail address, so please follow the instructions specified in the e-mail to set your password.

## Register and Add Yourself to an Existing Organization

If a colleague is already using Mackerel, they can send you an invitation to join an existing organization.

1. Ask another user who already belongs to an organization to send you an invitation to join. （→ [Inviting users to join your Mackerel organization](https://mackerel.io/docs/entry/howto/invite-others)）
2. Access the URL specified on the invitation e-mail to go to the sign-in page and click on "Sign Up."
3. On the sign-up page, enter your e-mail address and submit the form.
4. When this is done, it will display the invitation page for that organization. Click on "Join ○○" to join the organization.
5. You can now start using Mackerel. A confirmation e-mail will be sent to the previously registered e-mail address, so please follow the instructions specified in the e-mail to set your password.

<h2 id="next-step">What to Do Next</h2>

- Manage hosts
  - To register a host, [the agent must be installed](https://mackerel.io/docs/entry/howto/install-agent) on that host
  - To manage a host, you must first [create services and roles](https://mackerel.io/docs/entry/howto/create-services-and-roles)
- Start posting metrics
  - [Post user-specified custom metrics](https://mackerel.io/docs/entry/advanced/custom-metrics)
  - [Post service metrics](https://mackerel.io/docs/entry/advanced/fluentd)
- Manage users
  - [Invite other users](https://mackerel.io/docs/entry/howto/invite-others)
