---
Title: Installing the agent
Date: 2014-11-11T11:58:47+09:00
URL: https://mackerel.io/docs/entry/howto/install-agent
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450073187361
---

Once you have completed user registration with Mackerel and created an organization, you will now be able to install the agent and register your hosts. Download the package suited to the environment you are currently running from the 「[mackerel-agent install page][]」 and install.

- [Installing mackerel-agent in Amazon Linux](./install-agent/amazon-linux)
- [Installing mackerel-agent in Red Hat Enterprise Linux derivatives](./install-agent/rpm)
- [Installing mackerel-agent in Ubuntu / Debian](./install-agent/deb)
- [Installing mackerel-agent in binary](./install-agent/binary)
- [Installing mackerel-agent on Windows](./install-agent/msi)
- [Installing mackerel-agent on macOS](./install-agent/mac)

Once the agent has been installed and has begun operating properly, the host will be registered with Mackerel. Confirm that hosts have been registered by checking the main [dashboard](https://mackerel.io/my/dashboard).

<h2 id="configuration">Agent settings</h2>

The agent configuration file located at `/etc/mackerel-agent/mackerel-agent.conf` will be used by default. (for a KCPS use agent: `/etc/mackerel-agent-kcps/mackerel-agent-kcps.conf`) 

For more detailed information please refer to [mackerel-agent specifications](https://mackerel.io/docs/entry/spec/agent).

By using the configuration file the following things can be implemented: 

- [Setting of services and roles](https://mackerel.io/docs/entry/spec/agent#setting-services-and-roles)
- [Submission of custom metrics](https://mackerel.io/docs/entry/advanced/custom-metrics)
- [Adding monitors for script checks](https://mackerel.io/docs/entry/custom-checks)

## If monitoring an external KCPS host with the mackerel-agent KCPS version

Please describe the following setting in the first line of the configuration file.

    apibase = "https://kcps-mackerel.io/"

##Uninstalling the agent

To uninstall the agent please carry out the uninstall process according to the packaging system you used.

Please do not forget to delete the file `/var/lib/mackerel-agent/id` which by default contains the host ID.

[mackerel-agent install page]: https://mackerel.io/my/instruction-agent

