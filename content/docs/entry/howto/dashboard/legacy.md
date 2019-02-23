---
Title: Using Legacy Custom Dashboards
Date: 2018-11-12T20:42:34+09:00
URL: https://mackerel.io/docs/entry/howto/dashboard/legacy
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10257846132668666632
---

# Legacy Custom Dashboards

This page explains Custom Dashboards (Legacy Custom Dashboards) prior to the feature's renovation as of November 2018. For the new Custom Dashboards feature, refer to [Using Custom Dashboards](https://mackerel.io/docs/entry/howto/dashboard).

With this feature user-customized dashboards can be added to the standard dashboard view in Mackerel.

Legacy Custom Dashboards can be edited in Markdown format.


## Creating a Legacy Custom Dashboard
Clicking on Dashboards in the sidebar will now bring up the Dashboards menu.

The “New Legacy Custom Dashboard” button is located in the upper-right hand corner.

[f:id:mackerelio:20181113153625p:plain]

This will bring up the Create New Custom Dashboard screen.

Enter a name for the new Custom Dashboard in the form.


Describe the new Custom Dashboard’s structure in Markdown format in the lower part of the form. Markdown can be edited under the Edit tab, and a preview of the dashboard can be viewed under the Preview tab. Once editing is complete, click the Create button to finish.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20151208/20151208123425.png)

## Inserting graphs in Custom Dashboards
Markdown code can be obtained from the share menu which can be accessed via the share button located in the upper-right hand corner of graphs.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20180523/20180523151851.png)

Copy the code from the text box and paste it in the Custom Dashboard edit screen. After saving, the graph will be displayed. The embedded use iframe is also available in Custom Dashboards.

For more information about using graphs please refer to our [help page](https://mackerel.io/docs/entry/howto/view-graphs).


## Custom Dashboard Sample Code
In the edit Custom Dashboard screen, copy and paste the following Markdown into the form and click Preview.


```

|graph1|graph2 |
|:-----------|:------|
|copy and paste Markdown code from graph sharing menu 1|copy and paste Markdown code from graph sharing menu 2|

```

Select the graph you would like to display, obtain the code and paste it in the parts labeled “copy and paste Markdown code…”.

Below is a sample of a Custom Dashboard.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20180523/20180523134119.png)

## Use with regard to plans
This feature can only be used with a paid plan or during the trial period.


## Precautions
- HTML tags are invalid and cannot be used in the editing screen.
- There is limit on the maximum number of custom dashboards that can exist per organization (100 for the Standard plan). Check the number of custom dashboards that you can create in your organization [here](https://mackerel.io/my?tab=plan).
- If your paid plan is downgraded to the free plan, any custom dashboards that have been created will become unavailable but will not be deleted.

## Markdown Notation

- [Markdown](http://daringfireball.net/projects/markdown/) notation can be used.
- Some extended notations of [GitHub Flavored Markdown](https://help.github.com/categories/writing-on-github/) and/or other Markdown variants, such as tables and code blocks with syntax highlighting, are also supported.
- HTML cannot be used.
