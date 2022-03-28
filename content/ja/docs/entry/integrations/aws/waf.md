---
Title: AWSインテグレーション - WAF
Date: 2020-03-06T10:00:00+09:00
URL: https://mackerel.io/ja/docs/entry/integrations/aws/waf
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/26006613555445686
CustomPath: integrations/aws/waf
---

MackerelはAWSインテグレーションにて<a href="https://aws.amazon.com/jp/waf/" target="_blank">AWS WAF</a>のメトリック取得や監視に対応しています。AWSインテグレーションで連携を行なった場合、課金対象として1ウェブアクセスコントロールリスト = 1マイクロホストと換算します。またそれに加えて、取得されるメトリックの数に応じて、1マイクロホストあたりのメトリック数上限の超過による請求が行われる場合があります。

AWSインテグレーションの設定方法や対応AWSサービス一覧についてはこちらのページをご確認ください。<br>
<a href="https://mackerel.io/ja/docs/entry/integrations/aws">AWSインテグレーション</a>

## 取得メトリック
AWSインテグレーションのWAF対応で取得できるメトリックは以下の通りです。`メトリック`の説明に関しては<a href="https://docs.aws.amazon.com/ja_jp/waf/latest/developerguide/monitoring-cloudwatch.html" target="_blank">AWSのヘルプ</a>をご確認ください。

最大で `6 × (ルール数) + 6<"ALL">` のメトリックが取得されます。

|グラフ名|メトリック|Mackerel上のメトリック名|単位|Statistics|
|:---|:---|:---|:---|:---|
|WebACL Requests|AllowedRequests<br>BlockedRequests<br>CountedRequests<br>PassedRequests<br>RequestsWithValidCaptchaToken<br>CaptchaRequests|waf.web_acl_requests.#.allowed<br>waf.web_acl_requests.#.blocked<br>waf.web_acl_requests.#.counted<br>waf.web_acl_requests.#.passed<br>waf.web_acl_requests.#.valid_captcha_token<br>waf.web_acl_requests.#.captcha|integer|Sum|

- "Mackerel上のメトリック名"の#には、ルール名が入ります。

<h2 id="notes">注意事項</h2>
- AWSインテグレーションのWAF対応ではタグによるホストの絞り込みに対応していません。
- AWSインテグレーションのWAF対応ではタグによるサービス・ロール割り当てに対応していません。
