---
Title: Google Cloud Integration
Date: 2020-09-15T12:00:00+09:00
URL: https://mackerel.io/docs/entry/integrations/gcp
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613631923961
---

Google Cloud Integration lets you manage Google Cloud Platform services as hosts in Mackerel and monitor metrics. This feature is only available for Trial and Standard plans.

Google Cloud services are registered as hosts in Mackerel and counted as billable targets.
Compute Engine will count as a Standard host while all other services will count as Micro hosts.
Adittionally, the Google Cloud Platform API is called every 5 minutes to obtain the target metrics. Please be aware that [monitoring charges](https://cloud.google.com/stackdriver/pricing#monitoring-costs) may occur for this reason.

Currently, the following Google Cloud services are supported for Google Cloud Integration. Refer to each corresponding document regarding obtainable metrics.

- [Compute Engine](https://mackerel.io/docs/entry/integrations/gcp/gce)
- [Cloud SQL](https://mackerel.io/docs/entry/integrations/gcp/cloudsql)
- [App Engine](https://mackerel.io/docs/entry/integrations/gcp/appengine)

# Integration method
Google Cloud Integration uses a service account key to integrate.

A service account is an account used by applications and instances on Google Cloud Platform. General user accounts cannot be used with Google Cloud Integration. Service accounts are more secure due to the fact that they have limited permissions according to purpose. Please refer to the official document linked below for more on service accounts.

[https://cloud.google.com/iam/docs/service-accounts:cite]

[:contents]

## Integrate using Cloud SDK
This section explains how to integrate using Cloud SDK.

### Install Cloud SDK
[https://cloud.google.com/sdk/docs/downloads-versioned-archives:cite]

### Login
First, authorize Cloud SDK with the following command. Then, set the project ID for Google Cloud Integration settings.

```sh
$ gcloud config configurations create NAME
$ gcloud init --configuration NAME
```

When setting a different project ID, you can switch it using the following command.

```sh
$ gcloud config set project --configuration=NAME PROJECT-ID
```

### Enable Cloud API
Next, use the following command to enable the Cloud API so that Mackerel can retrieve metrics.

```sh
# The required API
$ gcloud services --configuration=NAME enable cloudresourcemanager.googleapis.com
$ gcloud services --configuration=NAME enable monitoring.googleapis.com

## The API required for each service configured for integration

# Integrating Compute Engine
$ gcloud services --configuration=NAME enable compute.googleapis.com
# Integrating Cloud SQL
$ gcloud services --configuration=NAME enable sqladmin.googleapis.com
# Integrating App Engine
$ gcloud services --configuration=NAME enable appengine.googleapis.com
```

### Create service account key
Create a service account in order for Mackerel to retrieve metrics and assign a role. Please note that, for security reasons, this account cannot be used with Mackerel if permissions higher than viewer or global permissions have been granted.

```sh
$ gcloud iam --configuration=NAME service-accounts create ACCOUNT --display-name='Mackerel Integration' --description='Mackerel account'

# Required role assignments
$ gcloud projects --configuration=NAME add-iam-policy-binding PROJECT-ID --member=serviceAccount:ACCOUNT@PROJECT-ID.iam.gserviceaccount.com --role=roles/browser
$ gcloud projects --configuration=NAME add-iam-policy-binding PROJECT-ID --member=serviceAccount:ACCOUNT@PROJECT-ID.iam.gserviceaccount.com --role=roles/monitoring.viewer

## Resource-specific roles

# Integrating Compute Engine
$ gcloud projects --configuration=NAME add-iam-policy-binding PROJECT-ID --member=serviceAccount:ACCOUNT@PROJECT-ID.iam.gserviceaccount.com --role=roles/compute.viewer
# Integrating Cloud SQL
$ gcloud projects --configuration=NAME add-iam-policy-binding PROJECT-ID --member=serviceAccount:ACCOUNT@PROJECT-ID.iam.gserviceaccount.com --role=roles/cloudsql.viewer
# Integrating App Engine
$ gcloud projects --configuration=NAME add-iam-policy-binding PROJECT-ID --member=serviceAccount:ACCOUNT@PROJECT-ID.iam.gserviceaccount.com --role=roles/appengine.appViewer
```

Next, issue a service account key. In the following example, this is saved in the file **mackerel-key.json**.

```sh
$ gcloud iam --configuration=NAME service-accounts keys create mackerel-key.json --iam-account=ACCOUNT@PROJECT-ID.iam.gserviceaccount.com
```

In Mackerel's Google Cloud Integration settings screen, set the Google Cloud project ID to `project ID` and the contents of **mackerel-key.json** to `service account key`.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200924/20200924171051.png)

Check the integrated host, then save the settings. After a short while, the host created with Google Cloud Integration should appear in the host list.

## Integrate using Cloud Console

### Login to Cloud Console
Access https://console.cloud.google.com and login.

### Enable Cloud API
Go to the [API Library](https://console.cloud.google.com/apis/library) and enable the following two APIs.

- Cloud Resource Manager API
- Stackdriver Monitoring API

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200924/20200924171625.png)
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200924/20200924171636.png)

You can also enable the following APIs as necessary.

- Compute Engine API (if monitoring a Compute Engine instance)
- Cloud SQL Admin API (if monitoring a Cloud SQL instance)
- App Engine Admin API (if monitoring an App Engine service)

### Create service account key
Create a service account in order for Mackerel to retrieve metrics and assign a role. Please note that, for security reasons, this account cannot be used with Mackerel if permissions higher than viewer or global permissions have been granted.

Go to [Service Accounts](https://console.cloud.google.com/iam-admin/serviceaccounts) and create a service account to integrate with Mackerel. **example** is the account in the example below.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200924/20200924171922.png)

Next, add the necessary roles and continue.

- Browser (required)
- Monitoring Viewer (required)
- Compute Viewer (if monitoring a Compute Engine instance)
- Cloud SQL Viewer (if monitoring a Cloud SQL instance)
- App Engine Viewer (if you need to monitoring an App Engine service)

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200924/20200924172227.png)

Since there is no special access authority required to access the service account, you can simply click 'finish'. This will create a service account, but you still need to have a service account key issued in order to integrate with Mackerel.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200924/20200924173014.png)

Open the details page for the service account that you just created and click 'Create new key'.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200924/20200924173130.png)

Select 'JSON' as the key type.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200924/20200924173307.png)

In Mackerel's Google Cloud Integration settings screen, set the Google Cloud project ID to `project ID` and the contents of the service account key to `service account key`.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20200924/20200924171051.png)

Check the integrated host, then save the settings. After a short while, the host created with Google Cloud Integration should appear in the host list.
