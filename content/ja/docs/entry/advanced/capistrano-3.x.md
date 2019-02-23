---
Title: Capistrano 3.x と連携する
Date: 2018-04-19T17:20:13+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/capistrano-3.x
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/17391345971636481568
---

Mackerelに登録しているホストの情報を、[Capistrano 3.x][cap3]のデプロイ対象ホスト群として利用するサンプルです。

[mackerel-client](http://rubygems.org/gems/mackerel-client) Gem を利用します。

Gemfileに追記:

```ruby
gem 'mackerel-client'
```

## Capistrano から利用する

config/{environment}.rb :

```ruby
require 'mackerel/client'

set :mackerel_api_key, MACKEREL_API_KEY
set :service, SERVICE_NAME

@client = Mackerel::Client.new(mackerel_api_key: mackerel_api_key)

def host_ip_addrs(role)
  hosts = @client.get_hosts(service: service, roles: role).select do |host|
    host.status === 'standby' || host.status === 'working'
  end.map do |host|
    interface = host.interfaces.find { |i| /^eth/ === i['name'] }
    interface['ipAddress'] if interface
  end.select {|ipaddr| ipaddr != nil }
end

role :app, host_ip_addrs(:app)

role :db, host_ip_addrs(:db)
```

<var>SERVICE_NAME</var>にはMackerelに登録しているサービスの名前を指定してください。`{environment}` には `production` `staging` などが入ります。

Capistranoのロールとして、Mackerelでサービスに所属しているホスト群を指定できます。

- ステータスが working または standby になっているホストが一覧できます。
- ホストのIPアドレスはエージェントが収集したものが使用されます。

これを流用したタスク定義のサンプルを以下に示します。

```ruby
desc "Mackerel integration test"
namespace :deploy do
  desc "echo message"
  task :echo do
    on roles(:app) do |host|
      execute "echo \"Integration Test\" : #{host}"
    end
  end
end
```

このタスクを実行すると次のような結果となります。

```sh
# bundle exec cap <environment> deploy:echo
00:00 deploy:echo
      01 echo "Integration Test" : 127.0.0.1
      01 Integration Test : 127.0.0.1
    ✔ 01 127.0.0.1 0.338s
```

[cap3]: https://github.com/capistrano/capistrano/wiki
