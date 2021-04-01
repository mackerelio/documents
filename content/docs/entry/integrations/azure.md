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

[SQL Database](https://mackerel.io/docs/entry/integrations/azure/sql-database)・[Cache for Redis](https://mackerel.io/docs/entry/integrations/azure/redis-cache)・[Virtual Machines](https://mackerel.io/docs/entry/integrations/azure/virtual-machine)・[App Service](https://mackerel.io/docs/entry/integrations/azure/app-service)・[Functions](https://mackerel.io/docs/entry/integrations/azure/functions)・[Load Balancer](https://mackerel.io/docs/entry/integrations/azure/load-balancer)・[Database for MySQL](https://mackerel.io/docs/entry/integrations/azure/database-for-mysql)・[Database for PostgreSQL](https://mackerel.io/docs/entry/integrations/azure/database-for-postgresql)・[Application Gateway](https://mackerel.io/docs/entry/integrations/azure/application-gateway)・[Blob Storage](https://mackerel.io/docs/entry/integrations/azure/blob-storage)・[Azure Files](https://mackerel.io/docs/entry/integrations/azure/azure-files)

[^1]: According to the specifications of Azure Metrics Explorer, any dimensions exceeding 50 for a single metric are ignored.

# Integration method
Azure Integration will integrate using service principals.

A service principal is an ID that is used to access a specific Azure resource, and is more secure than using a user ID because it has limited permissions.

When configuring Azure integration, you’ll need access permission to do both "Create an application in the Azure Active Directory" and "Assign a role to the service principal". To confirm whether or not you have each permission, check out the items "Check Azure Active Directory permissions" and "Check Azure Subscription permissions" in the official document below.

[https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal:embed:cite]

This help document will introduce how to setup Azure integration with Azure CLI 2.0 and Azure Portal.

[:contents]

## Integrate using Azure CLI 2.0
This section explains how to integrate using Azure CLI.

### Install Azure CLI 2.0 
[https://docs.microsoft.com/en-us/cli/azure/install-azure-cli:embed:cite]

### Login and Service Principal configuration
First, login to Azure with the following command.
```console
$ az login
```

Next, use the following command to simultaneously create a service principal for Azure integration and grant viewer privileges.

`--years` sets the expiration date of the `password`, with the default at 1 year. Please note that when the expiration date expires, obtaining metrics will be impossible until the password is reconfigured.

```console
$ az ad sp create-for-rbac --role Reader --years <YEARS>
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
 
Among the above results, configure the `tenantId` value to that of Mackerel’s `Tenant ID`, the `appId` value to that of the `Client ID`, and the `password` to that of the `Client Secret`. In addition to the Tenant ID, Client ID, and Client Secret, make sure that the region, service, and tag are properly configured from Mackerel’s configuration screen.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20170623/20170623190254.png)
![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20171213/20171213165935.png)

Upon confirming the integration host, save the configuration. After a short while, the host created with Azure integration will be displayed in the host list.

That concludes how to integrate using Azure CLI 2.0.

## Integrate using Azure Portal
This section explains how to integrate using Azure Portal.

### Login to Azure Portal
Visit https://portal.azure.com and login.

### Obtain Tenant ID
Select Azure Active Directory from the sidebar and click "Properties".

Upon selecting, enter the displayed "Directory ID" in the `Tenant ID` field of Mackerel's Azure integration configuration screen.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20170623/20170623190736.png)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20170623/20170623190731.png)

### Create an Active Directory Application
When integrating with Mackerel, an application shall be created in Azure Active Directory in order to obtain metrics through application authority instead of user authority.

Select "App registrations" from the previous Active Directory screen.
 
![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20170626/20170626102652.png)

Next, select "New application registration" and the following screen will appear.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20170626/20170626102649.png)

Register the application to be integrated with Mackerel. The application name and Sign-on URL are not used in Mackerel. Specify the type of application as "Web app / API".

### Obtain Client ID and Client Secret
When registration is complete, select the registered application from the previous screen. A screen like the one shown below should appear. Enter the "Application ID" in the `Client ID` field on the Mackerel screen.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20170626/20170626102953.png)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20170626/20170626102954.png)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20170626/20170626103356.png)

Next, select "Keys" on this application screen. Here you can create keys. After configuring the key’s description, expiration date, and then saving, the value of the key will be displayed. Copy this and enter it in the `Client Secret` field in Mackerel. (As long as the permission is not configured, "Invalid" will be displayed from the Mackerel screen. Proceed to the "Permission setup" item and grant read-only permission)

**Caution** If the expiration date of this key expires, it will be impossible to obtain metrics with Mackerel's Azure integration from that point on. In this case, recreate a new key. If you select "Never expires", the expiration date will not expire for a while.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20170626/20170626103359.png)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20170626/20170626103351.png)

### Permission setup
Although the keys have been obtained and configured with the above process, you must lastly grant permissions. Grant the permission to read metric values.

Select "Subscription" from Portal screen sidebar. Then select the target subscription.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20170626/20170626104121.png) 

Select "Access control (IAM)". Here you can configure permissions for users and service principals. Now we will configure permissions for the Active Directory application that we created earlier.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20170626/20170626104118.png)

Click "Add" and then select "Reader" for "Role". (If anything other than Reader is selected here, Mackerel's Azure integration will not obtain metrics for security reasons)

Select the application that you created earlier in the "Select" field. (Note: Searching for an application name will only yield results for an exact match, so be sure to enter the name correctly)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20170626/20170626104443.png)

After a short while, authority will be granted to the application displayed as follows. Make sure that the application displayed under "Reader" is correct.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20170626/20170626104403.png)

Since Azure integration supports multiple subscriptions, repeat the above permission configuration for any subscription you would like to monitor.

Again, make sure that the region, service, and tag are properly configured in addition to the Tenant ID, Client ID, and Client Secret from Mackerel’s configuration screen.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20170626/20170626104400.png)
![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20171213/20171213165935.png) 

Upon confirming the integration host, save the configuration. After a short while, the host created with Azure integration will be displayed in the host list.

This concludes the integration method using Azure Portal.
 
## Filter by tag

Specify the tag from Mackerel’s configuration screen. Confirm the number of integration hosts and save.

If you specify the tag like `service: foo, service: bar`, instances that allow tags with a key of service and value of foo or tags with a key of service and value of bar will be targeted. If the key or value includes a comma `,` etc., enclose it with quotations (`"`or `'`). For example, if the key is `service, role` and the value is `foo, bar`, specify `"service, role": "foo,bar”`.
