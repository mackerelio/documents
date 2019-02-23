---
Title: Integrating Capistrano 2.x
Date: 2015-11-25T14:20:18+09:00
URL: https://mackerel.io/docs/entry/advanced/capistrano-2.x
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6653586347146409405
---

This is a sample showing how to use the information of your hosts registered to Mackerel as deploy target hosts for [Capistrano 2.x][cap2].

We will use the [mackerel-client](http://rubygems.org/gems/mackerel-client) gemfile.

Appending the gemfile:

```ruby
gem 'mackerel-client'
```

## From Capistrano

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

Designate the name of the service registered to Mackerel in <var>SERVICE_NAME</var>.

The hosts that belong to that service in Mackerel can be assigned as Capistrano roles.

- Hosts whose statuses are either working or standby can be listed.
- As for the host IP addresses, information collected by the agent will be used.

Here we will define the task.
```
desc "Mackerel integration test"
namespace :deploy do
  desc "echo message"
  task :echo , roles => :app do
    run "echo \"Integration Test\""
  end
end
```

Executing the task will yield the following results.
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
