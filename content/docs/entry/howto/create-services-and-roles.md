---
Title: Creating services and roles
Date: 2014-11-10T17:41:12+09:00
URL: https://mackerel.io/docs/entry/howto/create-services-and-roles
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450073120903
---

## Creating services

By creating services, you will be able to collectively manage all the hosts working in cooperation under that service. Hosts contained within a service are further sorted by roles.

To create a new service, after logging into Mackerel, go to [Services](https://mackerel.io/my/services) in the side bar and then click the “New Service” button in the upper right hand corner. This will bring you to the “Create a New Service” page where you can now enter a name for your new service, as well as include some other information in the Notes field if necessary. Click “Create” and your new service is good to go.

To add hosts to a service, it’s necessary to first create at least one role within that service. 

## Creating roles

A role is something that represents a group of hosts within a service that perform a similar function. By creating and using roles it's easy to collect and organize and manage a service’s hosts in terms of the role they serve. This makes it possible to view graphs partitioned by role as well. This also allows for the viewing of overlaid graphs separated by role.

A role must always belong to one service.

One way a role can be created is by assigning one to a host. To do this, first log into Mackerel and go to [Hosts](https://mackerel.io/my/hosts) in the side bar, then click the “Assign roles” button next to the host that you want to create a role for. This will open a dialogue box where you can either assign the host to an existing role or create a new role. Select “Create a New Role”. Now you can choose which service the new role will belong to and give the role a name as well. 

[f:id:mackerelio:20190222134907p:plain]

Roles can alternatively be created from any service page by clicking the "New Role" button in the top right-hand corner of the screen.

## Assigning roles to hosts

For more information on assigning roles to hosts within a service, please refer to [Assigning roles to hosts](https://mackerel.io/docs/entry/howto/assign-roles-to-hosts).

-

If you have any questions please contact our support team at support@mackerel.io
