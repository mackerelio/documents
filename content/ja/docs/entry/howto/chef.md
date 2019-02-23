---
Title: Chefによるセットアップ
Date: 2014-09-02T16:08:57+09:00
URL: https://mackerel.io/ja/docs/entry/howto/chef
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815731865586
---

[https://github.com/mackerelio/cookbook-mackerel-agent:title] を利用するとmackerel-agentを簡単にセットアップできます。

必要なソフト
========

- Chef 12.5 以上
- Ruby 2.0 以上

インストール
========

cookbook の管理に [Berkshelf](https://docs.chef.io/berkshelf.html) をお使いの場合は ```Berksfile```に以下の設定を追加してください。

```
cookbook 'mackerel-agent'
```

使い方
========

お使いの chef cookbook の recipe に以下の設定を追加してください。
'Your API KEY' には[オーガニゼーション](https://mackerel.io/my)ページにて表示されるAPIキーを指定してください。Chef 用の API キーを新規作成することを推奨します。

```ruby
node.default['mackerel-agent']['conf']['apikey'] = 'Your APIKEY'

include_recipe 'mackerel-agent'
include_recipe 'mackerel-agent::plugins' # Option for installation of mackerel-agent-plugins package
```

アトリビュート
==========

次のようなアトリビュートを用意しています。
```default['mackerel-agent']['conf']['apikey']``` については「使い方」のように、必ず指定する必要があります。

```ruby
default['mackerel-agent']['conf']['apikey']  = "YOUR APIKEY"
default['mackerel-agent']['conf']['pidfile'] = "/path/to/pidfile"
default['mackerel-agent']['conf']['root'] = "/var/lib/mackerel-agent"
default['mackerel-agent']['conf']['verbose'] = false
default['mackerel-agent']['conf']['roles'] = ["My-Service:app", "Another-Service:db"]

# ホストのカスタムメトリックプラグインの指定
default['mackerel-agent']['conf']['plugin.metrics.vmstat'] = {
   'command' => '["ruby", "/etc/sensu/plugins/system/vmstat-metrics.rb"]',
}
```

ホストのカスタムメトリックについては、以下のエントリを参照してください。

[https://mackerel.io/ja/docs/entry/advanced/custom-metrics:embed:title]
