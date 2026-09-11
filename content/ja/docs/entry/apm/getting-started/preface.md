---
Title: Mackerel APM導入ガイド ― OpenTelemetryではじめるアプリケーション可視化
Date: 2026-09-11T10:58:40+09:00
URL: https://mackerel.io/ja/docs/entry/apm/getting-started/preface
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/14945776032076292500
---

[:contents]

## はじめに

このガイドは、Mackerel APMの導入を検討している方、あるいはこれから検証を始めようとしている方に向けた実践的な手引きです。

「APMに興味はあるけれど、何から手をつければいいかわからない」「OpenTelemetryという名前は聞くが、自分たちの環境で使えるのか不安」といった声をよく耳にします。このガイドでは、そうした疑問や不安を1つ1つ解消しながら、最初の計装から本番導入の判断まで、段階を追って進められるようにしました。

## このガイドの構成

本ガイドは、大きく2つのパートで構成されています。

##### 第1部：導入の流れ

APMの基礎知識から始まり、実際にトレースデータを取得して分析し、本番導入を判断するまでの一連の流れを扱います。章の順番に沿って読み進めることを想定しています。

- [Mackerel APM の基礎理解](./mackerel-apm-basics)では、APMとは何か、Mackerel APMがどのような仕組みで動くかを解説します
- [ゼロコード計装による最短データ取得](./zero-code-instrumentation)では、アプリケーションコードを変更せずにトレースを取得する方法を紹介します
- [Mackerel OpenTelemetryコレクターによる送信設定](./otel-collector-setup)では、コレクターの設定をカスタマイズする方法を学びます
- [短期検証による可視化分析](./verification-analysis)では、取得したトレースデータをMackerel APMの画面で読み解きます
- [コスト・データ量の見極め](./cost-estimation)では、本番環境での利用料金を見積もる方法を紹介します
- [フィルタリングとサンプリングによるデータ量・コストの最適化](./filtering-and-sampling)では、データ量を適切にコントロールする手法を解説します
- [Mackerel APM 本番導入チェックリスト](./production-readiness-checklist)では、本番導入前に確認すべき項目をまとめています

##### 第2部：言語別セットアップガイド

お使いの言語・フレームワークに合わせたゼロコード計装の具体的な手順を掲載しています。第1部の内容と併せて、必要な章を参照してください。以下の7つの環境に対応しています。

* [Node.js](./nodejs-zero-code-instrumentation)
* [PHP](./php-zero-code-instrumentation)
* [Java（Java Agent）](./java-agent-instrumentation)
* [Spring Boot](./spring-boot-instrumentation)
* [Python](./python-zero-code-instrumentation)
* [.NET](./dotnet-zero-code-instrumentation)
* [Ruby on Rails](./rails-instrumentation)

## 想定する読者

- Mackerelをすでに利用していて、APM機能の導入を検討している方
- アプリケーションのパフォーマンス課題を抱えていて、可視化の手段を探している方
- OpenTelemetryに興味があり、まずは手を動かして試してみたい方

APMやOpenTelemetryの事前知識は必要ありません。

## このガイドの使い方

まずは第1部を順番に読み進めてください。各章は前の章の内容を踏まえて構成しているため、途中を飛ばさずに進めることをおすすめします。

第2部の言語別ガイドは、お使いの環境に該当する章だけを参照すれば十分です。第1部の[ゼロコード計装による最短データ取得](./zero-code-instrumentation)で全体の流れを把握してから、具体的な手順を各言語のガイドで確認する、という読み方が効率的です。

また、「こういうことをしたいけれど、どの章を見ればよいかわからない」というときは、巻末の[逆引きリファレンス](./reverse-index)をご活用ください。目的からセクションを引くことができます。

<nav>
<ul style="display:flex;justify-content:space-between;padding:0;margin:0">
<li style="list-style:none;margin-left:auto">次の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/mackerel-apm-basics" rel="next">Mackerel APM の基礎理解</a></li>
</ul>
</nav>
