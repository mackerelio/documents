---
Title: Glossary of terminology
Date: 2015-02-16T17:31:01+09:00
URL: https://mackerel.io/docs/entry/glossary
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450083905096
---

The following is an explanation of terminology used within Mackerel.

## Service

In Mackerel, services are one of the two levels in to which hosts can be sorted. An example of a service could be something like "Hatena Bookmark", or "in-house system", etc. Typically, hosts are assigned to multiple “roles” which then function cooperatively within a service. By grouping hosts that are related or perform similar functions into roles and services, monitoring and managing hosts can be optimized.

## Role

A role is a function of further categorizing and sorting hosts within a service based on the function they perform (application, database, etc.) Hosts within a role are overlaid in one graph making it easier to pick up on trends in graphs and manage your hosts in terms of function or role rather than one at a time.

<h2 id="host">Host</h2>

A host is one instance of an OS that the mackerel-agent is running on. It’s possible to assign one host to multiple roles (as well as services). 

**Status**: Hosts are given statuses (standby, maintenance, working, etc.) for the purpose of indicating their current condition. A host's status is indicative of a its position in the life-cycle of a host within Mackerel, from initial set-up, to being deployed into a service, to eventual retirement.

**Host ID**: Mackerel identifies each host by its unique host ID. The host ID is saved in the file `/var/lib/mackerel-agent/id` in a host that the agent is running on. Because of this, please be aware that if the host ID file `/var/lib/mackerel-agent/id` were to be copied into a different host, that host would then be recognized as the same host and monitoring and metrics posting would not be able to function properly.

<h2 id="organization">Organization</h2>

Organizations are how Mackerel identifies companies, organizations, and groups of individuals who use Mackerel. A single Mackerel user’s account can be attached to multiple organizations. Information from hosts and services will never be shared outside the bounds of the organization it belongs to.

If an organization name you have entered is already taken by another organization, it will not be able to be used.

Multiple users may belong to a single organization as a team. Additionally, a single user can belong to multiple organizations such as one for personal use and one for company use.

- → [How to invite others to an organization](https://mackerel.io/docs/entry/howto/invite-others)

## Metrics

Metrics are data that indicate the current state of a variety of parameters within a host. They are collected and sent to Mackerel in one-minute intervals by the mackerel-agent. Standard metrics such as CPU usage, memory usage, and IO are collected and sent to Mackerel by default where they can be viewed in graphs and managed on the web.

Its also possible to register user-defined custom metrics using a simple Sensu plug-in compatible protocol.

- →[How to post user defined metrics](https://mackerel.io/docs/entry/advanced/custom-metrics)
