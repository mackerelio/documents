---
Title: Getting Started
Date: 2014-11-03T18:15:20+09:00
URL: https://mackerel.io/docs/entry/getting-started
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450072239698
---

Mackerel is a service for visualizing and monitoring the resource status of multiple servers and service performance.

If this is your first time using Mackerel, you have to create an organization first. Or, you can join an existing organization ([Glossary of terminology](https://mackerel.io/docs/entry/glossary#organization)) by getting an invitation from users who have already joined that organization.

[:contents]

## Join an Organization

### Create a new organization

1. Access the [sign-up page](https://mackerel.io/signup).
2. Enter your email address.
3.If you agree to the Terms of Use and you are an adult or a minor with parental consent, please check two boxes.
4. Click on Sign Up.
5. Follow the on-screen instructions to create an organization.
6. A confirmation email will be sent to the email address you entered in step 2. Please follow the contents of the email to set your password.

### Join an existing organization

1. Ask an administrator or owner who already belongs to the organization to invite you. ([Inviting users to join your Mackerel organization](https://mackerel.io/docs/entry/howto/invite-others))
2. Access the URL in the invitation email to view the sign-up page.
3. If you agree to the Terms of Use and you are an adult or a minor with parental consent, please check two boxes.
4. Click on Sign Up.
5. The Organization Invitation page will appear. Click Join <organization name> to join the organization.
6. A confirmation email will be sent to your email address, Please follow the instructions in the email to set your password.

<h2 id="next-step">What to Do Next</h2>

### Register hosts

By installing mackerel-agent on the server to be monitored, it will be registered as a host ([What is a "host"?](https://mackerel.io/docs/entry/spec/about-host)) with Mackerel. <br>
Cloud integration is available to register cloud managed services as monitored hosts. To monitor container services, such as ECS tasks and Kubernetes pods, you can use mackerel-container-agent. Metrics collected by agents ([Metrics specifications](https://mackerel.io/docs/entry/spec/metrics)) and cloud integration are posted to registered hosts.

* Install mackerel-agent and register hosts
  * [Installing the agent](https://mackerel.io/docs/entry/howto/install-agent)
* Register your cloud product as a host
  * [AWS Integration](https://mackerel.io/docs/entry/integrations/aws)
  * [Azure Integration](https://mackerel.io/docs/entry/integrations/azure)
  * [Google Cloud Integration](https://mackerel.io/docs/entry/integrations/gcp)
* Register a task (ECS) or Pod (Kubernetes) as a host
  * [Monitoring Containers](https://mackerel.io/docs/entry/howto/container-agent)

### Manage hosts

Registered hosts can be managed together in units of "services" and "roles" according to their roles ([Services and Roles](https://mackerel.io/docs/entry/spec/about-service-role)). A graph of hosts grouped by common roles (role graph) can be viewed for each service from the [Service List](https://mackerel.io/my/services).

* [Creating services and roles](https://mackerel.io/docs/entry/howto/create-services-and-roles)

### Monitor Hosts

Monitoring rules can be set to generate alerts and notifications based on monitoring results. For example, "Host metric monitor" generates an alert when the value of a metric posted to a host exceeds or falls below a threshold set in a monitoring rule.

* [Setting up monitoring and alerts](https://mackerel.io/docs/entry/howto/alerts)

By setting up check monitoring in mackerel-agent, you can monitor the process status and logs in the server. To use check monitoring, you need to install the check plugin beforehand.

* [Using the official check plugin pack for check monitoring](https://mackerel.io/docs/entry/howto/mackerel-check-plugins)

User-created plug-ins that follow the check monitoring specifications can also be used for monitoring.

* [Adding monitors for script checks](https://mackerel.io/docs/entry/custom-checks)


### Post a variety of metrics

In addition to metrics posted by agents and cloud integration, metrics such as middleware can be posted by using metric plug-ins. Metric plug-ins must be installed beforehand in order to use them.

* [Using the official plugin pack to visualize middleware metrics](https://mackerel.io/docs/entry/howto/mackerel-agent-plugins)

User-created plug-ins that follow the custom metric specifications can also be used to post metrics.

* [Posting user-defined custom metrics](https://mackerel.io/docs/entry/advanced/custom-metrics)

Metrics that are not tied to a specific host can be posted as service metrics by using the API or other means. Service metrics can be viewed for each service from the [Service List](https://mackerel.io/my/services).

* [Service metrics - Mackerel API Documents (v0)](https://mackerel.io/api-docs/entry/service-metrics#post)
* [Posting Service Metrics with fluentd](https://mackerel.io/docs/entry/advanced/fluentd)

### Create your own dashboard

Dashboards can be created by freely arranging necessary graphs, etc. according to the purpose. This can be used to view information from multiple hosts in a cross-sectional manner.

* [Using Custom Dashboards](https://mackerel.io/docs/entry/howto/dashboard)

### Manage users

You can invite users with whom you wish to collaboratively manage the organization. You can also set operating privileges for each user.

* [Inviting users to join your Mackerel organization](https://mackerel.io/docs/entry/howto/invite-others)
* [User permissions](https://mackerel.io/docs/entry/spec/authority)

## When you are in trouble

### Use the help and FAQ

The [Help](https://mackerel.io/docs/) page provides information on how to use Mackerel. Frequently Asked Questions are available on the [FAQ](https://support.mackerel.io/hc/en-us) page. If you have any questions, you may be able to resolve them by reviewing these pages.

### Contact our support team

If you are unable to resolve your issue after reviewing the Help or FAQ, please contact us using the [Contact Us](https://support.mackerel.io/hc/en-us/requests/new) form. Mackerel's technical support team will assist you in resolving the issue. Please check [Support related questions](https://support.mackerel.io/hc/en-us/articles/360043006972) for our support policy. In addition, please be aware of the following links to help us resolve your concerns as soon as possible.

* [Tips to help your inquiries to the support team get resolved quicker](https://mackerel.io/blog/entry/20200616)