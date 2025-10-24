---
Title: Assigning roles to hosts
Date: 2014-12-09T12:47:31+09:00
URL: https://mackerel.io/docs/entry/howto/assign-roles-to-hosts
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450076538325
---

## From the web UI

To assign a role that you have created to a host, first log into Mackerel and access the [list of hosts](https://mackerel.io/hosts) by clicking on “Hosts” in the sidebar. Next click on “Assign roles” in the SERVICE / ROLE column of the host you wish to assign a role to. A dialogue box will open with a list of roles; select one and click update to assign the role.

## From the agent

By editing the agent’s configuration file, you can have the agent automatically assign services and roles when it boots up. For more information please refer to the [agent specifications documentation](https://mackerel.io/docs/entry/spec/agent#setting-services-and-roles).

