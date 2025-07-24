---
Title: Tracing - Investigate database performance
Date: 2025-07-11T14:58:45+09:00
URL: https://mackerel.io/docs/entry/tracing/features/db-performance
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398507810604
---

In the Database tab of the APM service screen, you can view a list of database queries included in the application trace spans.

By utilizing this screen, you can easily identify slow queries that are causing performance degradation and queries that are frequently called and need to be improved.

<figure class="figure-image figure-image-fotolife" title="Database Tab">[f:id:mackerelio:20250724141945p:plain]<figcaption>Database Tab</figcaption></figure>


## Viewing the query list

The Database Performance screen displays a list of queries stored as values of the `db.statement` attribute or the `db.query.text` attribute included in the trace spans (the value of the `db.query.text` attribute takes precedence). If neither attribute is included in the trace spans, the queries are not displayed.

The following information is displayed for each query row. You can switch between ascending and descending order by clicking the column header.

| Column            | Description                                                     |
|-------------------|-----------------------------------------------------------------|
| Query             | Executed query content                                          |
| Total             | Total execution time of the query                               |
| Average           | Average execution time of the query                             |
| Approx P95 | 95th percentile value of the query execution time (approximate) |
| Execution Count   | Total number of times the query was executed                    |
| Trace             | Navigates to the list screen of traces containing this query    |
