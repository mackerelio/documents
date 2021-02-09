---
Title: Using go-mackerel-plugin to create a custom metric plugin
Date: 2017-11-27T18:20:34+09:00
URL: https://mackerel.io/docs/entry/advanced/go-mackerel-plugin
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812318003927
---

This document explains how to develop a plugin using [github.com/mackerelio/go-mackerel-plugin](https://github.com/mackerelio/go-mackerel-plugin), a helper library for creating custom metric plugins in Go language (also used in the official plugin).

By following this library’s method of development, you can easily output graph definitions, calculate differences from previously obtained values, and so on. Additionally, this library uses the same method of writing as the official plugin, so if you are considering submitting pull requests to the official plugin repository, we highly recommend using this library.

## source code structure

When using go-mackerel-plugin, the plugin source code consists of the following five parts.

1. The package declaration and import statement
2. The plugin struct definition
3. The graph definition output method `GraphDefinition` defined in struct
4. The metric acquisition method `FetchMetrics` defined in struct
5. The metric key prefix acquisition method `MetricKeyPrefix` defined in struct
6. The `main()` function definition 

Let's look at each using [mackerel-agent-uptime](https://github.com/mackerelio/mackerel-agent-plugins/tree/master/mackerel-plugin-uptime) as an example.


### 1. The package declaration and import statement

```go
package main

import (
    "flag"
    "fmt"
    "strings"

    mp "github.com/mackerelio/go-mackerel-plugin"
    "github.com/mackerelio/go-osstat/uptime"
)
```


Declare the `package` as `main`. Additionally, it’s typical to import go-mackerel-plugin under the alias `mp`. 

### 2. The plugin struct definition

```go
type UptimePlugin struct {
    Prefix string
}
```

This is the definition of the plugin struct. In this struct, the `Prefix` field is defined. This is used to determine the beginning of the metric namespace when outputting graph definitions. The standard `Prefix` for the uptime plugin is `uptime` and metrics are output with the key `uptime.seconds`, but this field is used when you want to change the `uptime` to `uptime2` for example.

In the uptime plugin, the `Prefix` field is not particularly useful. However, for middleware plugins for example, if you run the same middleware multiple times on one host and you want to obtain each metric, you’ll need to separate the metric namespaces. Therefore, defining this field is recommended.

In the uptime plugin, only this `Prefix` field is defined, but if it’s a general middleware plugin, the fields `Port` and `Host` will also be required.

Additionally, this plugin struct must satisfy the `interface` of `mp.PluginWithPrefix` . The `interface` definition is as follows.

```go
type PluginWithPrefix interface {
    FetchMetrics() (map[string]float64, error)
    GraphDefinition() map[string]mp.Graphs
    MetricKeyPrefix() string
}
```

This is the graph definition output method, the metric acquisition method, and the metric prefix acquisition method.

### 3. The graph definition output method `GraphDefinition` defined in struct

```go
func (u UptimePlugin) GraphDefinition() map[string]mp.Graphs {
    labelPrefix := strings.Title(u.MetricKeyPrefix())
    return map[string]mp.Graphs{
        "": {
            Label: labelPrefix,
            Unit:  mp.UnitFloat,
            Metrics: []mp.Metrics{
                {Name: "seconds", Label: "Seconds"},
            },
        },
    }
}
```


By defining this `GraphDefinition`, the graph definition JSON and metrics will be output correctly. With the uptime plugin, only one graph definition is returned with the empty string as the key, but many plugins return multiple graph definitions with keys like `"runtime"`, `"memory"`.

When the plugin outputs the list of graph definitions, it attaches the prefix returned by the `MetricKeyPrefix()` function before each key of the graph definition. For example, if `MetricKeyPrefix()` returns `uptime`, it’s output as `""` -> `"uptime"`, `"runtime"` -> `"uptime.runtime"`.

`Label` is the graph name displayed in Mackerel, `Unit` is the unit of graph, and "float", "integer", "percentage", "bytes", "bytes / sec", "iops" are able to be specified similar to the graph definition API.

`Metrics` specifies multiple `Metrics` definitions to be rendered within the graph. The fields for `mp.Metrics`  are as follows.

| Field | Type      | Explanation  |
| ---------- | ------  | ----- |
| Name       | string  | (Required) Name of the metric. It corresponds to the key name of the map to be acquired by `FetchMetrics()` |
| Label      | string  | Name displayed in Mackerel                                                      |
| Diff       | bool    | Whether or not to calculate the difference value in the plugin (Default: false)                              |
| Stacked    | bool    | Whether or not to display stacked in Mackerel (Default: false)                          |
| Scale      | float64 | If specified, the obtained value is multiplied by the value of this Scale before outputting. For example, if you want to correct the value obtained with KB into Byte, 1024 should be specified |




### 4. The metric acquisition method `FetchMetrics` defined in struct


```go
func (u UptimePlugin) FetchMetrics() (map[string]float64, error) {
    ut, err := uptime.Get()
    if err != nil {
        return nil, fmt.Errorf("Failed to fetch uptime metrics: %s", err)
    }
    return map[string]float64{"seconds": ut.Seconds()}, nil
}
```


`FetchMetrics()`  returns values in `map[string]float64` format. It returns a map with the uptime value stored with the key as `seconds`. `seconds` is the specified `Name` in the `GraphDefinition` above.

### 5. The metric key prefix acquisition method `MetricKeyPrefix` defined in struct

```go
func (u UptimePlugin) MetricKeyPrefix() string {
    if u.Prefix == "" {
        u.Prefix = "uptime"
    }
    return u.Prefix
}
```


Define the method for obtaining the metric prefix. If specified by the plugin user, the specified prefix is returned, if not `"uptime"` is returned by default.

### 6. The `main()` function definition

```go
func main() {
    optPrefix := flag.String("metric-key-prefix", "uptime", "Metric key prefix")
    optTempfile := flag.String("tempfile", "", "Temp file name")
    flag.Parse()

    u := UptimePlugin{
        Prefix: *optPrefix,
    }
    plugin := mp.NewMackerelPlugin(u)
    plugin.Tempfile = *optTempfile
    plugin.Run()
}
```


This is the plugin’s main procedure. It parses command line options, creates plugin objects, and runs the plugin.

The `Tempfile`, specified in `plugin`, is a file that holds the last values obtained for calculating the difference values. Please note that this should be specified so that overlaps do not occur with other plugins or when there are multiple configurations with varying parameters within the same plugin.

---

This is just a basic explanation on how to create a custom metric plugin using go-mackerel-plugin. Be sure to try to incorporate your favorite package and create your own original plugin. 

If you create a useful plugin that other users might find helpful, follow the document “Creating plugins supported with mkr plugin install” to make the plugin available with `mkr plugin install` and register it in the [plugin registry](https://github.com/mackerelio/plugin-registry).

## Related documents
- [Using mkr plugin install](https://mackerel.io/docs/entry/advanced/install-plugin-by-mkr)
- [Creating plugins supported with mkr plugin install](https://mackerel.io/docs/entry/advanced/make-plugin-corresponding-to-installer)
