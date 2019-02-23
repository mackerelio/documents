---
Title: Capistrano 2.x と連携する
Date: 2014-04-30T18:47:53+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/capistrano-2.x
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/12921228815722993191
---

Mackerelに登録しているホストの情報を、[Capistrano 2.x][cap2]のデプロイ対象ホスト群として利用するサンプルです。

[mackerel-client](http://rubygems.org/gems/mackerel-client) Gem を利用します。

Gemfileに追記:

```ruby
gem 'mackerel-client'
```

## Capistrano から利用する

config/deploy.rb (Capfile):

```ruby
load 'deploy'

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

role :app do
  host_ip_addrs(:app)
end

role :db do
  host_ip_addrs(:db)
end
```

<var>SERVICE_NAME</var>にはMackerelに登録しているサービスの名前を指定してください。

Capistranoのロールとして、Mackerelでサービスに所属しているホスト群を指定できます。

- ステータスが working または standby になっているホストが一覧できます。
- ホストのIPアドレスはエージェントが収集したものが使用されます。

ここでタスクを定義します。
```
desc "Mackerel integration test"
namespace :deploy do
  desc "echo message"
  task :echo , roles => :app do
    run "echo \"Integration Test\""
  end
end
```

このタスクを実行すると次のような結果となります。
```
# bundle exec cap deploy:echo
  * 2014-09-23 21:36:28 executing `deploy:echo'
  * executing "echo \"Integration Test\""
    servers: ["127.0.0.1"]
    [127.0.0.1] executing command
 ** [out :: 127.0.0.1] Integration Test
    command finished in 435ms
```

[cap2]: https://github.com/capistrano/capistrano/wiki
