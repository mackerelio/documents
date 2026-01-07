---
Title: ヘルプ
Date: 2014-04-30T11:15:10+09:00
URL: https://mackerel.io/ja/docs/entry/index
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815722966530
---

<div id="help-top-entry">
    <div class="hatena-module-search-box">
        <form class="search-form" role="search" action="https://mackerel.io/ja/docs/search" method="get">
            <input type="text" name="q" class="search-module-input" value="" placeholder="ヘルプ内検索" required="">
            <input type="submit" value="検索" class="search-module-button">
        </form>
    </div>
    <ul id="internal-links">
        <li><a href="#first-steps">Mackerelをはじめる</a></li>
        <li><a href="#accounts">ユーザーアカウント管理</a></li>
        <li><a href="#apm">アプリケーション監視（APM）</a></li>
        <li><a href="#infra-hosts">インフラ・ホスト監視</a></li>
        <li><a href="#monitoring-notifications">監視とアラート通知</a></li>
        <li><a href="#advanced">進んだ使い方</a></li>
        <li><a href="#api">API仕様</a></li>
    </ul>
    <div id="docs-list">
        <section id="section-newbie">
            <div id="first-steps" class="fragment"></div>
            <h4>Mackerelをはじめる</h4>
            <ul>
                <li><a href="https://mackerel.io/ja/docs/entry/getting-started"><strong>Mackerelをはじめる</strong><span>はじめてMackerelを使う方に向けて、サービスの概要とユーザー登録の方法を解説します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/overview"><strong>概要</strong><span>Mackerelおよびエージェントの概説</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/glossary"><strong>用語集</strong><span>Mackerelで使用されている用語とその解説</span></a></li>
                <li><a href="https://support.mackerel.io/hc/ja"><strong>FAQ</strong><span>よくある質問</span></a></li>
            </ul>
        </section>
        <section id="section-accounts">
            <div id="accounts" class="fragment"></div>
            <h4>ユーザーアカウント管理</h4>
            <ul>
                <li><a href="https://mackerel.io/ja/docs/entry/spec/authority"><strong>ユーザー権限</strong><span>ユーザー権限の仕様について</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/invite-others"><strong>他のユーザーをMackerelに招待する</strong><span>他のユーザーをMackerelに招待し、共同でホストを管理します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/MFA"><strong>2段階認証を利用する</strong><span>2段階認証を設定してセキュリティを強化します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/enforcing-MFA"><strong>2段階認証の設定を強制する</strong><span>2段階認証を必須化してセキュリティを強化します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/advanced/saml-settings"><strong>SAMLを設定する</strong><span>MackerelとIDプロバイダーの連携設定について解説します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/advanced/saml-authentication"><strong>SAMLで認証する</strong><span>お使いのIDプロバイダーの資格情報を使用してMackerelにサインインする方法を解説します</span></a></li>
            </ul>
        </section>
        <section id="section-apm">
            <div id="apm" class="fragment"></div>
            <h4>アプリケーション監視（APM）</h4>
            <ul>
                <li><a href="https://mackerel.io/ja/docs/entry/tracing/guide"><strong>Mackerel トレーシング機能ガイド</strong><span>トレーシング機能目次</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/tracing/guide/introduction"><strong>はじめに</strong><span>トレーシングの主な機能を紹介します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/tracing/guide/overview"><strong>Mackerelトレーシング機能概要</strong><span>トレーシング機能を使い始めるために必要な情報を紹介します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/tracing/installations"><strong>インストール・実装</strong><span>アプリケーションへの計装方法を紹介します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/tracing/features"><strong>機能</strong><span>トレーシング機能ではエラーや速度低下など、ユーザー体験悪化の原因を発見し解決できます</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/tracing/integrations/slack"><strong>Slackに通知する</strong><span>トレーシング機能ではアラートを Slack に送信できます</span></a></li>
            </ul>
        </section>
        <section id="section-infra-hosts">
            <div id="infra-hosts" class="fragment"></div>
            <h4>インフラ・ホスト監視</h4>
            <ul>
                <li><a href="https://mackerel.io/ja/docs/entry/spec/about-host"><strong>「ホスト」とは</strong><span>Mackerelにおける「ホスト」について</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/install-agent"><strong>エージェントをインストールする</strong><span>mackerel-agent をホストにインストールして、計測をはじめます</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/view-graphs"><strong>グラフを利用する</strong><span>表示範囲の拡大・縮小・移動など</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/spec/about-service-role"><strong>「サービス」「ロール」とは</strong><span>Mackerelにおける「サービス」「ロール」について</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/create-services-and-roles"><strong>サービス/ロールを作成する</strong><span>サービスおよびロールを作成し、複数のホストを分類整理します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/assign-roles-to-hosts"><strong>ホストにロールを割り当てる</strong><span>作成したロールをホストに割り当てて分類整理します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/graphboard"><strong>グラフボードを利用する</strong><span>サービス画面でグラフボードを利用します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/advanced/custom-metrics"><strong>ホストのカスタムメトリックを投稿する</strong><span>mackerel-agentからMySQLなどのミドルウェアのメトリックを投稿します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/mackerel-agent-plugins"><strong>ミドルウェアのメトリック可視化に公式プラグイン集を使う</strong><span>公式プラグインで各種ミドルウェアに対応します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/spec/agent"><strong>mackerel-agent仕様</strong><span>mackerel-agentコマンドのオプションや設定について</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/spec/metrics"><strong>メトリック仕様</strong><span>グラフに描画されるメトリック名の仕様について</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/integrations/aws"><strong>AWSインテグレーション</strong><span>AWSクラウド製品をMackerelのホストとして管理、監視します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/integrations/azure"><strong>Azureインテグレーション</strong><span>Azureクラウド製品をMackerelのホストとして管理、監視します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/integrations/gcp"><strong>Google Cloudインテグレーション</strong><span>Google Cloudのクラウド製品をMackerelのホストとして管理、監視します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/host-retirement"><strong>登録されたホストを Mackerel の管理対象から外す</strong><span>監視が不要になったホストを、Mackerel の管理対象から外します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/auto-scaling"><strong>Auto Scaling環境で使う</strong><span>自動的に台数増減する環境に対応します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/advanced/docker"><strong>Dockerをモニタリングする</strong><span>mackerel-agentのDockerイメージを利用してDockerホストをモニタリングできます</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/container-agent"><strong>コンテナを監視する</strong><span>コンテナの監視にはmackerel-container-agentを利用します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/labeled-metrics/post-metrics"><strong>ラベル付きメトリックを Mackerel に投稿する</strong><span>OpenTelemetryに準拠したメトリックを投稿できます</span></a></li>
            </ul>
        </section>
        <section id="section-monitoring-notifications">
            <div id="monitoring-notifications" class="fragment"></div>
            <h4>監視とアラート通知</h4>
            <ul>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/alerts"><strong>監視・通知を設定する</strong><span>ホストの死活監視とメトリックの監視を設定します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/custom-checks"><strong>チェック監視項目を追加する</strong><span>Nagiosのようなチェック監視をおこなう</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/mackerel-check-plugins"><strong>チェック監視に公式チェックプラグイン集を使う</strong><span>公式プラグインでさまざまなチェックをおこなえます</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/check/log"><strong>ログ監視をおこなう</strong><span>ログファイルに特定のパターンがあったらアラートを発報します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/check/process"><strong>プロセス監視をおこなう</strong><span>プロセス数の監視をおこないます</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/mkr/wrap"><strong>cronなどのバッチジョブを監視する</strong><span>mkr wrapでcronなどのバッチジョブを監視できます</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/external-monitoring"><strong>URL外形監視をおこなう</strong><span>URL外形監視を設定します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/expression-monitoring"><strong>式による監視をおこなう</strong><span>式による監視を設定します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/anomaly-detection-for-roles"><strong>ロール内異常検知による監視をおこなう</strong><span>ロール内のホストを機械学習で監視します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/query-monitoring"><strong>クエリによる監視をおこなう</strong><span>PromQL クエリによりラベル付きメトリックを監視します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/alerts/email"><strong>メールにアラートを通知する</strong><span>アラートをメールで通知します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/alerts/slack"><strong>Slackにアラートを通知する</strong><span>アラートをSlackへ通知します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/alerts/chatwork"><strong>Chatworkに通知を通知する</strong><span>アラートをChatworkへ通知します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/alerts/twilio"><strong>Twilio にアラートを通知する</strong><span>アラートをTwilioへ通知します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/alerts/pagerduty"><strong>PagerDutyにアラートを通知する</strong><span>アラートをPagerDutyへ通知します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/alerts/OpsGenie"><strong>Opsgenieに通知を通知する</strong><span>アラートをOpsgenieへ通知します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/alerts/jira-service-management"><strong>Jira Service Managementにアラートを通知する</strong><span>アラートをJira Service Managementへ通知します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/alerts/microsoft-teams"><strong>Microsoft Teamsにアラートを通知する</strong><span>アラートをMicrosoft Teamsへ通知します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/alerts/webhook"><strong>Webhookにアラートを通知する</strong><span>アラートをWebhookへ通知します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/alerts/eventbridge"><strong>Amazon EventBridgeにアラートを通知する</strong><span>アラートをAmazon EventBridgeへ通知します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/downtimes"><strong>ダウンタイム</strong><span>定期的な監視の停止を設定します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/alert-groups"><strong>アラートグループでアラートをまとめる</strong><span>アラートをまとめるアラートグループを設定します</span></a></li>
            </ul>
        </section>
        <section id="section-advanced">
            <div id="advanced" class="fragment"></div>
            <h4>進んだ使い方</h4>
            <ul>
                <li><a href="https://mackerel.io/ja/docs/entry/advanced/advanced-graph"><strong>カスタマイズしたグラフを表示する</strong><span>関数を使って柔軟にカスタマイズしたグラフを表示します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/dashboard"><strong>カスタムダッシュボードを利用する</strong><span>カスタムダッシュボードを利用します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/labeled-metrics/query-graph"><strong>クエリグラフを利用する</strong><span>ラベル付きメトリックをクエリグラフで表示します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/advanced/cli"><strong>CLIツール mkr を使う</strong><span>コマンドラインツールを使って作業を自動化しましょう</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/advanced/install-plugin-by-mkr"><strong>mkr plugin installでプラグインをインストールする</strong><span>mkr を利用するとmackerelプラグインやcheckプラグインを簡単にインストールできます</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/advanced/go-mackerel-plugin"><strong>go-mackerel-pluginを利用してカスタムメトリックプラグインを作成する</strong><span>公式で提供しているライブラリを利用してメトリックプラグインを作成する方法を紹介します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/advanced/checkers"><strong>checkersを利用してチェックプラグインを作成する</strong><span>公式で提供しているライブラリを利用してチェックプラグインを作成する方法を紹介します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/advanced/make-plugin-corresponding-to-installer"><strong>mkr plugin installに対応したプラグインを作成する</strong><span>作成したプラグインをmkrのプラグインインストーラに対応する方法を紹介します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/advanced/monitors-github"><strong>監視ルールをGitHubで管理しよう</strong><span>mkr を利用してGitHubで監視ルールを管理する方法を紹介します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/howto/metadata"><strong>メタデータを利用する</strong><span>管理データを登録して活用します</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/advanced/experimental-features"><strong>実験的機能を利用する</strong><span>正式リリース前の機能を先行してお試しいただけます</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/advanced/cloudwatch-logs-aggregator"><strong>Amazon CloudWatch Logs のログを集計して Mackerel にメトリックを投稿する</strong><span>ログを活用してアプリケーションのメトリック監視を行えます</span></a></li>
            </ul>
        </section>
        <section id="section-api">
            <div id="api" class="fragment"></div>
            <h4>API仕様</h4>
            <ul>
                <li><a href="https://mackerel.io/ja/api-docs/"><strong>Mackerel API ドキュメント（v0）</strong><span>MackerelのAPIの仕様について</span></a></li>
                <li><a href="https://mackerel.io/ja/docs/entry/spec/metadata"><strong>Mackerelに関するメタデータAPI</strong><span>Mackerelに関する情報を提供するメタデータAPIについて</span></a></li>
            </ul>
        </section>
    </div>
    <div id="contact-human">
        <p id="human-image"><img src="/assets/images/blog/support.png" alt="" /></p>
        <h4>なにかお困りですか？</h4>
        <p id="contact-human-info">このヘルプサイトの内容だけでは解決できない問題を抱えている場合はMackerelサポートチームへご連絡ください。</p>
        <p id="contact-human-button"><a href="https://support.mackerel.io/hc/ja/requests/new" class="ui-button button-blue">サポートチームへ連絡</a></p>
        <p id="contact-human-link"><a href="https://support.mackerel.io/hc/ja">よくあるご質問を見る</a></p>
    </div>
    <div id="about-container">
        <h4 id="about"><span>このページについて/about</span></h4>
        <p>このページにはMackerel <<https://mackerel.io>> 及び、KDDIクラウドプラットフォームサービス（略称：KCPS）を対象に構築された <<https://kcps-mackerel.io>> に関するヘルプを記載しています。</p>
    </div>
</div>
