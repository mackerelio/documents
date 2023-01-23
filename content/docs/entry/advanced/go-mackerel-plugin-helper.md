---
Title: Creating a custom metrics plugin with go-mackerel-plugin-helper
Date: 2016-04-11T11:49:16+09:00
URL: https://mackerel.io/docs/entry/advanced/go-mackerel-plugin-helper
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10328537792370278284
---

**Currently, we recommend that you use [go-mackerel-plugin](https://github.com/mackerelio/go-mackerel-plugin) for creating plugins rather than the [go-mackerel-plugin-helper](https://github.com/mackerelio/go-mackerel-plugin-helper) introduced here. For more details on how to create a plugin using go-mackerel-plugin, refer to https://mackerel.io/docs/entry/advanced/go-mackerel-plugin.**

This document explains how to develop a plugin using [github.com/mackerelio/go-mackerel-plugin-helper](https://github.com/mackerelio/go-mackerel-plugin-helper), a helper library for custom metric development using Go language.

By following this helper to developing plugins, you can easily output graph definitions and calculate differential values of the previously acquired values. Naturally, using this helper will also allow you to write a plugin in the same manner as the official plugin, so if you are considering sending a pull request to the official plugin, we strongly recommend that you use this helper.

## The configuration for when using go-mackerel-plugin-helper 

The following five parts make up the plugin source code for when using go-mackerel-plugin-helper.

1. package declaration and import statement
2. struct definition for plugin use
3. Define the graph definition output method `GraphDefinition` to the struct
4. Define the metric acquisition method `FetchMetrics` to the struct 
5. `main()` function definition

From here, let’s take a look at each using [mackerel-agent-uptime](https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-uptime) as an example. 

### 1. package declaration and import statement 

```go
package main

import (
    "flag"
    "fmt"
    "strings"

    mp "github.com/mackerelio/go-mackerel-plugin-helper"
    "github.com/mackerelio/golib/uptime"
)
```

Declare `main` for `package`. Additionally, it is customary to import go-mackerel-plugin-helper with the alias `mp`. 

### 2. struct definition for plugin use

```go
type UptimePlugin struct {
    Prefix string
}
```

Here, the struct that will be used for the plugin is being defined. A field called `Prefix` is defined in this struct. This is used to decide the prefix of the metric’s namespace when outputting graph definitions. In the standard uptime plugin, `Prefix` is `uptime`, and will output the metric with the key called `uptime.seconds`. This field is used when, for example, you would like to change `uptime` to `uptime2` in the configuration. 

The `Prefix` field isn’t particularly beneficial in the uptime plugin. However, in a middleware plugin for example, separating the metric namespace is necessary in order to obtain each metric while starting up multiples of the same middleware in one host. Because of this, we recommend that you define this field.

In uptime plugin, only the `Prefix` field is defined, but for middleware plugins, the `Port` and `Host` fields may also be required.

Additionally, this plugin struct must meet the `interface` of `mp.Plugin`. The definition of the `interface` is as follows.

```go
type Plugin interface {
    GraphDefinition() map[string]Graphs
    FetchMetrics() (map[string]interface{}, error)
}
```

These are the graph definition output and the metric acquisition methods.

### 3. Define the graph definition output method `GraphDefinition` to the struct

```go
func (u UptimePlugin) GraphDefinition() map[string](mp.Graphs) {
    labelPrefix := strings.Title(u.Prefix)
    return map[string](mp.Graphs){
        u.Prefix: mp.Graphs{
            Label: labelPrefix,
            Unit:  "float",
            Metrics: [](mp.Metrics){
                mp.Metrics{Name: "seconds", Label: "Seconds"},
            },
        },
    }
}
```

By defining `GraphDefinition`, the graph definition JSON and metrics will output correctly. For the uptime plugin, a single graph definition with `u.Prefix` as the key will be returned, but for many other plugins, several graph definitions will be returned with keys such as `u.Prefix + "runtime"` and `u.Prefix + "memory"`.

`Label` is the graph name displayed from the Mackerel page. `Unit` is the unit of the graph. And similar to the graph definition API, "float", "integer", "percentage", "seconds", "milliseconds", "bytes", "bytes/sec", "bits/sec", and "iops" can be configured.

The multiple `Metrics` definitions drawn inside the graph are configured in `Metrics`. Fields that can be specified in the `mp.Metrics` are as follows.

| Field | Type      | Explanation  |
| ---------- | ------  | ----- |
| Name       | string  | (required)Metric name. Corresponds to the key names of the map returned by  `FetchMetrics()`. |
| Label      | string  | The display name on the Mackerel page.                                                      |
| Diff       | bool    | Whether or not the differential values are being calculated by the plugin. (Default: false)                              |
| Type       | strint  | "float64", "uint32" or "uint64". Mainly used for determining the maximum value when the Diff calculation is required from the integer value counter. (Default: float64) |
| Stacked    | bool    | Whether or not the graph is stacked in the Mackerel page. (Default: false)                          |
| Scale      | float64 | If specified, the value obtained is multiplied by the Scale value before outputting. For example, if you want to change the value obtained in KB to Byte, specify 1024. |

### 4. Define the metric acquisition method `FetchMetrics` to the struct 

```go
func (u UptimePlugin) FetchMetrics() (map[string]interface{}, error) {
    ut, err := uptime.Get()
    if err != nil {
        return nil, fmt.Errorf("Faild to fetch uptime metrics: %s", err)
    }
    return map[string]interface{}{"seconds": ut}, nil
}
```

`Fetchmetrics()` will return values in `map[string]interface{}` format.  They become `interface{}`, but in actuality, they are some sort of numerical value. This is done in order to manage `uint64` and `float64` in a unified manner.  

Here, the map where the uptime values are stored is returned with `seconds` as the key. 

### 5. `main()` function definition

```go
func main() {
    optPrefix := flag.String("metric-key-prefix", "uptime", "Metric key prefix")
    optTempfile := flag.String("tempfile", "", "Temp file name")
    flag.Parse()
    
    u := UptimePlugin{
        Prefix: *optPrefix,
    }
    helper := mp.NewMackerelPlugin(u)
    helper.Tempfile = *optTempfile
    if helper.Tempfile == "" {
        helper.Tempfile = fmt.Sprintf("/tmp/mackerel-plugin-%s", *optPrefix)
    }
    helper.Run()
}
```

This is the plugin’s main process. The parsing of the command line options, the creation of the plugin helper object, and the plugin execution is performed.

`Tempfile`, specified in `helper`, is a file for holding the last obtained value which will be used for differential value calculation. In other plugins, and even in the same plugin, there are cases where there are multiple different settings and it is necessary to specify so as not to overlap, so please be careful.

---

Using go-mackerel-plugin-helper, we’ve explained how to create a custom metric plugin. Since this explanation contains merely basic concepts, we invite you to try and create your own plugin embedded with all your favorite packages.  And if you create a plugin that other users might find useful, please send a pull request to the official repository. 

