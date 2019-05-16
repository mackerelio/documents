---
Title: FAQ・Handling of host conversion when plan limits are exceeded
Date: 2017-12-15T15:51:10+09:00
URL: https://mackerel.io/docs/entry/faq/contracts/limit-exceeded-conversion
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812326837426
---

In addition to the previous section, here we will explain how host conversion will be done in response to plan overages of each type. All decimal points will be rounded up.

* Host metrics
  * `overage ÷ plan limit (with the Standard plan, 200 for Standard hosts, 30 for Micro hosts)`
  * All hosts exceeding the limit will factor into the calculation
  * Host metrics will be calculated using the equation below
    * `Standard metrics + custom metrics + check monitors`
  * Each metric will be calculated according to the type of host (standard host or micro host) it is linked to
* Service metrics
  * `overage ÷ plan limit (200 for Standard plans)`
  * Calculated as Standard host
* External monitors
  * `overage ÷ plan limit (20 for Standard plans)`
  * Calculated as Standard host


#### Examples using the Standard plan

Upper limits of the Standard plan include 200 for standard host metrics, 30 for micro host metrics, 200 for service metrics, and 20 for external monitoring items.

Below are 2 example possible usage cases.

### Usage Pattern A

| Pattern A | Configuration Count | Overage | Additional Standard host count due to overage | Additional Micro host count |
| --- | ---:| ---:| ---:| ---:|
| Standard host A metrics | 180 | 0 | 0 | - |
| Standard host B metrics | 180 | 0 | 0 | - |
| Micro host C metrics | 20 | 0 | - | 0 |
| Service metrics | 90 | 0 | 0 | - |
| External monitors | 19 | 0 | 0 | - |

In this case, there are no excess items, so the number of standard hosts will be counted as 2 for A and B, and 1 micro host for C.

### Usage pattern B

| Pattern B | Configuration Count | Overage | Additional standard host count due to overage | Additional micro host count |
| --- | ---:| ---:| ---:| ---:|
| Standard host A metrics | 401 | 201 | 2 | - |
| Standard host B metrics | 180 | 0 | 0 | - |
| Micro host C metrics | 50 | 20 | - | 1 |
| Service metrics | 240 | 40 | 1 | - |
| External monitors | 30 | 10 | 1 | - |

In this case, in addition to the 2 standard hosts for A and B, there are 4 standard host overages for a total of 6. The total number of microhosts comes to 2 due to the one additional micro host overage of C.

---

[< Return to FAQ](https://mackerel.io/docs/entry/faq)
