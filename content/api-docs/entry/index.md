---
Title: Mackerel API Documents (v0)
Date: 2016-02-12T19:23:55+09:00
URL: https://mackerel.io/api-docs/entry/index
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-api.hatenablog.mackerel.io/atom/entry/6653586347157001366
---

<div id="api-documents-entry">

<div class="hatena-module-search-box">
  <form class="search-form" role="search" action="https://mackerel.io/api-docs/search" method="get">
    <input type="text" name="q" class="search-module-input" value="" placeholder="Search this API documents" required="">
    <input type="submit" value="検索" class="search-module-button">
  </form>
</div>

<div id="api-summary">
  <h2 id="summary">Summary</h2>
  <ul>
    <li>The base URL is <code>https://api.mackerelio.com/</code>. (<code>https://kcps-mackerel.io</code> for KCPS users) </li>
    <li>POST/PUT input and the success response should be in JSON (<code>application/json</code>). </li>
    <li>Please specify JSON that is valid against RFC. </li>
    <li><b><code>X-Api-Key</code> header (required): the API key of the organization to which targeted hosts and services belong.</b>
      <ul>
        <li>You can see your API keys in the <a href="https://mackerel.io/my?tab=apikeys">Dashboard</a>.</li>
        <li>Each API key's Read and Write permissions can be configured.
          <ul>
            <li>API keys that only have Read permission cannot be used for APIs that require Write permission.</li>
          </ul>
        </li>
      </ul>
    </li>
  </ul>
</div>

<div class="index-rows">
  
  <h2>Contents</h2>
  <div class="index-row">
    <h3><a href="entry/services">Services</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/services#list">
          <p>List Services</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/services</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/services#create">
          <p>Register Services</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/services</code>
          </p>
        </a>
      </div>      
      <div class="api">
        <a href="entry/services#delete">
          <p>Delete Services</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/services#rolelist">
          <p>List Roles</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/services#rolecreate">
          <p>Register Roles</p>
          <p class="type-post">
              <code>POST</code>
              <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/services#roledelete">
          <p>Delete Roles</p>
          <p class="type-delete">
              <code>DELETE</code>
              <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/services#metric-names">
          <p>List Metric Names</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metric-names</code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/hosts">Hosts</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/hosts#create">
          <p>Register Host Information</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/hosts</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#get">
          <p>Get Host Information</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#get-by-custom-identifier">
          <p>Get Host Information By Custom Identifier</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/hosts-by-custom-identifier/<em>&lt;customIdentifier&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#update-information">
          <p>Update Host Information</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#update-status">
          <p>Update Host Status</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/status</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#bulk-update-statuses">
          <p>Bulk Update Host Statuses</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/hosts/bulk-update-statuses</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#update-roles">
          <p>Update Host Roles</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/role-fullnames</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#retire">
          <p>Retire Hosts</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/retire</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#bulk-retire">
          <p>Bulk Retire Hosts</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/hosts/bulk-retire</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#list">
          <p>List Hosts</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/hosts</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#metric-names">
          <p>List Metric Names</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metric-names</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/hosts#monitored-statuses">
          <p>List Monitoring Statuses</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/monitored-statuses</code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/host-metrics">Host Metrics</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/host-metrics#post">
          <p>Post Metrics</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/tsdb</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/host-metrics#get">
          <p>Get Host Metrics</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metrics</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/host-metrics#get-latest">
        <p>Get Latest Metrics</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/tsdb/latest</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/host-metrics#post-graphdef">
          <p>Post Graph Definitions</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/graph-defs/create</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/host-metrics#delete-graphdef">
          <p>Delete Graph Definitions</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/graph-defs</code>
          </p>
        </a>
      </div>
    </div>
  </div>
  
  <div class="index-row">
    <h3><a href="entry/service-metrics">Service Metrics</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/service-metrics#post">
          <p>Post Service Metrics</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/tsdb</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/service-metrics#get">
        <p>Get Service Metrics</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metrics</code>
          </p>
        </a>
      </div>
    </div>
    <div class="api">
      <a href="entry/service-metrics#delete-graph-def">
        <p>Delete Service Metrics Graph Definition</p>
        <p class="type-delete">
          <code>DELETE</code>
          <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/graph-defs/<em>&lt;graphName&gt;</em></code>
        </p>
      </a>
    </div>
  </div>
  
  <div class="index-row">
    <h3><a href="entry/check-monitoring">Check Monitoring</a></h3>
    <div class="apis">  
      <div class="api">
        <a href="entry/check-monitoring#post">
          <p>Post Monitoring Check Reports</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/monitoring/checks/report</code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/metadata">Metadata</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/metadata#get">
          <p>Get Host Metadata</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#hostput">
          <p>Register/Update Host Metadata</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#hostdelete">
          <p>Delete Host Metadata</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#hostlist">
          <p>List Host Metadata</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/hosts/<em>&lt;hostId&gt;</em>/metadata</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#serviceget">
          <p>Get Service Metadata</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#serviceput">
          <p>Register/Update Service Metadata</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#servicedelete">
          <p>Delete Service Metadata</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#servicelist">
          <p>List Service Metadata</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/metadata</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#roleget">
          <p>Get Role Metadata</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#roleput">
          <p>Register/Update Role Metadata</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#roledelete">
          <p>Delete Role Metadata</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em>/metadata/<em>&lt;namespace&gt</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/metadata#rolelist">
          <p>List Role Metadata</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/services/<em>&lt;serviceName&gt;</em>/roles/<em>&lt;roleName&gt;</em>/metadata</code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/monitors">Monitors</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/monitors#create">
          <p>Register Monitor Configurations</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/monitors</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/monitors#list">
          <p>List Monitor Configurations</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/monitors</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/monitors#get">
          <p>Get Monitor Configurations</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/monitors/<em>&lt;monitorId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/monitors#update">
          <p>Update Monitor Configurations</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/monitors/<em>&lt;monitorId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/monitors#delete">
          <p>Delete Monitor Configurations</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/monitors/<em>&lt;monitorId&gt;</em></code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/downtimes">Downtime</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/downtimes#create">
          <p>Register Downtime</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/downtimes</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/downtimes#list">
          <p>List Downtime</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/downtimes</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/downtimes#update">
          <p>Update Downtime</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/downtimes/<em>&lt;downtimeId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/downtimes#delete">
          <p>Delete Downtime</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/downtimes/<em>&lt;downtimeId&gt;</em></code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/channels">Notification Channels</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/channels#get">
          <p>Get Notification Channels</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/channels</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/channels#create">
          <p>Register Notification Channels</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/channels</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/channels#delete">
          <p>Delete Notification Channels</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/channels/<em>&lt;channelId&gt;</em></code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/notification-groups">Notification Groups</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/notification-groups#create">
          <p>Register Notification Groups</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/notification-groups</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/notification-groups#get">
          <p>Get Notification Groups</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/notification-groups</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/notification-groups#update">
          <p>Update Notification Groups</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/notification-groups/<em>&lt;notificationGroupId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/notification-groups#delete">
          <p>Delete Notification Groups</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/notification-groups/<em>&lt;notificationGroupId&gt;</em></code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/alerts">Alerts</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/alerts#list">
          <p>List Alerts</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/alerts</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/alerts#get">
          <p>Get Alert</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/alerts/<em>&lt;alertId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/alerts#update">
          <p>Update Alert</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/alerts/<em>&lt;alertId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/alerts#close">
          <p>Close Alerts</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/alerts/<em>&lt;alertId&gt;</em>/close</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/alerts#logs">
        <p>Get Alert Logs</p>
        <p class="type-get">
          <code>GET</code>
          <code>/api/v0/alerts/<em>&lt;alertId&gt;</em>/logs</code>
        </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/alert-group-settings">Alert Group Settings</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/alert-group-settings#list">
          <p>List Alert Group Settings</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/alert-group-settings</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/alert-group-settings#create">
          <p>Register Alert Group Settings</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/alert-group-settings</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/alert-group-settings#get">
          <p>Get Alert Group Settings</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/alert-group-settings/<em>&lt;alertGroupSettingId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/alert-group-settings#update">
          <p>Update Alert Group Settings</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/alert-group-settings/<em>&lt;alertGroupSettingId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/alert-group-settings#delete">
          <p>Delete Alert Group Settings</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/alert-group-settings/<em>&lt;alertGroupSettingId&gt;</em></code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/dashboards">Dashboards</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/dashboards#create">
          <p>Create Dashboards</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/dashboards</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/dashboards#get">
          <p>Get Dashboards</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/dashboards/<em>&lt;dashboardId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/dashboards#update">
          <p>Update Dashboards</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/dashboards/<em>&lt;dashboardId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/dashboards#delete">
          <p>Delete Dashboards</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/dashboards/<em>&lt;dashboardId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/dashboards#list">
          <p>List Dashboards</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/dashboards</code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/graph-annotations">Graph Annotations</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/graph-annotations#create">
          <p>Create Graph Annotations</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/graph-annotations</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/graph-annotations#get">
          <p>Get Graph Annotations</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/graph-annotations</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/graph-annotations#update">
          <p>Update Graph Annotations</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/graph-annotations/<em>&lt;annotationId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/graph-annotations#delete">
          <p>Delete Graph Annotations</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/graph-annotations/<em>&lt;annotationId&gt;</em></code>
          </p>
        </a>
      </div>
    </div>
  </div>



  <div class="index-row">
    <h3><a href="entry/users">Users</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/users#list">
          <p>List Users that are Organization Members</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/users</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/users#delete">
          <p>Delete Users that are Organization Members</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/users/<em>&lt;userId&gt;</em></code>
          </p>
        </a>
      </div>
    </div>
  </div>
  
  <div class="index-row">
    <h3><a href="entry/invitations">Invitations</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/invitations#list">
          <p>List Invitations</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/invitations</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/invitations#create">
          <p>Create Invitations</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/invitations</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/invitations#revoke">
          <p>Cancel Invitations</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/invitations/revoke</code>
          </p>
        </a>
      </div>
    </div>
  </div>
 

  <div class="index-row">
    <h3><a href="entry/organizations">Organization</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/organizations#get">
          <p>Get Organization Information</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/org</code>
          </p>
        </a>
      </div>
    </div>
  </div>

  <div class="index-row">
    <h3><a href="entry/aws-integration">AWS Integration</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/aws-integration#list">
          <p>List AWS Integration Settings</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/aws-integrations</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/aws-integration#get">
          <p>Get AWS Integration Settings</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/aws-integrations/<em>&lt;awsIntegrationId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/aws-integration#create">
          <p>Register AWS Integration Settings</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/aws-integrations</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/aws-integration#update">
          <p>Update AWS Integration Settings</p>
          <p class="type-put">
            <code>PUT</code>
            <code>/api/v0/aws-integrations/<em>&lt;awsIntegrationId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/aws-integration#delete">
          <p>Delete AWS Integration Settings</p>
          <p class="type-delete">
            <code>DELETE</code>
            <code>/api/v0/aws-integrations/<em>&lt;awsIntegrationId&gt;</em></code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/aws-integration#generate-external-id">
          <p>Generate AWS Integration External ID</p>
          <p class="type-post">
            <code>POST</code>
            <code>/api/v0/aws-integrations-external-id</code>
          </p>
        </a>
      </div>
      <div class="api">
        <a href="entry/aws-integration#excludable-metrics">
          <p>List Excludable Metrics for AWS Integration</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/aws-integrations-excludable-metrics</code>
          </p>
        </a>
      </div>
    </div>
  </div>
  <div class="index-row">
    <h3><a href="entry/traces">Traces</a></h3>
    <div class="apis">
      <div class="api">
        <a href="entry/traces#get">
          <p>Get Trace</p>
          <p class="type-get">
            <code>GET</code>
            <code>/api/v0/traces/<em>&lt;traceId&gt;</em></code>
          </p>
        </a>
      </div>
    </div>
  </div>
</div>

<h2 id="data-types">Data types</h2>
<ul>
  <li>Host ID <code><em>&lt;hostId&gt;</em></code>
    <ul>
      <li>This is a string that is provided by Mackerel when a host is registered. It is used to distinguish and identify each unique host the agent is running on.</li>
    </ul>
  </li>
  <li>Service name <code><em>&lt;serviceName&gt;</em></code>
    <ul>
      <li>This is the name of a service registered with Mackerel.</li>
    </ul>
  </li>
</ul>

</div>

<div id="contact-human">
  <p id="human-image"><img src="/assets/images/blog/support.png" alt="" /></p>
  <h4>Need even more help?</h4>
  <p id="contact-human-info">If the content covered in this help page doesn't solve your problem, please contact our support team.</p> 
  <p id="contact-human-button"><a href="https://support.mackerel.io/hc/en-us/requests/new" class="ui-button button-blue">Contact Us</a></p>
</div>
<div id="about-container">
  <h4 id="about"><span>About</span></h4>
  <p>This is the page containing Mackerel's <https://mackerel.io> instructional documents.</p>
</div>
