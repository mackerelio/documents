---
Title: Tracing - FAQ
Date: 2025-07-11T16:13:55+09:00
URL: https://mackerel.io/docs/entry/tracing/faq
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398507883564
---

## Is the infrastructure secure?

We use AWS and separate public and private networks using Amazon VPC.

## Are security patches applied?

We regularly check for updates, including automatic checks, and apply them as necessary.

## Are storage and databases encrypted?

Yes, they are encrypted.

## Where is the data stored?

All customer data is stored within Japan.

## How long is the data retained?

Data is retained for 14 days and then deleted.

## What happens if the number of span submissions exceeds the plan limit?

If the limit is exceeded, additional spans will be rejected. In such cases, a 429 error will be returned to the sender.
