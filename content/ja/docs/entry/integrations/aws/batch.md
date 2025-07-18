---
Title: AWSインテグレーション - AWS Batch
Date: 2020-01-10T10:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/batch
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613498446796
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/batch/" target="_blank">AWS Batch</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1リージョン = 1マイクロホストと換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのBatch対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/batch/latest/userguide/job_states.html" target="_blank">AWSのヘルプ</a>をご確認ください。

最大で `7 × (ジョブキュー数)` のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:---|:---|:---|:---|:---|
|Job Queue Status|-|batch.job_queue_status.#.succeeded<br>batch.job_queue_status.#.failed<br>batch.job_queue_status.#.running<br>batch.job_queue_status.#.starting<br>batch.job_queue_status.#.runnable<br>batch.job_queue_status.#.pending<br>batch.job_queue_status.#.submitted|integer|-|

- "Mackerel上のメトリック名"の#には、ジョブキュー名が入ります。

<h2 id="notes">注意事項</h2>
- AWSインテグレーションのBatch対応ではタグによるホストの絞り込みに対応していません。
- AWSインテグレーションのBatch対応ではタグによるサービス・ロール割り当てに対応していません。
