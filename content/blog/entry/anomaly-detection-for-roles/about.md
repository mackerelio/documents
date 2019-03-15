---
Title: 'New feature・How to use Anomaly Detection for roles '
Date: 2019-03-08T10:36:25+09:00
URL: https://mackerel.io/blog/entry/anomaly-detection-for-roles/about
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio.hatenablog.mackerel.io/atom/entry/17680117126989848371
---

Hello. Mackerel Team Director id:daiksy:detail here.

The beta version of Mackerel’s new feature ‘Anomaly Detection for roles’ which uses machine learning is now being offered. You might have heard about the development of this feature at Meetup and other past events. 

[https://mackerel.io/docs/entry/howto/anomaly-detection-for-roles:embed:cite]

Anomaly detection differs slightly from the way monitoring has been used up until now. In this article, we’ll take a look at what anomaly detection is and how it can be used. 

### What is Anomaly Detection for roles?

‘Anomaly Detection for roles’ is a function that uses machine learning to detect abnormalities in the server without having to set special monitoring items for hosts within a role in Mackerel.

Up until now, a substantial amount of experience and know-how regarding server monitoring was needed to be able to configure monitors precisely. Let’s say you want an alert issued when the CPU load gets high, but it’s actually quite difficult to determine what percentage of CPU usage is considered high-load, or what thresholds should be set for which items when detecting for application abnormalities. In order to be able to make these kinds of decisions, operational experience and technical knowledge are needed. On top of this, the idiosyncrasies of applications change daily, and if left alone, monitor configurations can become obsolete, so regular maintenance is a must.

 ‘Anomaly Detection for roles’ can help with these types of monitoring complications.

With Mackerel, [it’s recommended that you organize your servers into roles](https://mackerel.io/docs/entry/howto/create-services-and-roles). The role being the role that server plays in a service. By appropriately setting roles, you can classify groups of servers with similar load trends such as "application servers" or "database servers". Mackerel's ‘Anomaly Detection for roles’ feature uses machine learning to learn a server's "normal state" from past trends of metrics over the entire role. Newly posted metrics are monitored against the learned results, and anything that is outside of the "normal state" is regarded as an anomaly, and an alert occurs. In other words, the ‘Anomaly Detection for roles’ feature detects server abnormalities without having to configure individual monitors.

### Role configuration is vital to improving detection precision

With "Anomaly Detection for roles", roles are specified as the monitoring target. Mackerel uses past system metrics from hosts that are registered in the specified role to learn trends. As previously mentioned, it is recommended that roles be categorized by the role a server plays in a service, such as application servers and database servers, because of this we can assume that a role contains servers with similar metric trends, and trends can be learned from the entire role. Consequently, if a role contains servers with significantly different trends, or those with extremely different specifications, accuracy will fall. For example, when "active" and "standby" servers coexist for a long period of time, servers with different trends get mixed together in the role.

Therefore, In order to increase the precision of Mackerel's Anomaly Detection, it is important to first properly categorize servers by roles.

### How to use Anomaly Detection for roles

‘Anomaly Detection for roles’ learns trends from past metrics. If newly posted metrics are determined to be outside of those trends, an alert will occur. This alert notification will also display the metrics that were determined to be abnormal. From this information, the user who receives the alert can estimate what kind of anomaly is occurring in the server. For example, the alert may show an increase in memory usage outside of the normal trend. 

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20190308/20190308102821.png)

However, even if the alert shows that the detected anomaly is based on memory usage metrics, there is no guarantee that the cause of the issue applies to memory. This point requires careful attention.

‘Anomaly Detection for roles’ performs a combination of learning and judgment for trends of system metrics of hosts configured in a role. When an issue occurs in a server, more often than not several metrics are affected by the primary cause of the issue. For example, when the amount of data written to a disk increases, the network's transfer load needed to send that data also increases at the same time, and as a result, this may also affect memory usage. Even if trends change in such a complex manner, only the one metric used as the basis of detection is recorded in alerts of ‘Anomaly Detection for roles’. So, you need to be able to see the role graph transversely when an alert occurs.

Due to the nature of this feature, it’s slightly difficult to define a typical troubleshooting response such as "restart this server when this monitoring alert occurs". We recommend using ‘Anomaly Detection for roles’ to quickly detect the rare case anomalies in auxiliary monitoring applications while also configuring monitors with thresholds based on your past operational experience. You might also consider an operation cycle where you add a threshold based monitor upon receiving an alert with anomaly detection.

### Current limitations with Anomaly Detection for roles

At the moment, ‘Anomaly Detection for roles’ is only supported for Linux environments with mackerel-agent installed. Windows and Integration environments are not currently supported.
