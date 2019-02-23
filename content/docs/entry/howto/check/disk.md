---
Title: Disk monitoring
Date: 2017-10-02T16:13:29+09:00
URL: https://mackerel.io/docs/entry/howto/check/disk
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812303796946
---

Disk monitoring can be done using `check-disk` of the official check plugin pack. To learn how to install the official check plugin pack, please refer to [Using the official check plugin pack for check monitoring](https://mackerel.io/docs/entry/howto/mackerel-check-plugins).

## Using `check-disk` 

In order to monitor disks using `check-disk`, write a command similar to the following example into the mackerel-agent.conf and restart the mackerel-agent.

In the following example, Warning occurs when the `/tmp` disk capacity is 10% or less, and Critical when the disk capacity is 5% or less.

```config
[plugin.checks.disk]
command = ["check-disk", "--path", "/tmp", "--warning", "10%", "--critical", "5%"]
```

The options `--warning` and `--critical` can be specified by disk capacity instead of "%". Additionally, multiple `--path` options can be specified, in which case they are treated as OR conditions.

In the following example, the two partitions "/tmp" and "/var" are checked and if the capacity of either partition falls below 10 GB, Warning will occur. If the capacity falls below 5 GB, Critical will occur.

```config
[plugin.checks.disk]
command = ["check-disk", "--path", "/tmp", "--path", "/var", "--warning", "10", "--critical", "5", "--units", "GB"]
```


## Specifying thresholds

With `check-disk`, thresholds can be configured to generate alerts for the monitoring target disk using options like the following.

- `-w`, `--warning`
  - If the available disk space or the disk ratio is less than the configured value, a Warning alert occurs
- `-c`, `--critical`
  - If the available disk space or the disk ratio is less than the configured value, a Critical alert occurs
- `-W`, `--iwarning`
  - If the inode ratio is less than the configured value, a Warning alert occurs
- `-K`, `--icritical`
  - If the inode ratio is less than the configured value, a Critical alert occurs


## Specifying partitions 

By default, `check-disk` monitors all devices, but you can also specify the file system type and the device mount point to be monitored.

- `-p`, `--path`
  - Monitor the specified device (multiple specifications possible)
- `-x`, `--exclude-device`
  - Exclude the specified device from the monitoring targets
- `-X`, `--exclude-type`
  - Exclude devices of the specified file system type from the monitoring targets (multiple specifications possible)
- `-N`, `--include-type`
  - Monitor devices of the specified file system type only (multiple specifications possible)

In the following example, all devices excluding “/tmp” type are monitored.

```config
[plugin.checks.disk]
command = ["check-disk", "--include-type", "none", "--xclude-device", "/tmp", "--warning", "10%", "--critical", "5%"]
```


For other options check out `check-disk --help` and [README](https://github.com/mackerelio/go-check-plugins/blob/master/check-disk/README.md).

## Source code

The `check-disk` source code is publicly available here: 

<https://github.com/mackerelio/go-check-plugins/tree/master/check-disk>
