---
Title: Mackerel Overview
Date: 2014-11-03T18:16:28+09:00
URL: https://mackerel.io/docs/entry/overview
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450072239778
---

Mackerel is an elegantly simple and powerful service that employs the concept of "roles" to optimize management and monitoring of hosts. By running mackerel-agent on your hosts, you can manage any number of hosts using both Mackerel's web GUI and API to visualize the statuses of your hosts and applications with a high degree of detail and customization.

Here are a few of the main features you can expect with Mackerel.

## Mackerel (on the web)

The following tasks and more can be done at (https://mackerel.io/):

- View list of registered hosts.
- Edit host information.
- Manage and group hosts into services and roles.
- Visualize hosts' resource information by role.
- Visualize resource data for individual hosts.

## mackerel-agent

mackerel-agent is a program that is installed on your hosts to periodically gather host information and metrics. That data is then sent to Mackerel where it is displayed in graphs to be managed and monitored.

<h3 id="support-environments">Supported Environments</h3>

As of now, Mackerel and mackerel-agent have been verified to operate in the following environments:

- Mackerel (web)
  - [Google Chrome](https://www.google.com/chrome/browser/desktop/index.html) latest version
  - [Mozilla Firefox](https://www.mozilla.org/en-US/firefox/new/) latest version
- mackerel-agent
    - Linux
        - CentOS 6/7/8, Ubuntu 16.04LTS/18.04LTS/20.04LTS,  Debian 8/9/10, Amazon Linux, Amazon Linux 2
        - Aside from the above, the agent will probably be run on most Linux systems (kernel version 2.6.23 or later), however we do not give official support for them at this time.
    - Windows
        - Windows Server 2012 and later 64-bit environments
    - Additionally, mackerel-agent is especially well suited for public IaaS cloud-based systems such as Amazon Web Services, Microsoft Azure and Google Compute Engine. (There may be reduced functionality on certain systems, such as those with specialized cloud network systems.)

<h3 id="tsdb-spec">Metric data retention</h3>

Mackerel saves metric data in a time series database.
Metric data is saved in 1 minute intervals, and stored for the period shown as follows.

| interval  | retention period |
| --------- | ---------------- |
| 1 min     | 460 days         |
| 1 day     | after 460 days   |

This chart is only significant to data retention periods; this information doesn't apply to graph display time span.
