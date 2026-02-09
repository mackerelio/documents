---
Title: Azure Integration
Date: 2017-06-26T11:08:35+09:00
URL: https://mackerel.io/docs/entry/integrations/azure
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812273182826
---

Using Azure integration, you can manage Azure cloud products as a host of Mackerel and monitor its metrics. This function is only offered in the Trial plan and Standard plan.

Each Azure cloud product will be registered as one host in Mackerel and therefore be counted as a billable host.
Virtual Machines will count as standard hosts and all other products will count as micro hosts. 
Additionally, the API of Azure will be called every 5 minutes for each targeted metric to be obtained. Please take note, for this reason, an [Azure Monitor API usage fee](https://azure.microsoft.com/en-us/pricing/details/monitor/) may occur.

Currently, the following Azure cloud products are supported. For information on obtaining metrics, please refer to each individual document.[^1]

[SQL Database](https://mackerel.io/docs/entry/integrations/azure/sql-database)・[Cache for Redis](https://mackerel.io/docs/entry/integrations/azure/redis-cache)・[Virtual Machines](https://mackerel.io/docs/entry/integrations/azure/virtual-machine)・[App Service](https://mackerel.io/docs/entry/integrations/azure/app-service)・[Functions](https://mackerel.io/docs/entry/integrations/azure/functions)・[Load Balancer](https://mackerel.io/docs/entry/integrations/azure/load-balancer)・[Database for MySQL](https://mackerel.io/docs/entry/integrations/azure/database-for-mysql)・[Database for PostgreSQL](https://mackerel.io/docs/entry/integrations/azure/database-for-postgresql)・[Application Gateway](https://mackerel.io/docs/entry/integrations/azure/application-gateway)・[Blob Storage](https://mackerel.io/docs/entry/integrations/azure/blob-storage)・[Azure Files](https://mackerel.io/docs/entry/integrations/azure/azure-files)・[Azure NetApp Files](https://mackerel.io/docs/entry/integrations/azure/netapp-files)

[^1]: According to the specifications of Azure Metrics Explorer, any dimensions exceeding 50 for a single metric are ignored.

## Integration method
Azure Integration will integrate using service principals.

A service principal is an ID that is used to access a specific Azure resource, and is more secure than using a user ID because it has limited permissions.

When configuring Azure integration, you’ll need access permission to do both "Create an application in the Microsoft Entra ID"[^2] and "Assign a role to the service principal". To confirm whether or not you have each permission, check out the items "Check Microsoft Entra ID permissions" and "Check Azure Subscription permissions" in the official document below.

[^2]: Azure Active Directory has been renamed to Microsoft Entra ID. Some images in the document are from before the renaming.

[https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal:embed:cite]

This help document will introduce how to setup Azure integration with Azure CLI 2.0 and Azure Portal.

[:contents]

## Set up authentication credentials for your Azure account

Create authentication credentials using either Azure CLI 2.0 or Azure Portal, and set them up in Azure Integration.

### Integrate using Azure CLI 2.0
This section explains how to integrate using Azure CLI.

#### Install Azure CLI 2.0
[https://docs.microsoft.com/en-us/cli/azure/install-azure-cli:embed:cite]

#### Login and Service Principal configuration
First, login to Azure with the following command.
```console
$ az login
```

To configure the service principal, you will need the subscription ID. Please obtain it in advance using the following command.

```console
$ az account show --query id --output tsv
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

Next, use the following command to simultaneously create a service principal for Azure integration and grant viewer privileges.

`--years` sets the expiration date of the `password`, with the default at 1 year. Please note that when the expiration date expires, obtaining metrics will be impossible until the password is reconfigured.

```console
$ az ad sp create-for-rbac --role Reader --scope /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --years <YEARS>
{
  "appId": "abcdefgh-abcd-efgh-abcd-abcdefghijkl",
  "displayName": "azure-cli-2017-01-23-45-67-89",
  "name": "http://azure-cli-2017-01-23-45-67-89",
  "password": "foofoo-bar-bar-foo-foobarbaz",
  "tenantId": "12345678-1234-5678-1234-123456781234",
}
```

Check out the following link for more about the options for `create-for-rbac` and more. 
 
[https://docs.microsoft.com/en-us/cli/azure/ad/sp#create-for-rbac:embed:cite]
 
Among the above results, please set the value of `tenantId` as the `Tenant ID` in Mackerel, the value of `appId` as the `Client ID`, and `password` as the `Secret Key`. In Mackerel's settings screen, make sure that the Tenant ID, Client ID, and Secret Key are correctly set. If authentication fails, please check [here](#Verify-if-the-authentication-credentials-are-correct) for verification of the authentication information.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240215/20240215074908.png)

If there are no issues, proceed to [Select the Services and Subscriptions to Integrate](#Select-the-Services-and-Subscriptions-to-Integrate).

### Integrate using Azure Portal
This section explains how to integrate using Azure Portal.

#### Login to Azure Portal
Visit https://portal.azure.com and login.

#### Obtain Tenant ID
Select Microsoft Entra ID from the sidebar and click "Properties".

Upon selecting, enter the displayed "Tenant ID" in the `Tenant ID` field of Mackerel's Azure integration configuration screen.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240215/20240215064438.png)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240215/20240215064452.png)

### Create an Entra ID Application
When integrating with Mackerel, an application shall be created in Microsoft Entra ID in order to obtain metrics through application authority instead of user authority.

Select "App registrations" from the previous Entra ID screen.
 
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240215/20240215065004.png)

Next, select "New registration" and the following screen will appear.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240215/20240215065222.png)

Please register an application for integration with Mackerel. The application's display name and redirect URI are not used on the Mackerel side. When choosing from 'Public client/Native', 'Web', or 'Single-page application (SPA)' for the redirect URI, please select 'Web'.

#### Obtain Client ID and Client Secret
When registration is complete, select the registered application from the previous screen. A screen like the one shown below should appear. Enter the "Application (client) ID" in the `Client ID` field on the Mackerel screen.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240215/20240215065906.png)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240215/20240215065911.png)

On this application's screen, next select 'Certificates & secrets'. Here, you can create a client secret. Click on 'New client secret', set a description and an expiration for the secret, and save. The value of the secret will be displayed. Copy this and enter it into the `Secret Key` field on the Mackerel side.  
It will be shown as 'Invalid' on the Mackerel screen until permission settings are configured. Proceed to the 'Permission settings' section and grant viewing permissions.

**Caution** If the expiration date of this secret passes, it will no longer be possible to obtain metrics in Mackerel's Azure Integration from that point. In such a case, please create a new secret.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240215/20240215071358.png)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240215/20240215071458.png)

#### Permission setup
Although the keys have been obtained and configured with the above process, you must lastly grant permissions. Grant the permission to read metric values.

Please select 'Subscriptions' from the sidebar of the Portal screen. Then choose the target subscription and select 'Access control (IAM)'. Here, you can set permissions for users and service principals.

 Now we will configure permissions for the Entra ID application that we created earlier.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240215/20240215072659.png) 

Click on 'Add' and then 'Add role assignment'. For the 'Role', select 'Reader' and proceed.  
If you accidentally select a permission other than 'Reader' here, for safety reasons, Mackerel's Azure Integration will not retrieve metrics.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240215/20240215073625.png)

In 'Assign access to', check 'User, group, or service principal', and for the members, select the application you created earlier.  
Note: The search for the application name is prefix-based, so it won't appear unless you start typing from the beginning.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240215/20240215073630.png)

Click on 'Review + assign', and after a while, the permissions will be granted to the application.

In the 'Role assignments' tab, make sure that the application listed under the 'Reader' section is correct.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240215/20240215074305.png)

Since Azure integration supports multiple subscriptions, repeat the above permission configuration for any subscription you would like to monitor.

In Mackerel's settings screen, make sure that the Tenant ID, Client ID, and Secret Key are correctly set. If authentication fails, please check [here](#Verify-if-the-authentication-credentials-are-correct) to verify the authentication information.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240215/20240215074908.png)

### Verify if the authentication credentials are correct

If there are errors in the settings and the integration fails, the following reasons may be displayed.

- Authentication with the provided credentials failed. Please verify the entered credentials.
- Excessively strong permissions have been granted. Please grant only the minimum necessary permissions.
- Currently, authentication is temporarily unavailable. Please try again after some time.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240215/20240215075001.png)

Please review your settings after confirming that there are no issues with the authentication credentials and permissions.

## Select the Services and Subscriptions to Integrate

Choose the region and check the services you want to integrate.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240215/20240215075504.png)


If you want to select additional subscriptions to integrate, check the desired subscriptions. If you do not select any, all subscriptions will be included.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240215/20240215080423.png)

After confirming the hosts to integrate, save the settings. After a while, the hosts created by Azure Integration will be displayed in the host list.
 
## Filter by tag

Specify the tag from Mackerel’s configuration screen. Confirm the number of integration hosts and save.

If you specify the tag like `service: foo, service: bar`, instances that allow tags with a key of service and value of foo or tags with a key of service and value of bar will be targeted. If the key or value includes a comma `,` etc., enclose it with quotations (`"`or `'`). For example, if the key is `service, role` and the value is `foo, bar`, specify `"service, role": "foo,bar”`.

## Configure automatic new metrics addition

Mackerel may add new metrics as each Azure service is updated.

By turning on "Add new metrics automatically", you can avoid having to select metrics for retrieval on the Azure integration settings page each time they are added.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20251030/20251030151120.png" width="1627" height="213" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>

## Limit metrics retrieved

Select the metrics you wish to retrieve in the Mackerel Azure integration configuration page. Uncheck any unwanted metrics and save them.

For example, to exclude `virtual_machine.cpu.percent` from being retrieved, simply uncheck the corresponding checkbox as shown below.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20251030/20251030151159.png" width="526" height="641" loading="lazy" title="" class="hatena-fotolife" itemprop="image"></span></p>
