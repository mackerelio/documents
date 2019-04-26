---
Title: Using mkr plugin install
Date: 2017-11-27T18:19:45+09:00
URL: https://mackerel.io/docs/entry/advanced/install-plugin-by-mkr
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812320746567
---

Mackerel plugins as well as check plugins that meet the specifications listed in the document [“Creating plugins supported with mkr plugin install”](https://mackerel.io/docs/entry/advanced/make-plugin-corresponding-to-installer) can be installed using the `mkr plugin install` command. This command retrieves the corresponding executable file from Github Releases, taking into account the OS or CPU Architecture that executed the command, in order to easily install the plugin.

In this document, we’ll be introducing how to use the `mkr plugin install` command.

## Synopsis

```sh
# Usage: mkr plugin install [--prefix <prefix>] [--upgrade] [--overwrite] <install_target>

# Install mackerelio/mackerel-plugin-sample(v0.0.1 release) to /opt/mackerel-agent/plugins/bin
$ sudo mkr plugin install mackerelio/mackerel-plugin-sample@v0.0.1

# Install mackerel-plugin-sample(latest release) defined in plugin registry to /path/to/plugin_dir/bin
$ mkr plugin install --prefix /path/to/plugin_dir mackerel-plugin-sample
```

## Installing plugins from Github
Using `mkr plugin install`, plugins can be installed from Github's repository. However, the plugin must meet the specifications described in [this document](https://mackerel.io/docs/entry/advanced/make-plugin-corresponding-to-installer).

To install from Github, specify the Github owner name, repository name, as well as the tag name for Github Releases as shown below.

```
$ mkr plugin install <owner>/<repo>[@<release_tag>]
```

Using [this release](https://github.com/mackerelio/mackerel-plugin-sample/releases) of [mackerelio/mackerel-plugin-sample](https://github.com/mackerelio/mackerel-plugin-sample) as an example, installing the `v0.0.1` release should be as follows. The plugin’s executable file will be under `/opt/mackerel-agent/plugins/bin` by default.

```sh
$ sudo mkr plugin install mackerelio/mackerel-plugin-sample@v0.0.1
           Downloading https://github.com/mackerelio/mackerel-plugin-sample/releases/download/v0.0.1/mackerel-plugin-sample_darwin_amd64.zip
           Installing /opt/mackerel-agent/plugins/bin/mackerel-plugin-sample
           Successfully installed mackerelio/mackerel-plugin-sample@v0.0.1
$ /opt/mackerel-agent/plugins/bin/mackerel-plugin-sample
sample.dice.d6  1       1508917208
sample.dice.d20 16      1508917208
```


If you omit the tag name for Github Releases, the latest release from Github Releases will be sought and installed. Please note that in order to obtain the latest release, the Github API will be accessed and you will need to configure the Github token as described [later on](#setting-github-token).

```sh
$ sudo mkr plugin install mackerelio/mackerel-plugin-sample
# The latest release v0.0.1 to be installed
```



## Installing plugins registered in the repository
Mackerel provides an [official plugin registry](https://github.com/mackerelio/plugin-registry) to help users find helpful plugins. Plugins registered under [plugins/directory](https://github.com/mackerelio/plugin-registry/tree/master/plugins) can easily be installed using mkr.

```sh
$ mkr plugin install <plugin_name>[@<release_tag>]
```


For example, in order to install the file in the registry called [mackerel-plugin-sample.json](https://github.com/mackerelio/plugin-registry/blob/master/plugins/mackerel-plugin-sample.json), do the following.

```sh
# Install mackerel-plugin-sample release v0.0.1
# Note) The release tag must go to the actual Github repository defined by the registry source
$ sudo mkr plugin install mackerel-plugin-sample@v0.0.1
# Install the latest release of mackerel-plugin-sample
$ sudo mkr plugin install mackerel-plugin-sample
```



## Installing plugins in a different location
By default, the plugin's executable file is installed in `/opt/mackerel-agent/plugins/bin`, but can be installed in a different location by using the `--prefix` option.

```sh
$ mkr plugin install --prefix /path/to/plugin_dir mackerel-plugin-sample
# Command to be installed under /path/to/plugin_dir/bin
```

## Installing only if the plugin of the specified release tag is not already installed
Using the `--upgrade` option, you can skip the installation process if the plugin of the specified release tag is already installed.

```
$ sudo mkr plugin install mackerelio/mackerel-plugin-sample@v0.0.2
           Downloading https://github.com/mackerelio/mackerel-plugin-sample/releases/download/v0.0.2/mackerel-plugin-sample_darwin_amd64.zip
           Installing /opt/mackerel-agent/plugins/bin/mackerel-plugin-sample
           Successfully installed mackerelio/mackerel-plugin-sample@v0.0.2
$ sudo mkr plugin install --upgrade mackerelio/mackerel-plugin-sample@v0.0.2
           release_tag v0.0.2 is already installed. Skip installing for now
$ sudo mkr plugin install --upgrade mackerelio/mackerel-plugin-sample@v0.0.3
           Downloading https://github.com/mackerelio/mackerel-plugin-sample/releases/download/v0.0.3/mackerel-plugin-sample_darwin_amd64.zip
           Installing /opt/mackerel-agent/plugins/bin/mackerel-plugin-sample
           Successfully installed mackerelio/mackerel-plugin-sample@v0.0.3
```

This option allows you to avoid meaningless downloads. We recommend adding in this upgrade option when performing plugin installation with provisioning tools.

You can also use the upgrade option to downgrade. For example, if v.0.0.2 is currently installed and v0.0.1 is then specified and executed, v0.0.1 will be installed.

## Overwriting existing executable files with the same name
If an executable file with the same name already exists, mkr will skip the installation of that file by default. By attaching the `--overwrite` option you can choose to always overwrite without skipping.

```sh
# Skip when executable file already exists
$ sudo mkr plugin install mackerel-plugin-sample
           Downloading https://github.com/mackerelio/mackerel-plugin-sample/releases/download/v0.0.1/mackerel-plugin-sample_darwin_amd64.zip
           /opt/mackerel-agent/plugins/bin/mackerel-plugin-sample already exists. Skip installing for now
           Successfully installed mackerel-plugin-sample
# Forcibly overwrite with --overwrite option
$ sudo mkr plugin install mackerel-plugin-sample --overwrite
           Downloading https://github.com/mackerelio/mackerel-plugin-sample/releases/download/v0.0.1/mackerel-plugin-sample_darwin_amd64.zip
           Installing /opt/mackerel-agent/plugins/bin/mackerel-plugin-sample
           Successfully installed mackerel-plugin-sample
```



This option is useful if you want to reinstall plugins of the same version. Otherwise use the `--upgrade` option.

<h2 id="setting-github-token">Configuring the Github token</h2>

`mkr plugin install` uses the Github API to find the latest release from Github. Therefore, if you do not specify an access token that can be obtained from the [Github settings screen](https://github.com/settings/tokens), the installation may fail due to the [Github API Rate Limit](https://developer.github.com/v3/#rate-limiting).

In mkr, the environment variable `GITHUB_TOKEN` or `github.token` from `.gitconfig` is used as the access token. Please specify one of these.

```sh
# Configure with environment variable
$ export GITHUB_TOKEN=<github_access_token>
# Configure with .gitconfig
$ git config --global github.token <github_access_token>
```

## Using mkr plugin install from a server provisioning tool
Even if the Github access token is configured, you may be subject to restrictions of the Github API Rate Limit if using `mkr plugin install` from a large number of servers all at once with a server provisioning tool such as Chef or Ansible.

If a tag for Github Releases is explicitly specified in `mkr plugin install`, then the Github API will not be accessed and thus you will not be subject to the Rate Limit restrictions. Therefore, we recommend that you explicitly specify the release tag when using `mkr plugin install` from a server provisioning tool.

## Important points when using mkr plugin install in Windows environments

Currently, because the mkr.exe included in the mackerel-agent installer (msi file) is a 32-bit version (386 build), similar to the mackerel-agent.exe, the plugin obtained by `mkr plugin install` will also be a 386 build. 

The plugin installation path will be under the mackerel-agent installation directory `plugins\bin`. Therefore, Windows administrative authority is required to run mkr.exe for deployment. In addition, please describe the following in the mackerel-agent.conf when using the installed plugin.

```
[plugin.metrics.sample]
command = ["plugins/bin/mackerel-plugin-sample"]
```

## Creating a plugin that can be installed with mkr plugin install
If you want to create a plugin that can be installed with `mkr plugin install`, refer to the following documents.

- [Using go-mackerel-plugin to create custom metric plugins](https://mackerel.io/docs/entry/advanced/go-mackerel-plugin)
- [Creating plugins supported with mkr plugin install](https://mackerel.io/docs/entry/advanced/make-plugin-corresponding-to-installer)

If you create a useful plugin, we’d love to have you register it in the [plugin registry](https://github.com/mackerelio/plugin-registry) and supported with the installer.
