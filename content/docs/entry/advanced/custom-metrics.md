---
Title: Posting user-defined custom metrics
Date: 2014-11-12T11:52:00+09:00
URL: https://mackerel.io/docs/entry/advanced/custom-metrics
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8454420450073281205
---

In addition to standard host metrics which are automatically posted to Mackerel by the agent such as loadavg5, cpu.user, etc., any user-defined values or "custom metrics" can be configured to be periodically posted to Mackerel.

By entering a measured value output command in a format compatible with [sensu][] (a plugin which will be mentioned later), that output will be sent together with default metrics to Mackerel and will be displayed in a separate graph. 

We also offer an official plugin pack which you can learn more about here: [Using the official plugin pack to visualize middleware metrics](https://mackerel.io/docs/entry/howto/mackerel-agent-plugins)

Additionally, for more information on how to develop a plugin using [github.com/mackerelio/go-mackerel-plugin](https://github.com/mackerelio/go-mackerel-plugin) (a helper library that is used in our official plugins), please refer to [Using go-mackerel-plugin to create a custom metric plugin](https://mackerel.io/docs/entry/advanced/go-mackerel-plugin).

<h2 id="setting">Configuration</h2>

Add the below item to the [agent configuration file](https://mackerel.io/docs/entry/spec/agent#config-file) like the example below.

```config
[plugin.metrics.vmstat]
command = [ "ruby", "/path/to/vmstat-metrics.rb" ]
user = "SOME_USER_NAME" # optional
custom_identifier = "SOME_IDENTIFIER" # optional
include_pattern = 'cpu' # optional
exclude_pattern = 'waiting$' # optional
timeout_seconds = 45 # optional
env = { SAMPLE_KEY = "VALUE" } # optional
```

The configuration item name enclosed by `[]` in the first line must begin with "plugin.metrics." and contain exactly two dots. When specifying more than one plugin, please specify a different name for each.

Descriptions for each configuration item on and after the second line are listed below. The only mandatory item is `command`, all other items are optional.

- `command`: A command which mackerel-agent runs periodically and whose stdout is used as metric data. This command must follow the <a href="#post-metric">specifications</a> mentioned below.
    - It is also possible to transfer a string to `command`. As shown in the example above, you can specify command as: `command = "ruby /path/to/vmstat-metrics.rb"`
        - If a string is transferred, it will run via a shell and escaping will be necessary.
        - If an array is transferred, it will not run via a shell and escaping will be unnecessary.
- user: The user to run command can be specified with `command`. If a user is not specified, the executing user of mackerel-agent will also be the executing user of command. Not yet supported for Windows environments.
- custom_identifier: Sends the resulting metrics as metrics of the specified identifier, and not as metrics of the host on which the agent is running.
    - This is useful for adding metrics to hosts integrated with AWS / Azure / Google Cloud integration. Reference the [AWS Integration document](https://mackerel.io/docs/entry/integrations/aws#plugin-custom-identifier) for details.
- include_pattern / exclude_pattern: Regular expressions can be written to post specific metrics of the execution result to Mackerel.
    - If include_pattern is specified, only metrics with a name that matches the specified regular expression are posted.
    - If exclude_pattern is specified, only metrics whose metric name does not match the specified regular expression are posted.
    - Metrics with a name that matches both include_pattern and exclude_pattern will not be posted.
- timeout_seconds: Specify the plugin timeout in seconds. The default value is 30 seconds. Since simultaneous activation for each plugin is not controlled, we recommend that the plugin execution interval not be exceeded.
- env: Environment variables can be specified to pass to command. Specify with TOML [Table](https://github.com/toml-lang/toml#table) or [Inline Table](https://github.com/toml-lang/toml#inline-table).

**Caution: Using PowerShell Script**

Depending on the Windows PowerShell Script execution policy, plugins written in PowerShell Script may not be able to be executed. In that case, prepare a batch file as shown below and assign it to the plugin's command and it will then be able to be executed.

```bat
@echo off
cd %~dp0

powershell Set-ExecutionPolicy RemoteSigned
powershell .\sample-plugin.ps1
```
<h2 id="post-metric">Posting metrics</h2>

The command should output in the following format (\t is the tab character):

```
{metric name}\t{metric value}\t{epoch seconds}
```

- Metrics whose names are the same up to the last dot (`.`) will be grouped together and charted in one graph.
- All custom metrics are given the prefix "custom." automatically.
- Characters which can be used in custom metric names include any alphanumeric characters as well as hyphens (`-`), underscores (`_`), and dots (`.`).

**Example: if posting metrics named `example.foo` and `example.bar`:**

A graph named `custom.example.*` will appear in the host details page displaying data from both `example.foo` and `example.bar`.

[sensu]: http://sensuapp.org/

<h2 id="graph-schema">Defining Graph Schema</h2>

You can specify the display settings of graphs created by posted custom metrics with the plugin's command executed by the agent in JSON format.

Upon booting up mackerel-agent runs once for each of the custom metric commands specified in the configuration file with environment variable `MACKEREL_AGENT_PLUGIN_META` set to "1". If the command's output is in the format described below, the agent will recognize the output as the plugin's meta information and will send the graph definition to Mackerel. With this feature, you can configure graph display settings without having to manage them on the web UI.

The first line of the output must be as shown below, otherwise mackerel-agent will assume that this plugin does not output the meta-information and won’t be able to set graph definition. 

```
# mackerel-agent-plugin
```

Moving on from there, we will output the following configuration in JSON.

```
{
  "graphs": {
    {graph}: {
      "label": GRAPH_LABEL,
      "unit": UNIT_TYPE
      "metrics": [
        {
          "name": METRIC_NAME,
          "label": METRIC_LABEL
        },
        ...
      ]
    },
    GRAPH_NAME: ...
  }
}
…
```

The following is a description of each item.

| Key | Description |
| ---- | ---- |
| `graphs.{graph}` | The name for the graph corresponding to custom metrics {graph}.* . {graph} may contain dots (`.`). Additionally, wildcard characters (`*` and `#`) can also be used. For more details, refer to the [API specs (v0) / Posting Graph Definitions help page.](https://mackerel.io/api-docs/entry/host-metrics#post-graphdef) |
| `graphs.{graph}.label` | The label for the graph corresponding to custom metrics {graph}.* . |
| `graphs.{graph}.unit` | The type of value displayed in the graph corresponding to custom metrics {graph}.* . Must be one of the following: "float", "integer", "percentage", "bytes", "bytes/sec", or "iops". |
| `graphs.{graph}.metrics` | An array of metric definitions of custom metrics {graph}.* . |

Metric definitions contain the following items:


| Key | Description |
| ---- | ---- | 
| `name` | Specifies that this definition corresponds to custom metric {graph}.{name} . {name} cannot contain dots (`.`). Any alphanumeric characters, hyphen (`-`), or underscore (`_`) can be used（/[-a-zA-Z0-9_]/）. | 
| `label` | The label of the series corresponding to custom metric {graph}.{name} . | 
| `stacked` | Indicates whether or not the series of custom metrics {graph}.{name} is in stacked display mode. If false it will be displayed in line segment mode. |

<h3 id="graph-schema-example-ruby">Example in Ruby</h3>

Here is a little example in Ruby, which posts the rolls of 6-sided and 20-sided dice to Mackerel.

```ruby
if ENV['MACKEREL_AGENT_PLUGIN_META'] == '1'
  require 'json'

  meta = {
    :graphs => {
      'super.dice' => {
        :label   => 'My Dice',
        :unit    => 'integer',
        :metrics => [
          {
            :name  => 'd6',
            :label => 'Die (d6)'
          }, {
            :name  => 'd20',
            :label => 'Die (d20)'
          }
        ]
      }
    }
  }

  puts '# mackerel-agent-plugin'
  puts meta.to_json
  exit 0
end

puts [ 'super.dice.d6',  rand( 6)+1, Time.now.to_i ].join("\t")
puts [ 'super.dice.d20', rand(20)+1, Time.now.to_i ].join("\t")
```

With this plugin you can create a graph like the one shown here.

<p><span itemscope itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20171017/20171017113656.png" alt="f:id:mackerelio:20171017113656p:image" title="f:id:mackerelio:20171017113656p:image" class="hatena-fotolife" itemprop="image"></span></p>

<h2 id="changes">Update History</h2>
- 2014-11-17
  - Available characters specified for metric name. 
- 2014-10-02
  - Removal of “experimental” from “Defining Graph Schema”. 
- 2014-09-05
  - Deletion of “type: designate a metric” in settings. 
  - Addition of descriptions for official plugin package.
- 2014-08-22
  - Version 0.12 support for content in “Defining Graph Schema（experimental)”. 
- 2014-08-07
  - Addition of “Defining Graph Schema（experimental)”.
- 2014-05-27
  - Item name in “configuration” changed from “sensu.checks” to “plugin.metrics”.
  - Update "Posting metrics" to meet the specification change where metric names are given the "custom." prefix.


-

If you have any questions please contact our support team at support@mackerel.io
