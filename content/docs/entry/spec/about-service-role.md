---
Title: Services and Roles
Date: 2020-09-24T14:54:52+09:00
URL: https://mackerel.io/docs/entry/spec/about-service-role
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613631852067
CustomPath: spec/about-service-role
---

In Mackerel, instead of managing hosts one by one, they are aggregated into appropriate groups and managed/configured by their unit.

A "service" is the unit used to group multiple hosts that make up the system or service to be monitored. A host can belong to multiple "services" as well.

[f:id:mackerelio:20200923113905p:plain]

Within a "service", hosts are alloted certain roles/responsibilities. To express this concept, Mackerel uses "roles".

A "role" is the unit used to further catorgorize hosts that belong to a "service". "Roles" can be created for each "service" and a host can belong to multiple "roles". Hosts that belong to a "service" must have a "role".

[f:id:mackerelio:20200923113947p:plain]

You can create as many "services" and "roles" as you like for free. Refer to the following page for more on how to so.

[Creating services and roles - Mackerel Help](https://mackerel.io/docs/entry/howto/create-services-and-roles)

<h2 id="benefit-service-role">The advantages of creating "services" and "roles"</h2> 
Creating "services" and "roles" and grouping multiple hosts together lets you do more. Below are some of the benefits.

- Groups of hosts are easier to manage when linked to "services" and "roles" that are based on specific policies.
- Metadata can be added and managed for "services" and "roles".
    - [https://mackerel.io/api-docs/entry/metadata#roleput:title]
- Monitoring targets can be filtered by "service"/"role" using a [monitor's](https://mackerel.io/docs/entry/howto/alerts) `filter conditions`. 
- Hosts/alerts can be filtered by "service"/"role" from the [Hosts list screen](https://mackerel.io/my/hosts) and [Alerts list screen](https://mackerel.io/my/alerts).


<figure class="figure-image figure-image-fotolife" title="Monitors and notification groups can be configured for "services"">[f:id:mackerelio:20200923114109p:plain]<figcaption>Monitors and notification groups can be configured for "services".</figcaption></figure>

<figure class="figure-image figure-image-fotolife" title="Likewise, monitors and notification groups can be configured for "roles"">[f:id:mackerelio:20200923114203p:plain]<figcaption>Likewise, monitors and notification groups can be configured for "roles"</figcaption></figure> .

The following are features available for "services".

- Recipients of alert notifications via [notification groups](https://mackerel.io/docs/entry/howto/alerts#notification-group) can be switched on a service-by-service basis.
- Numerical indicators of a service (metrics that are not / should not be linked to a specific host) can be posted for the "service" ([service metrics](https://mackerel.io/api-docs/entry/service-metrics)).
    - Response time obtained using an [external URL monitor](https://mackerel.io/docs/entry/external-monitoring) can be registered as a service metric.


The following are features available for "roles".
    
- Graphs of multiple hosts that belong to the same role can be displayed together in one graph.
    - Load statuses of past hosts that no longer exist can also be checked in this graph.
    - There are a few things to consider when using this feature. See the 'Notes regarding "roles"' section below.

<figure class="figure-image figure-image-fotolife" title="When a "role" is displayed in a single graph, each host is color-coded.">[f:id:mackerelio:20200923114309p:plain]<figcaption>When a "role" is displayed in a single graph, each host is color-coded.</figcaption></figure>


<h2 id="unit-example-service-role">Example units for creating "services" / "roles"</h2>
When it comes to creating a "service" / "role" unit, there is no wrong answer. An optimal unit can depend on scale, provision method, and organizational structure of the target system or service. Consider the following examples.

<h3 id="unit-example-service">Example units of "services"</h3>
- A unit based on the provided system or service
- A unit based on the environment of the provided system or service
    - such as `staging` or `production`
- A micro-service based unit, such as a system consisting of micro-service architecture

<h3 id="unit-example-role">Example units of "roles"</h3>
- A unit based on each function of hosts belonging to the "service"
    - such as "application server", "batch server", "database server", etc.
- A unit based on applications/middleware running on the server
    - Servers are grouped with those that should have similar load trends
- A unit based on host server specs
- A unit based  on monitoring level
    - such as `filesystem-90` or `filesystem-95` ([Monitors](https://mackerel.io/docs/entry/howto/alerts) that correspond to roles are configured and operated separately)

Taking into consideration that "services" and "roles" can be used to narrow down and filter multiple host groups, creating an optimal unit is important.

<h2 id="service-consideration">Notes regarding "services"</h2>
The following should be taken into consideration when using "services".

- "Services" cannot be configured to span multiple organizations.
- Once created, the name of a "service" cannot be changed. You must recreate a new "service" and delete the old one. "Services" can be deleted via the settings link displayed to the right of the "service" name.

[f:id:mackerelio:20200930172003p:plain]

<h2 id="role-consideration">Notes regarding "roles"</h2>
The following should be taken into consideration when using "roles".

― "Roles" cannot be configured to span multiple organizations.
- Using "roles", you can display graphs of multiple hosts that belong to the same "role" together in one graph, but the graph will only contain host information from after the configuration to that "role".
