---
Title: トレース - Mackerelトレーシング機能概要
Date: 2025-03-13T15:56:19+09:00
URL: https://mackerel.io/ja/docs/entry/tracing/guide/overview
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/6802418398333958374
---

このページではMackerelトレーシング機能を使い始めるために必要な情報を紹介します。

[:contents]

## Mackerelトレーシング機能とは

Mackerelトレーシング機能は、エラーや速度低下など、ユーザー体験の悪化原因を発見し、解決することができます。

Mackerelトレーシング機能を使用すると、以下が可能になります

* 問題の発見
  * 問題発生時のアラート
  * 類似エラーの発見
  * エラー原因のサジェスト
* トレースの確認
  * 分散トレーシング
  * トレース間の比較
  * トレース分布の確認

各機能の紹介は以下をご覧ください

[https://mackerel.io/ja/docs/entry/tracing/guide/introduction:embed:cite]

[https://mackerel.io/ja/docs/entry/tracing/features:embed:cite]

## インストール

エラーやトレースの情報は、主にOpenTelemetryを使用して取得しています。

そのため、Mackerelトレーシング機能を使用するためには最低限以下の作業が必要です。

1. Mackerelにアカウントを作成する
2. OpenTelemetryに対応したツールをサーバーにインストールする

** OpenTelemetryについては、以下のページを参照してください。 **

[OpenTelemetryとは](https://mackerel.io/ja/docs/entry/tracing/guide/what-is-opentelemetry)

## インストール方法

サーバーやアプリケーションに以下の方法でツールをインストールする必要があります

### OpenTelemetryのインストール方法

OpenTelemetryのSDKを使用することは計装 (instrumentation) といわれています。

各言語の具体的な計装方法は以下のページを参考にしてください。

[インストール・実装](https://mackerel.io/ja/docs/entry/tracing/installations)
