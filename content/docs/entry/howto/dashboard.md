---
Title: Using Custom Dashboards
Date: 2015-07-13T17:32:20+09:00
URL: https://mackerel.io/docs/entry/howto/dashboard
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450101369423
---

# Custom Dashboards
With this feature, user-customized dashboards can be added to the standard dashboard view in Mackerel.

## Creating a custom dashboard
Click "Dashboards" located in left sidebar menu to change to the dashboard management screen.

Then click the "New Custom Dashboard" button.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240909/20240909104114.png" alt="f:id:mackerelio:20240909104114:plain" title="f:id:mackerelio:20240909104114:plain" class="hatena-fotolife" itemprop="image"></span></p>


### Create from template

Click "Generate custom dashboard manually" button.

This will show dialog to crete dashboard from template.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240909/20240909143333.png" alt="f:id:mackerelio:20240909143333:plain" title="f:id:mackerelio:20240909143333:plain" class="hatena-fotolife" itemprop="image"></span></p>

Enter a name for the new Custom Dashboard in the title field.

Select the appropriate template for the dashboard you want to create and click the "Next" button.
For more information on the available templates, see [Templates available for custom dashboard generation](#templates) below.

Enter or select the required fields for each template and click the "Create" button.
Based on the settings and the posted metrics, you will be redirected to the generated dashboard page.

If you want to edit the generated dashboard, see the next [Create by yourself](#manual) section.

<h3 id="manual">Create by yourself</h3>

Click "Create custom dashboard manually" button.

This will bring up the "Create a new Custom Dashboard" screen.

Enter a name for the new Custom Dashboard in the title field.

You can add widgets by dragging and dropping icons inside the frame at the bottom of the screen. For more on widgets, see "Available widgets for custom dashboards" below.

[f:id:mackerelio:20181113153659p:plain]

After you’ve placed your widgets, if you want to edit the contents or delete a widget, use the icons displayed in the upper right corner of each widget. You can rearrange and resize widgets by dragging and dropping. You can also duplicate a widget by dragging and dropping while pressing the Alt (option) key.

[f:id:mackerelio:20181113153723p:plain]

To undo your last action, click the "Undo" button.

[f:id:mackerelio:20181113153741p:plain]

When you’ve finished editing, complete the process by hitting the create button.

## Available widgets for custom dashboards
The following widgets can be arranged in Custom Dashboards. For more details, check out the respective pages for each feature.

<dl>
    <dt><a href="https://mackerel.io/docs/entry/howto/dashboard/graph">Graph widgets</a></dt>
    <dd>Display various graphs.</dd>
    <dt><a href="https://mackerel.io/docs/entry/howto/dashboard/value">Value widgets</a></dt>
    <dd>Display the latest values of various metrics with numbers.</dd>
    <dt><a href="https://mackerel.io/docs/entry/howto/dashboard/markdown">Markdown widgets</a></dt>
    <dd>Freely write content in Markdown format.</dd>
    <dt><a href="https://mackerel.io/docs/entry/howto/dashboard/alert-status">Alert Status widgets</a></dt>
    <dd>Display alert statuses for all the hosts belonging to a role.</dd>
</dl>

<h2 id="templates">Templates available for custom dashboard generation</h2>

### Three-tier architecture template

This is a dashboard of a three-tier system consisting of web servers, app servers, and database servers, which is typical of a web application.

Specify the roles of Web, App, and DB.
Hosts that belong to each role and post metrics for the plugin or cloud integration corresponding to that role are selected for the target.

The plugins and cloud integrations currently supported by each role are as follows:

- Web Role
  - AWS Integration - API Gateway
  - mackerel-plugin-accesslog
  - mackerel-plugin-apache2
  - mackerel-plugin-nginx
- App Role
  - mackerel-plugin-gostats
  - mackerel-plugin-jvm
  - mackerel-plugin-jmx-jolokia
- DB Role
  - mackerel-plugin-postgres
  - mackerel-plugin-mysql

## Precautions
- There is limit on the maximum number of custom dashboards that can exist per organization (100 for the Standard plan). Check the number of custom dashboards that you can create in your organization [here](https://mackerel.io/my?tab=plan).
- If your paid plan is downgraded to the free plan, any custom dashboards exceeding the limit will become unavailable but will not be deleted.
