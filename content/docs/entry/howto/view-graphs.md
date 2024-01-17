---
Title: Using graphs
Date: 2014-11-10T15:04:47+09:00
URL: https://mackerel.io/docs/entry/howto/view-graphs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450073111319
---

[:contents]

<h2 id="view-graphs">How to View Graphs</h2>

Graphs are divided into two sections. The upper section is for viewing data in close detail while the lower section displays a more broad, wide-ranged view. Inside the graph, metrics are displayed as color coded lines.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150805/20150805181446.png)

By mousing over any point on a graphed line, you can see detailed information about a metric at any particular moment. 

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150805/20150805181428.png)

<h2 id="period">Change the display period of a graph</h2>

The lower portion of the graph has a slider which can be used to quickly track forward and backward as well as zoom in and out.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150805/20150805182732.gif)

Displayed at the top of the page are the time span control buttons. Using these controls you can change the time interval graphs will display.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906103033.png" alt="f:id:mackerelio:20220906103033p:plain" title="f:id:mackerelio:20220906103033p:plain" class="hatena-fotolife" itemprop="image" width="500"></span></p>

You can specify the time period with the clock icon.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906182657.png" alt="f:id:mackerelio:20220906182657p:plain" title="f:id:mackerelio:20220906182657p:plain" class="hatena-fotolife" itemprop="image" width="300"></span></p>

<h2 id="select-metrics">Select a metric to display on the graph</h2>

Click on a metric name shown beneath the graph to show or hide that metric.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906112652.png" alt="f:id:mackerelio:20220906112652p:plain" title="f:id:mackerelio:20220906112652p:plain" class="hatena-fotolife" itemprop="image" width="500"></span></p>

<h2 id="simplify">Simplified Display</h2>

Any role graph from the Services detail screen with a large number of hosts (more than 30) is simplified. Stacked graphs display the sum and line graphs display the maximum, minimum, and average. By clicking "show all hosts" located at the bottom of the graph, a detailed version of the simplified graph can be seen. 

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160304/20160304142120.png)

In the opened up graph detail screen, graphs with more than 200 hosts are always simplified, but can be switched from the "simplify graph" checkbox.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906185144.png" alt="f:id:mackerelio:20220906185144p:plain" title="f:id:mackerelio:20220906185144p:plain" class="hatena-fotolife" itemprop="image" width="500"></span></p>

<h2 id="graph-annotations">Creating graph annotations</h2>
Leave arbitrary information on graphs. You can do this by dragging over a section of the graph, specifying the period to be annotated, then entering a title and description.

Graph annotations can be created for service metric graphs and role graphs. Leaving annotations can be really useful regarding the deployment/release of applications performed on a per-service and per-role basis.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20170125/20170125184543.png)

<h2 id="full-screen">Make a graph full-screen</h2>

Clicking on the Full Screen button in the upper right corner of the graph will display that graph in a new window or tab in full screen mode.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906183528.png" alt="f:id:mackerelio:20220906183528p:plain" title="f:id:mackerelio:20220906183528p:plain" class="hatena-fotolife" itemprop="image" width="300"></span></p>

<h2 id="post-to-channel">Posting graphs to notification channels</h2>

Graph URLs can be posted to notification channels directly from Mackerel. From the graph menu, click the camera icon, select a channel to send the graph URL to, and then press “Post”. By doing this, the URL will be posted collectively with the graph information.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906183654.png" alt="f:id:mackerelio:20220906183654p:plain" title="f:id:mackerelio:20220906183654p:plain" class="hatena-fotolife" itemprop="image" width="300"></span></p>

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20161017/20161017170856.png)

If you select a metric to display ([Select a metric to display on the graph](https://mackerel.io/docs/entry/howto/view-graphs#select-metrics)), it will share a graph showing the status of that metric. This does not yet support embedded graphs like alert graphs, dashboards, etc., and preview graphs for monitoring with expressions are not currently supported.

To use this feature, you need to set a <a href="https://mackerel.io/my/channels/-/create" target="_blank">notification channel</a> in advance. The following notification channels are currently supported for submissions.

* Slack
* LINE
* Chatwork
* Typetalk
* Microsoft Teams

<h2 id="graph-share">Sharing graphs</h2>

Graphs can be shared with others in a variety of ways. Display the graph sharing menu by clicking the share button located on each graph.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906183741.png" alt="f:id:mackerelio:20220906183741p:plain" title="f:id:mackerelio:20220906183741p:plain" class="hatena-fotolife" itemprop="image" width="300"></span></p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906185547.png" alt="f:id:mackerelio:20220906185547p:plain" title="f:id:mackerelio:20180726163218p:plain" class="hatena-fotolife" itemprop="image" width="500"></span></p>

<h3 id="download-image">Downloading images</h2>
Graph images can be downloaded by clicking the `download` button. The display range of the graph image is the same as the range displayed on the console.

<h3 id="share-url">Sharing URLs</h2>
You can generate a URL to display a graph. Graphs can be shared using these URLs. The display range of a shared graph can be made to match the range displayed on the console by checking the "Current Display Range" box. Only accounts that belong to that organization can view the graph with this URL.

<h3 id="embed-graph">Select the embedded format and share</h2>
Get information for sharing graphs in embedded formats for HTML or markdown documents. The following formats can be selected.

- iframe
- Image
- SVG
- Markdown

Only accounts that belong to that organization can view embedded graphs. The display range of the shared graph can be made to match the range displayed on the console by checking the "Current Display Range" box.

<h3 id="publish-graph">Publishing graphs</h2>
Graphs can be made public by clicking the "Create URL" button.**Anyone with access to a public URL will be able to see the graph, so please be careful.**

You can check on your shared graphs or stop sharing from the [Shared Graphs](https://mackerel.io/my?tab=sharedGraphs) tab.

<h2 id="graph-definitions">Change the details displayed for a graph</h2>

Click on the gear icon in the upper right corner of a custom metric graph to open the Graph Definition screen. This screen allows you to change what is displayed for your graphs.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906183837.png" alt="f:id:mackerelio:20220906183837p:plain" title="f:id:mackerelio:20220906183837p:plain" class="hatena-fotolife" itemprop="image" width="300"></span></p>

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906184020.png" alt="f:id:mackerelio:20220906184020p:plain" title="f:id:mackerelio:20220906184020p:plain" class="hatena-fotolife" itemprop="image" width="500"></span></p>

You can hide graphs with the "Delete this graph" button. The graph will reappear when a new custom metric for that graph is posted.

<h2 id="delete-graph">Delete graph definitions</h2>

You can delete unneeded graph definitions from the [Graph Definition List](https://mackerel.io/my/graph-defs) screen. The Graph Definition List screen can be accessed from "Settings" on the right side of the Custom Metrics heading on the Host Details screen.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20220906/20220906184205.png" alt="f:id:mackerelio:20220906184205p:plain" title="f:id:mackerelio:20220906184205p:plain" class="hatena-fotolife" itemprop="image" width="500"></span></p>

Note that graph definitions are settings owned by the organization. Deleting them will affect all hosts.
