---
Title: Using Custom Dashboards
Date: 2015-07-13T17:32:20+09:00
URL: https://mackerel.io/docs/entry/howto/dashboard
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450101369423
---

# Custom Dashboards
With this feature, user-customized dashboards can be added to the standard dashboard view in Mackerel.

This page explains Custom Dashboards following the feature's renovation as of November 2018. For details regarding this feature before its renovation, refer to [Using Legacy Custom Dashboards](https://mackerel.io/docs/entry/howto/dashboard/legacy).

## Creating a custom dashboard
Click "Dashboards" located in left sidebar menu to change to the dashboard management screen.

Then click the "New Custom Dashboard" button.

[f:id:mackerelio:20181113153625p:plain]

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
</dl>

## Use regarding plans
This feature can only be used with a paid plan or during a trial period.

## Precautions
- There is limit on the maximum number of custom dashboards that can exist per organization (100 for the Standard plan). Check the number of custom dashboards that you can create in your organization [here](https://mackerel.io/my?tab=plan).
- If your paid plan is downgraded to the free plan, any custom dashboards that have been created will become unavailable but will not be deleted.
