---
Title: Installing mackerel-agent on Windows
Date: 2015-08-12T12:42:25+09:00
URL: https://mackerel.io/docs/entry/howto/install-agent/msi
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450105331216
---

[For users of Mackerel KCPS version](#kcps)

## Downloading the installer

Download the installer from <https://mackerel.io/file/agent/msi/mackerel-agent-x64-latest.msi>(64bit) or <https://mackerel.io/file/agent/msi/mackerel-agent-latest.msi>(32bit), and copy it into the host which is to be targeted for monitoring.

## Installing the agent on a Windows host
There are two ways to install the agent on a Windows host; install from the command line or using the GUI.

### Installing with the GUI
Double click on the icon of the downloaded msi file and follow the dialog.

Part way through, you will be asked to input an API key, but this can be checked from the [API tab in the Organization page of the Mackerel Management screen](https://mackerel.io/my?tab=apikeys).

## Installing the agent on a host from the administrator’s command prompt

On the host that will be targeted for monitoring, using the command `msiexec` from the administrator’s command prompt will install the agent.

    msiexec /qn /i path/to/mackerel-agent-x64-latest.msi APIKEY="<YOUR API KEY>"

You can check `<YOUR API KEY>` by going to the API Keys tab in the Organization page in Mackerel. After installation has been completed, a completion message will not be displayed, but if the installation was successful the mackerel-agent will start up automatically. You can check the Windows Service management screen to see if the mackerel-agent is running or not. The service name will be "mackerel-agent", and it's status should be "running".

## Checking the mackerel-agent’s log

The mackerel-agent’s log will be outputted to the application log in Windows’ event log. Please refer here when you want to view the agent’s behavior. 

## Updating mackerel-agent

To update the mackerel-agent download the latest installer and complete the install to be able to update to the newest version of the agent.

We suggest performing an update when new features are released or when improvements are made to the agent's stability.

Even without updating the agent, the standard features (posting metrics, monitoring, etc.) will still be operational.

## Uninstalling mackerel-agent

mackerel-agent can be uninstalled by going to Programs and Features in the Control Panel and deleting it. After uninstalling, the ID files will remain by default, so please restart your machine and delete each folder in `C:\Program Files (x86)\Mackerel`. 

<h1 id="kcps">Installing the mackerel agent KCPS version on Windows</h1>

Download the installer from <http://repo-kcps.mackerel.io/file/msi/mackerel-agent-x64-latest-kcps.msi>(64bit) or <http://repo-kcps.mackerel.io/file/msi/mackerel-agent-latest-kcps.msi>(32bit), and copy it into the host which is to be targeted for monitoring.

The following URL can also be used to download the installer directly from a host on KCPS.

    http://198.18.0.16/file/msi/mackerel-agent-latest-kcps.msi

## Installing the agent on a Windows host
There are two ways to install the agent on a Windows host; install from the command line or using the GUI.

### Installing with the GUI
Double click on the icon of the downloaded msi file and follow the dialog.


## Installing the agent on a host from the administrator’s command prompt

Using the command `msiexec` from the administrator’s command prompt will install the agent on a host monitoring target.

    msiexec /qn /i path/to/mackerel-agent-x64-latest-kcps.msi APIKEY="<YOUR API KEY>"

After installation, if completed successfully, the mackerel-agent will start up automatically with no further notification. You can confirm whether or not the mackerel-agent is running from the Windows Service management screen. The service name will be "mackerel-agent", and it's status should be "running".

## Checking the mackerel-agent log

The mackerel-agent log will be outputted to the application log in Windows’ event log. Please refer here when you want to view the agent’s behavior. 
