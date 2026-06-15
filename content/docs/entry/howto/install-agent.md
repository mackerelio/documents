---
Title: Installing the agent
Date: 2014-11-11T11:58:47+09:00
URL: https://mackerel.io/docs/entry/howto/install-agent
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450073187361
---

Once you have registered with Mackerel and created an organization, you will be able to install the agent and register your hosts. For installation instructions, refer to the help documentation for your OS. For supported environments, refer to [Supported Environments](https://mackerel.io/docs/entry/overview#support-environments).

<h2 id="install">Installation instructions</h2>

For installation instructions, refer to the help documentation for each OS.

- [Installing mackerel-agent on Amazon Linux](./install-agent/amazon-linux)
- [Installing mackerel-agent on CentOS / Red Hat Enterprise Linux derivatives](./install-agent/rpm)
- [Installing mackerel-agent on Ubuntu / Debian](./install-agent/deb)
- [Using the binary file directly on Linux](./install-agent/binary)
- [Installing mackerel-agent on Windows Server](./install-agent/msi)

<h2 id="communication">Communication during agent installation</h2>

When installing the agent using the setup script, communication will occur with the following destinations. In environments where outbound internet access is restricted, please allow communication to these destinations before installing the agent.

- `https://mackerel.io/`
  - For the destination IP addresses and ports, refer to [What is Mackerel's IP address and port number?](https://support.mackerel.io/hc/en-us/articles/360039633271)
- Repository
  - For Amazon Linux / CentOS / Red Hat Enterprise Linux derivatives:
    - `http://yum.mackerel.io/`
  - For Ubuntu / Debian:
    - `http://apt.mackerel.io/`
  - Repositories are hosted on Amazon S3. For the IP address ranges used by Amazon S3, refer to [AWS IP address ranges](https://docs.aws.amazon.com/vpc/latest/userguide/aws-ip-ranges.html)

<h2 id="start">Communication when the agent is running</h2>

After the agent is installed, communication will occur with the following destinations. If communication is unavailable, the agent will fail to start and metrics cannot be posted.

- `https://api.mackerelio.com/`
  - For the destination IP addresses and ports, refer to [What is Mackerel's IP address and port number?](https://support.mackerel.io/hc/en-us/articles/360039633271)

For servers that cannot communicate directly with the internet, the agent supports the use of a [proxy server](https://mackerel.io/docs/entry/spec/agent#config-file-proxy).

<h2 id="configuration">Agent configuration file</h2>

The agent configuration file is located at the following paths. For details on what can be configured using this file, refer to [mackerel-agent specifications](https://mackerel.io/docs/entry/spec/agent).

- For Amazon Linux / CentOS / Red Hat Enterprise Linux derivatives / Ubuntu / Debian:
  - `/etc/mackerel-agent/mackerel-agent.conf`
  - KCPS version: `/etc/mackerel-agent/mackerel-agent-kcps.conf`
- For Windows Server:
  - 64-bit: `C:\Program Files\Mackerel\mackerel-agent\mackerel-agent.conf`
  - 32-bit: `C:\Program Files (x86)\Mackerel\mackerel-agent\mackerel-agent.conf`

<h2 id="uninstall">Uninstalling the agent</h2>

Uninstallation instructions are available in the [installation instructions](#install) help documentation for each OS.

<h3 id="uninstall-files">Files not deleted when uninstalling the agent</h3>

The agent configuration file and id file under the following directories will not be deleted even when the agent is uninstalled. If you reinstall the agent while these files remain, you can reuse the host ID and configuration. However, if the host with that ID has already been retired, the ID cannot be reused and the agent will fail to start. In this case, please delete the id file before starting the agent.

- For Amazon Linux / CentOS / Red Hat Enterprise Linux derivatives / Ubuntu / Debian:
  - `/var/lib/mackerel-agent`
  - KCPS version: `/var/lib/mackerel-agent-kcps`
- For Windows Server:
  - 64-bit: `C:\Program Files\Mackerel\mackerel-agent`
  - 32-bit: `C:\Program Files (x86)\Mackerel\mackerel-agent`
