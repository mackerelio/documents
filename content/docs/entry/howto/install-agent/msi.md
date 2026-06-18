---
Title: Installing mackerel-agent on Windows Server
Date: 2015-08-12T12:42:25+09:00
URL: https://mackerel.io/docs/entry/howto/install-agent/msi
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450105331216
---

[If you are using the KCPS version of Mackerel](#kcps)

## Installing the mackerel-agent

You can install the mackerel-agent using the GUI or from the command line.

Please allow the [communications that occur during mackerel-agent installation](https://mackerel.io/docs/entry/howto/install-agent#install) in advance.

### 1. Download the installer

On the [Register a new Host screen](https://mackerel.io/my/instruction-agent), select Microsoft Windows and click the download button. Copy the downloaded installer to the server to be monitored.

### 2. Install the mackerel-agent

#### Installing with the GUI
Double-click on the downloaded msi file and follow the dialog to install.

During installation, you will be prompted to enter an API key. On the [Register a new Host screen](https://mackerel.io/my/instruction-agent), select Microsoft Windows and enter the API key shown in "A. Install with GUI". The API key can also be obtained from the [API Keys tab in the Web console](https://mackerel.io/my?tab=apikeys).

#### Installing from the command line

On the [Register a new Host screen](https://mackerel.io/my/instruction-agent), select Microsoft Windows and run the command shown in "B. Install from command line" from an administrator command prompt.

```
msiexec /qn /i path/to/mackerel-agent-x64-latest.msi APIKEY="<YOUR_API_KEY>"
```

### 3. Confirm host registration

After installation is complete, the mackerel-agent will start automatically. Once the mackerel-agent starts, the host will be registered in Mackerel. Check the [Hosts screen](https://mackerel.io/my/hosts).

<!--
If the host is not registered, please refer to [FAQ: Host is not registered](https://support.mackerel.io/hc/en-us/articles/30952244573337).
-->

## Restarting the mackerel-agent

Restart the mackerel-agent from the Services list.

<h2 id="checking-mackerel-agent-log">Checking the mackerel-agent's log</h2>

The mackerel-agent's log can be viewed in Event Viewer under Windows Logs > Application. The event source is mackerel-agent.

## Updating the mackerel-agent

To update the mackerel-agent, perform an overwrite installation with the newer version's installer.

The latest installer can be obtained by selecting Microsoft Windows on the [Register a new Host screen](https://mackerel.io/my/instruction-agent) and clicking the download button.

## Uninstalling the mackerel-agent

Uninstall the mackerel-agent from Programs and Features in the Control Panel.

Some files will not be deleted during uninstallation. If they are no longer needed, please delete them.

- [Files not deleted when uninstalling the agent](https://mackerel.io/docs/entry/howto/install-agent#uninstall-files)

<h2 id="kcps">If you are using the KCPS version of Mackerel</h2>

For users of the KCPS version of Mackerel, please refer to the KCPS knowledge site.

- [Installing the mackerel-agent](https://doc.cloud-platform.kddi.ne.jp/guidebook/hands-on/kansi/kihon/agent-install/)
- [Restarting the mackerel-agent](https://doc.cloud-platform.kddi.ne.jp/guidebook/hands-on/kansi/kihon/agent-restart/)
- [Checking the mackerel-agent's log](#checking-mackerel-agent-log) (can be checked using the method described on this page)
- [Updating the mackerel-agent](https://doc.cloud-platform.kddi.ne.jp/guidebook/hands-on/kansi/kihon/mackerel-versionup/)
- [Uninstalling the mackerel-agent](https://doc.cloud-platform.kddi.ne.jp/faq/functions_operations/partner-service-functionoperation/mackerel-partner-service-functionoperation/17607/)
