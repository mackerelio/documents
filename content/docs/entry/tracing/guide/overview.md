---
Title: Tracing - Mackerel's tracing feature overview
Date: 2025-07-11T11:58:30+09:00
URL: https://mackerel.io/docs/entry/tracing/guide/overview
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398507686233
---

This page provides the information you need to get started with Mackerel's tracing feature.

[:contents]

## What is Mackerel's tracing feature?

Mackerel's tracing feature allows you to discover and resolve issues that cause degraded user experience, such as errors and slow performance.

With Mackerel's tracing feature, you can do the following:

* Identify issues
  * Receive alerts when issues occur
  * Discover similar errors
  * Suggest error causes
* View traces
  * Distributed tracing
  * Compare traces
  * View trace distribution

For more details on each feature, please refer to the following:

[https://mackerel.io/docs/entry/tracing/guide/introduction:embed:cite]

[https://mackerel.io/docs/entry/tracing/features:embed:cite]

## Installation

Information about errors and traces is primarily collected using OpenTelemetry.

Therefore, the following minimum steps are required to use Mackerel's tracing feature:

1. Create a Mackerel account
2. Install OpenTelemetry-compatible tools on the server

** For more information about OpenTelemetry, please refer to the following page. **

[What is OpenTelemetry](https://mackerel.io/docs/entry/tracing/guide/what-is-opentelemetry)

## Installation Guide

You need to install the tools on your server or application using the steps described below.

### How to install OpenTelemetry

Using the OpenTelemetry SDK is referred to as instrumentation.

For specific instrumentation guides for each language, please refer to the following page.

[Installation and implementation](https://mackerel.io/docs/entry/tracing/installations)
