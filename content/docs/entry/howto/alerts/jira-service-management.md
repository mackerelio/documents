---
Title: Jira Service Management notifications
Date: 2025-09-30T15:45:53+09:00
URL: https://mackerel.io/docs/entry/howto/alerts/jira-service-management
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802888565263922766
---

By using Jira Service Management in cooperation with Mackerel, alert notifications from alerts that are raised by monitoring rules that have been set in Mackerel can be sent to Jira Service Management. More specifically, when an alert is raised in Mackerel, a new alert will be created by Jira Service Management. Additionally, when an alert is resolved in Mackerel, the alert which was created by Jira Service Management will be closed.

![Jira Service Management alert](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20250908/20250908154304.png)

## Adding Jira Service Management Integration
Integration settings can be configured in the [Jira Service Management form](https://mackerel.io/my/channels/-/create#jira-service-management).

![Jira Service Management form](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20250908/20250908140337.png)

For integration we are using the [Integration Events API](https://developer.atlassian.com/cloud/jira/service-desk-ops/rest/v1/api-group-integration-events/). Enter your API key generated according to [Set up an API integration | Jira Service Management Cloud | Atlassian Support](https://support.atlassian.com/jira-service-management-cloud/docs/set-up-an-api-integration/).
