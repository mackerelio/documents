---
Title: Using Chef to set-up mackerel-agent
Date: 2015-02-16T12:15:41+09:00
URL: https://mackerel.io/docs/entry/howto/chef
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450083874503
---

Make setting up mackerel-agent even easier with [mackerelio/cookbook-mackerel-agent · GitHub](https://github.com/mackerelio/cookbook-mackerel-agent).

##Software requirements

- Chef v12.5 or later
- Ruby v2.0 or later

##Installation

If you are using [Berkshelf](https://docs.chef.io/berkshelf.html) for management of cookbook, please add to `Berksfile` the following setting:
```
cookbook 'mackerel-agent'
```
##Usage

Enter the following setting into the chef cookbook recipe you are using. Where it says ‘Your APIKEY’ specify the API key which is displayed in the your [organization’s page](https://mackerel.io/my). Generating a new API key for use with Chef is recommended. 
```ruby
node.default['mackerel-agent']['conf']['apikey'] = 'Your APIKEY'

include_recipe 'mackerel-agent'
include_recipe 'mackerel-agent::plugins' # Option for installation of mackerel-agent-plugins package
```
##Attributes

Attributes have been prepared  as are shown below. In regards to `default['mackerel-agent']['conf']['apikey']` , just as was described in the previous section, “Usage,” it is necessary to specify the API key here as well.
```ruby
default['mackerel-agent']['conf']['apikey']  = "YOUR APIKEY"
default['mackerel-agent']['conf']['pidfile'] = "/path/to/pidfile"
default['mackerel-agent']['conf']['root'] = "/var/lib/mackerel-agent"
default['mackerel-agent']['conf']['verbose'] = false
default['mackerel-agent']['conf']['roles'] = ["My-Service:app", "Another-Service:db"]

# Specification of host custom metrics plugin
default['mackerel-agent']['conf']['plugin.metrics.vmstat'] = {
   'command' => '["ruby", "/etc/sensu/plugins/system/vmstat-metrics.rb"]',
}
```
For information about host custom metrics please refer to the following entry. 

[https://mackerel.io/docs/entry/advanced/custom-metrics:embed:title]

