---
Title: Creating check plugins with checkers
Date: 2016-04-22T18:45:23+09:00
URL: https://mackerel.io/docs/entry/advanced/checkers
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6653812171392487214
---

The official check plugins are created using a utility library called [github.com/mackerelio/checkers](https://github.com/mackerelio/checkers). Additionally, it uses [github.com/jessevdk/go-flags](https://github.com/jessevdk/go-flags) together for the command line parameter parse.

This section describes how to create check plugins using checkers.

## Source code structure for the check plugin

If you are using checkers, the plugin’s source code will consist of the following 4 parts.

1. Package declaration and import statement
2. Defining `struct` for command line parameter use
3. Defining the `main()` function
4. Implementing the `run()` function

Here, let's take a look at each using [check-uptime](https://github.com/mackerelio/go-check-plugins/tree/master/check-uptime) as an example.

## 1. Package declaration and import statement

```go
package main

import (
    "fmt"
    "os"
    "time"

    "github.com/jessevdk/go-flags"
    "github.com/mackerelio/checkers"
    "github.com/mackerelio/golib/uptime"
)
```

The command will execute as is, so declare `package` as `main` and import other necessary libraries. 

## 2. Defining `struct` for command line parameter use

```go
var opts struct {
    WarnUnder *float64 `short:"w" long:"warning-under" value-name:"N" description:"Trigger a warning if under the seconds"`
    CritUnder *float64 `short:"c" long:"critical-under" value-name:"N" description:"Trigger a critial if under the seconds"`
    WarnOver  *float64 `short:"W" long:"warning-over" value-name:"N" description:"Trigger a warning if over the seconds"`
    CritOver  *float64 `short:"C" long:"critical-over" value-name:"N" description:"Trigger a critical if over the seconds"`
}
```

Configure the settings of the command line parameters received by the plugin. Because this set-up process conforms to the specifications of github.com/jessevdk/go-flags, refer to the [go-flags document](https://godoc.org/github.com/jessevdk/go-flags) for more details.

The command line parameters specify the threshold settings, middleware port, and more. In the case of `check-time`, if uptime falls or exceeds a certain amount of time,  the four setting options of `warning-under`, `critical-under`, `warning-over`, `critical-over` are possible.

## 3. Defining the `main()` function

```go
func main() {
    ckr := run(os.Args[1:])
    ckr.Name = "Uptime"
    ckr.Exit()
}
```

The `main ()` function is the command’s entry point, but with only three lines, it’s very simple. The `run ()` function receives the `*checkers.Checker` object, configures the `Name`, and ends the sequence. `Name` is meant to specify the words displayed at the beginning when the plugin is run. For example, when running check-uptime, the output will be displayed like shown below. “Uptime” in this output is the `Name`.

```
% check-uptime
Uptime OK: 0 day(s) 3 hour(s) 17 minute(s) (11877 second(s))
```

## 4. Implementing the `run()` function


```go
func run(args []string) *checkers.Checker {
    _, err := flags.ParseArgs(&opts, args) // <- (1) Processing command line parameters
    if err != nil {
        os.Exit(1)
    }
    ut, err := uptime.Get() // <- (2) Obtaining uptime
    if err != nil {
        return checkers.Unknown(fmt.Sprintf("Faild to fetch uptime metrics: %s", err))
    }

    // (3) Threshold comparison and determining the end status
    checkSt := checkers.OK
    if opts.WarnUnder != nil && *opts.WarnUnder > ut {
        checkSt = checkers.WARNING
    }
    if opts.WarnOver != nil && *opts.WarnOver < ut {
        checkSt = checkers.WARNING
    }
    if opts.CritUnder != nil && *opts.CritUnder > ut {
        checkSt = checkers.CRITICAL
    }
    if opts.CritOver != nil && *opts.CritOver < ut {
        checkSt = checkers.CRITICAL
    }

    // (4) Constructing the output message
    dur := time.Duration(ut * float64(time.Second))
    hours := int64(dur.Hours())
    days := hours / 24
    hours = hours % 24
    mins := int64(dur.Minutes()) % 60
    msg := fmt.Sprintf("%d day(s) %d hour(s) %d minute(s) (%d second(s))\n", days, hours, mins, int64(dur.Seconds()))
    return checkers.NewChecker(checkSt, msg)
}
```

The processing of `check-uptime` is implemented almost entirely within this `run()` function. The `run()` function’s signature becomes `func run (args [] string) *checkers.Checker`. This means that it receives the command line parameters and returns the `*checkers.Checker` which is the monitoring results object. 

Although supplemented with comments, this `run()` function can roughly be divided into the following four parts. 

1. Processing command line parameters
2. Obtaining uptime
3. Threshold comparison and determining the end status 
4. Constructing the output message

### Processing command line parameters

In the processing of command line parameters, the contents are mapped in `opt` by `flags.ParseArgs()`.

### Obtaining uptime

Uptime is obtained by using `uptime.Get()` from the github.com/mackerelio/golib/uptime package

### Threshold comparison and determining the end status 

The end status is determined while comparing thresholds within `opt`. The end status uses the `checkers.OK`/`WARNING`/`CRITICAL`/`UNKNOWN` constant.

### Constructing the output message

In constructing the output message, the `*checkers.Checker` object is returned in the end. Here, the object is returned as `return checkers.NewChecker(checkSt, msg)` , but when the end status is determined, checkers allows you to write code more simply like `return checkers.Critical("message")` , so please choose accordingly depending on your situation.

With this, we have explained how to create a general check-plugin. Now, by all means, please try it out for yourself. 

