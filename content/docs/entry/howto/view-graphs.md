---
Title: Using graphs
Date: 2014-11-10T15:04:47+09:00
URL: https://mackerel.io/docs/entry/howto/view-graphs
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450073111319
---

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150805/20150805181446.png)

Graphs are divided into two sections. The upper section is for viewing data in close detail while the lower section displays a more broad, wide-ranged view. Inside the graph, metrics are displayed as color coded lines.

The lower portion of the graph has a slider which can be used to quickly track forward and backward as well as zoom in and out.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150805/20150805182732.gif)

Displayed at the top of the page are the time span control buttons. Using these controls you can change the time interval graphs will display.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150805/20150805181425.png)

By mousing over any point on a graphed line, you can see detailed information about a metric at any particular moment. 

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150805/20150805181428.png)

<h2 id="simplify">Simplified Display</h2>

Any role graph from the Services detail screen with a large number of hosts (more than 30) is simplified. Stacked graphs display the sum and line graphs display the maximum, minimum, and average. By clicking "show all hosts" located at the bottom of the graph, a detailed version of the simplified graph can be seen. 

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160304/20160304142120.png)

In the opened up graph detail screen, graphs with more than 200 hosts are always simplified, but can be switched from the "simplify graph" checkbox.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20160304/20160304142123.png)

<h2 id="graph-annotations">Creating graph annotations</h2>
Leave arbitrary information on graphs. You can do this by dragging over a section of the graph, specifying the period to be annotated, then entering a title and description.

Graph annotations can be created for service metric graphs and role graphs. Leaving annotations can be really useful regarding the deployment/release of applications performed on a per-service and per-role basis.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20170125/20170125184543.png)

<h2 id="post-to-channel">Posting graphs to notification channels</h2>

Graph URLs can be posted to notification channels directly from Mackerel. From the graph menu, click the camera icon, select a channel to send the graph URL to, and then press “Post”. By doing this, the URL will be posted collectively with the graph information.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20161017/20161017170855.png)


![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20161017/20161017170856.png)

Each notification channel can be configured from the [Channel Settings](https://mackerel.io/my/channels?new).

This feature is not yet supported for alert graphs, embedded graphs in Dashboards etc., and preview graphs of expression monitoring. The notification channels that are currently supported are [Slack](https://mackerel.io/docs/entry/howto/alerts/slack), [Hipchat](https://mackerel.io/docs/entry/howto/alerts/hipchat), [Typetalk](https://mackerel.io/docs/entry/howto/alerts/typetalk), [LINE](https://mackerel.io/docs/entry/howto/alerts/line), [Yammer](https://mackerel.io/docs/entry/howto/alerts/yammer) and [Microsoft Teams](https://mackerel.io/docs/entry/howto/alerts/microsoft-teams).

<h2 id="graph-share">Sharing graphs</h2>

Graphs can be shared with others in a variety of ways.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20180726/20180726163218.png" alt="f:id:mackerelio:20180726163218p:plain" title="f:id:mackerelio:20180726163218p:plain" class="hatena-fotolife" itemprop="image" width="500"></span></p>

Display the graph sharing menu by clicking the share button located on each graph.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20180726/20180726163225.png" alt="f:id:mackerelio:20180726163225p:plain" title="f:id:mackerelio:20180726163225p:plain" class="hatena-fotolife" itemprop="image"></span></p>

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

You can check on your shared graphs or stop sharing from the [Shared Graphs tab](https://mackerel.io/my?tab=sharedGraphs).

-

If you have any questions please contact our support team at support@mackerel.io

<!-- share: https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150805/20150805181426.png-->
<!-- fullscreen: https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150805/20150805181427.png-->
<!-- arrange: https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20150805/20150805181424.png-->
