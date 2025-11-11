---
Title: Mackerel API ドキュメント（v0）
Date: 2016-02-12T19:22:41+09:00
URL: https://mackerel.io/ja/api-docs/entry/index
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api-jp.hatenablog.mackerel.io/atom/entry/6653586347157001270
---

<div id="api-documents-entry">

<div class="hatena-module-search-box">
  <form class="search-form" role="search" action="https://mackerel.io/ja/api-docs/search" method="get">
    <input type="text" name="q" class="search-module-input" value="" placeholder="APIドキュメント内検索" required="">
    <input type="submit" value="検索" class="search-module-button">
  </form>
</div>

<div id="api-summary">
  <h2 id="summary">概要</h2>
  <ul>
    <li>ベースURLは <code>https://api.mackerelio.com/</code>です。(Mackerel for KCPS利用の場合は<code>https://kcps-mackerel.io</code>)</li>
    <li>POST/PUT時の入力および成功時の出力は JSON (<code>application/json</code>)です。</li>
    <li>RFCに準拠したJSONを指定してください。</li>
    <li><b><code>X-Api-Key</code> ヘッダ（必須）: 対象とするホストやサービスが所属するオーガニゼーションのAPIキーです。</b>
      <ul>
        <li>APIキーは<a href="https://mackerel.io/my?tab=apikeys">ダッシュボードのAPIキータブ</a>で確認できます。</li>
        <li>APIキーの権限は Read権限と Write権限を設定できます。
          <ul>
            <li>Read権限のみを持つAPIキーでは、Write権限が必要なAPIを使用できません。</li>
          </ul>
        </li>
      </ul>
    </li>
  </ul>
</div>

<div class="index-rows">

  <h2>目次</h2>
  <div class="index-row">
    <h3><a href="entry/services">サービス</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/services#list">
          <p>サービスの一覧</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/services</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/services#create">
          <p>サービスの登録</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/services</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/services#delete">
          <p>サービスの削除</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/services#rolelist">
          <p>ロールの一覧</p>
          <p class="type-get">
              <code>GET</code>
              <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/services#rolecreate">
          <p>ロールの登録</p>
          <p class="type-post">
              <code>POST</code>
              <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/services#roledelete">
          <p>ロールの削除</p>
          <p class="type-delete">
              <code>DELETE</code>
              <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/services#metric-names">
          <p>メトリック名の一覧</p>
          <p class="type-get">
              <code>GET</code>
              <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metric-names</code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/hosts">ホスト</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/hosts#create">
          <p>ホスト情報の登録</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/hosts</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#get">
          <p>ホスト情報の取得</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#get-by-custom-identifier">
          <p>customIdentifier を指定したホスト情報の取得</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/hosts-by-custom-identifier/<em>&lt;customIdentifier&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#update-information">
          <p>ホスト情報の更新</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#update-status">
        <p>ホストのステータスの更新</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/status</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#bulk-update-statuses">
        <p>ホストの一括ステータス更新</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/hosts/bulk-update-statuses</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#update-roles">
          <p>ホストのロールの更新</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/role-fullnames</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#retire">
          <p>ホストの退役</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/retire</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#bulk-retire">
          <p>ホストの一括退役</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/hosts/bulk-retire</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#list">
          <p>ホストの一覧</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/hosts</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#metric-names">
          <p>メトリック名の一覧</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metric-names</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#monitored-statuses">
          <p>監視ステータスの一覧</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/monitored-statuses</code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/host-metrics">ホストメトリック</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/host-metrics#post">
          <p>メトリックの投稿</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/tsdb</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/host-metrics#get">
          <p>ホストのメトリックの値の取得</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metrics</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/host-metrics#get-latest">
          <p>メトリックの最新値の取得</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/tsdb/latest</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/host-metrics#post-graphdef">
          <p>グラフ定義の投稿</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/graph-defs/create</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/host-metrics#delete-graphdef">
          <p>グラフ定義の削除</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/graph-defs</code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/service-metrics">サービスメトリック</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/service-metrics#post">
          <p>サービスメトリックの投稿</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/tsdb</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/service-metrics#get">
          <p>サービスメトリックの値の取得</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metrics</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/service-metrics#delete-graph-def">
          <p>サービスメトリックのグラフ定義の削除</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/graph-defs/<em>&lt;graphName&gt;</em></code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/check-monitoring">チェック監視</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/check-monitoring#post">
          <p>チェック監視結果の投稿</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/monitoring/checks/report</code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/metadata">メタデータ</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/metadata#get">
          <p>ホストメタデータの取得</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#hostput">
          <p>ホストメタデータの登録・更新</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#hostdelete">
          <p>ホストメタデータの削除</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#hostlist">
          <p>ホストメタデータの一覧</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metadata</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#serviceget">
          <p>サービスメタデータの取得</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#serviceput">
          <p>サービスメタデータの登録・更新</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#servicedelete">
          <p>サービスメタデータの削除</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#servicelist">
          <p>サービスメタデータの一覧</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metadata</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#roleget">
          <p>ロールメタデータの取得</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#roleput">
          <p>ロールメタデータの登録・更新</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#roledelete">
          <p>ロールメタデータの削除</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#rolelist">
          <p>ロールメタデータの一覧</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em>/metadata</code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/monitors">監視ルール</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/monitors#create">
          <p>監視ルールの登録</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/monitors</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/monitors#list">
          <p>監視ルールの一覧</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/monitors</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/monitors#get">
          <p>監視ルールの取得</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/monitors/<em>&lt;monitorId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/monitors#update">
          <p>監視ルールの更新</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/monitors/<em>&lt;monitorId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/monitors#delete">
          <p>監視ルールの削除</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/monitors/<em>&lt;monitorId&gt;</em></code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/downtimes">ダウンタイム</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/downtimes#create">
          <p>ダウンタイムの登録</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/downtimes</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/downtimes#list">
          <p>ダウンタイムの一覧</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/downtimes</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/downtimes#update">
          <p>ダウンタイムの更新</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/downtimes/<em>&lt;downtimeId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/downtimes#delete">
          <p>ダウンタイムの削除</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/downtimes/<em>&lt;downtimeId&gt;</em></code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/channels">通知チャンネル</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/channels#get">
          <p>通知チャンネルの一覧取得</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/channels</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/channels#create">
          <p>通知チャンネルの登録</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/channels</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/channels#delete">
          <p>通知チャンネルの削除</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/channels/<em>&lt;channelId&gt;</em></code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/notification-groups">通知グループ</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/notification-groups#create">
          <p>通知グループの登録</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/notification-groups</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/notification-groups#get">
          <p>通知グループの一覧取得</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/notification-groups</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/notification-groups#update">
          <p>通知グループの更新</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/notification-groups/<em>&lt;notificationGroupId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/notification-groups#delete">
          <p>通知グループの削除</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/notification-groups/<em>&lt;notificationGroupId&gt;</em></code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/alerts">アラート</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/alerts#list">
          <p>アラートの一覧</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/alerts</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/alerts#get">
          <p>アラートの取得</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/alerts/<em>&lt;alertId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/alerts#update">
          <p>アラート更新</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/alerts/<em>&lt;alertId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/alerts#close">
          <p>アラートを閉じる</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/alerts/<em>&lt;alertId&gt;</em>/close</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/alerts#logs">
        <p>アラートログの取得</p>
        <p class="type-get">
          <code>GET</code>
          <code>/api/v0/alerts/<em>&lt;alertId&gt;</em>/logs</code>
        </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/alert-group-settings">アラートグループ設定</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/alert-group-settings#list">
          <p>アラートグループ設定の一覧</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/alert-group-settings</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/alert-group-settings#create">
          <p>アラートグループ設定の作成</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/alert-group-settings</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/alert-group-settings#get">
          <p>アラートグループ設定の取得</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/alert-group-settings/<em>&lt;alertGroupSettingId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/alert-group-settings#update">
          <p>アラートグループ設定の更新</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/alert-group-settings/<em>&lt;alertGroupSettingId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/alert-group-settings#delete">
          <p>アラートグループ設定の削除</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/alert-group-settings/<em>&lt;alertGroupSettingId&gt;</em></code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/dashboards">ダッシュボード</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/dashboards#create">
          <p>ダッシュボードの作成</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/dashboards</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/dashboards#get">
          <p>ダッシュボードの取得</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/dashboards/<em>&lt;dashboardId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/dashboards#update">
          <p>ダッシュボードの更新</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/dashboards/<em>&lt;dashboardId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/dashboards#delete">
          <p>ダッシュボードの削除</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/dashboards/<em>&lt;dashboardId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/dashboards#list">
          <p>ダッシュボードの一覧</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/dashboards</code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/graph-annotations">グラフアノテーション</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/graph-annotations#create">
          <p>グラフアノテーションの作成</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/graph-annotations</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/graph-annotations#get">
          <p>グラフアノテーションの取得</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/graph-annotations</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/graph-annotations#update">
          <p>グラフアノテーションの更新</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/graph-annotations/<em>&lt;annotationId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/graph-annotations#delete">
          <p>グラフアノテーションの削除</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/graph-annotations/<em>&lt;annotationId&gt;</em></code>
          </p>
        </a>
      </div>
    </div>
  </div>


  <div class="index-row">
    <h3><a href="entry/users">ユーザー</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/users#list">
          <p>オーガニゼーションメンバーに所属するユーザーの一覧</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/users</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/users#delete">
          <p>オーガニゼーションメンバーに所属するユーザーの削除</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/users/<em>&lt;userId&gt;</em></code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/invitations">招待</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/invitations#list">
          <p>招待の一覧</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/invitations</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/invitations#create">
          <p>招待の作成</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/invitations</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/invitations#revoke">
          <p>招待の取り消し</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/invitations/revoke</code>
          </p>
        </a>
      </div>
    </div>
  </div>


  <div class="index-row">
    <h3><a href="entry/organizations">オーガニゼーション</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/organizations#get">
          <p>オーガニゼーションの情報を取得</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/org</code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/aws-integration">AWSインテグレーション</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/aws-integration#list">
          <p>AWSインテグレーション設定の一覧</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/aws-integrations</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/aws-integration#get">
          <p>AWSインテグレーション設定の取得</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/aws-integrations/<em>&lt;awsIntegrationId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/aws-integration#create">
          <p>AWSインテグレーション設定の登録</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/aws-integrations</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/aws-integration#update">
          <p>AWSインテグレーション設定の更新</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/aws-integrations/<em>&lt;awsIntegrationId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/aws-integration#delete">
          <p>AWSインテグレーション設定の削除</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/aws-integrations/<em>&lt;awsIntegrationId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/aws-integration#generate-external-id">
          <p>AWSインテグレーション外部IDの生成</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/aws-integrations-external-id</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/aws-integration#excludable-metrics">
          <p>AWSインテグレーションの除外可能なメトリック名一覧</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/aws-integrations-excludable-metrics</code>
          </p>
        </a>
      </div>
    </div>
  </div>
  <div class="index-row">
    <h3><a href="entry/traces">トレース</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/traces#get">
          <p>トレースの取得</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/traces/<em>&lt;traceId&gt;</em></code>
          </p>
        </a>
      </div>
    </div>
  </div>
</div>

<h2 id="data-types">データの種類</h2>
<ul>
  <li>ホスト ID <code><em>&lt;hostId&gt;</em></code>
    <ul>
      <li>ホスト登録時にMackerelから与えられる文字列。エージェントが稼動しているホストを一意に識別する。</li>
    </ul>
  </li>
  <li>サービス名 <code><em>&lt;serviceName&gt;</em></code>
    <ul>
      <li>Mackerelに登録したサービス名</li>
    </ul>
  </li>
</ul>

</div>

<div id="contact-human">
    <p id="human-image"><img src="/assets/images/blog/support.png" alt=""></p>
    <h4>なにかお困りですか？</h4>
    <p id="contact-human-info">このヘルプサイトの内容だけでは解決できない問題を抱えている場合はMackerelサポートチームへご連絡ください。</p>
    <p id="contact-human-button"><a href="https://support.mackerel.io/hc/ja/requests/new" class="ui-button button-blue">サポートチームへ連絡</a></p>
    <p id="contact-human-link"><a href="https://support.mackerel.io/hc/ja">よくある質問を見る</a></p>
</div>
<div id="about-container">
    <h4 id="about"><span>このページについて/about</span></h4>
    <p>このページにはMackerel &lt;<a href="https://mackerel.io">https://mackerel.io</a>&gt; 及び、KDDIクラウドプラットフォームサービス（略称：KCPS）を対象に構築された &lt;<a href="https://kcps-mackerel.io">https://kcps-mackerel.io</a>&gt; に関するAPIドキュメントを記載しています。</p>
</div>
