---
Title: FAQ・mackerel-agent と AWS / Azure インテグレーションを併用した場合の課金体系はどうなりますか？
Date: 2018-01-17T13:04:38+09:00
URL: https://mackerel.io/ja/docs/entry/faq/contracts/combined-integration
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8599973812338373103
---

mackerel-agent と AWS / Azure インテグレーションを併用した場合、その2つの機能から取得されるメトリックは一つのホストに集約されますので、スタンダードホストもしくはマイクロホスト1台分のご利用料金で利用できます。

- Amazon EC2 / Azure Virtual Machine インスタンスに mackerel-agent がインストールされ、インテグレーションによる連携も行われている場合: 1スタンダードホスト
    - インテグレーションによる登録ホストとの紐付けには、各プラットフォームが提供しているインスタンスメタデータAPIから取得できる情報を使用しています。
    - AWSの場合、EC2インスタンスメタデータサービスのバージョンには v1 と v2 がありますが、mackerel-agent は v1 のみ対応しています。
    - インスタンスメタデータサービス v1、あるいはインスタンスメタデータサービスそのものを無効にすると、Mackerelにはそれぞれ異なるスタンダードホストとして登録されてしまうため、ご注意ください。
- その他のサービスで、インテグレーションにより登録されたホストに対して、[custom_identifierを使用してプラグインによる監視を集約](https://mackerel.io/ja/docs/entry/integrations/aws#plugin-custom-identifier)している場合: 1マイクロホスト

ただし、ホストあたりのメトリック数の上限などについては変わりませんので、その点はご注意ください。メトリック数超過などに関する注意事項は、[プラン上限超過時のホスト台数換算について](https://mackerel.io/ja/docs/entry/faq/contracts/limit-exceeded-conversion) を参照してください。

---

[< FAQに戻る](https://mackerel.io/ja/docs/entry/faq)
