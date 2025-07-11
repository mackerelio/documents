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
  - [Google Chrome](https://www.google.com/chrome/) (desktop version) latest version
  - [Mozilla Firefox](https://www.mozilla.org/firefox/) (desktop version) latest version
  - [Microsoft Edge](https://www.microsoft.com/edge) (desktop version) latest version
- mackerel-agent
    - Linux
        - Red Hat Enterprise Linux 8/9/10, MIRACLE LINUX 8/9, AlmaLinux 8/9/10, Rocky Linux 8/9/10, Oracle Linux 8/9
        - CentOS Stream 9
        - Ubuntu 22.04LTS/24.04LTS, Debian 11/12
        - Amazon Linux 2, Amazon Linux 2023
        - x64 and arm64 processor architecture including AWS Graviton processors.
        - Aside from the above, the agent will probably be run on most Linux systems (kernel version 3.2 or later) or Red Hat Enterprise Linux derivatives, however we do not give official support for them at this time.
    - Windows
        - Windows Server 2016, 2019, 2022 (LTSC), 2025 (LTSC) for x64-based Systems
        - Windows Update (KB5022661) for "Windows support for the Trusted Signing program" released in September 2021 must be applied.
        - "Microsoft Identity Verification Root Certificate Authority 2020" certificate authority (CA) must be installed to properly verify signed module.
        - See [KB5022661—Windows support for the Trusted Signing (formerly Azure Code Signing) program](https://support.microsoft.com/topic/kb5022661-windows-support-for-the-azure-code-signing-program-4b505a31-fa1e-4ea6-85dd-6630229e8ef4) for more information.
  - Additionally, mackerel-agent is especially well suited for public IaaS cloud-based systems such as Amazon Web Services, Microsoft Azure and Google Compute Engine. (There may be reduced functionality on certain systems, such as those with specialized cloud network systems.)

<h3 id="tsdb-spec">Metric data retention</h3>

Mackerel saves metric data in a time series database.
Metric data is saved in 1 minute intervals, and stored for the period shown as follows.

| interval  | retention period |
| --------- | ---------------- |
| 1 min     | 460 days         |
| 1 day     | after 460 days   |

This chart is only significant to data retention periods; this information doesn't apply to graph display time span.
