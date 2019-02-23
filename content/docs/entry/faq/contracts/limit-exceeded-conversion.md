---
Title: FAQ・Handling of host conversion when plan limits are exceeded
Date: 2017-12-15T15:51:10+09:00
URL: https://mackerel.io/docs/entry/faq/contracts/limit-exceeded-conversion
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812326837426
---

In addition to the previous section, here we will explain how host conversion will be done in response to plan overages of each type of item. All decimal points will be rounded up.

* Host metrics
  * `overage ÷ plan limit (200 for Standard plans)`
  * calculation will be done in regard to all hosts exceeding the limit
  * hosts' host metrics will be calculated with the equation below
    * `Standard metrics + custom metrics + check monitors`
* Service metrics
  * `overage ÷ plan limit (200 for Standard plans)`
* External monitors
  * `overage ÷ plan limit (20 for Standard plans)`


#### Examples using the Standard plan

The Standard plan has multiple limits, for host metrics it's 200, for service metrics 200, and for external monitors the limit is 20.

Below are 2 example possible use cases.

| Pattern A          | usage | overage | host conversion |
|--------------------|-------:|-------:|---------:|
| Host A metrics |    180 |      0 |        0 |
| Host B metrics |    180 |      0 |        0 |
| Host C metrics |    180 |      0 |        0 |
| Service metrics |     90 |      0 |        0 |
| External monitors           |     19 |      0 |        0 |

In this case there are three hosts, A, B, and C, and no overages, so total hosts will be counted as 3.

| Pattern B          | usage | overage | host conversion |
|--------------------|-------:|-------:|---------:|
| Host A metrics |    401 |    201 |        2 |
| Host B metrics |    180 |      0 |        0 |
| Host C metrics |    180 |      0 |        0 |
| Service metrics |    240 |    40 |        1 |
| External monitors          |     30 |     10 |        1 |

In this case we have 3 hosts, plus 4 hosts converted from overages, making a total of 7 hosts.

---

[< Return to FAQ](https://mackerel.io/docs/entry/faq)
