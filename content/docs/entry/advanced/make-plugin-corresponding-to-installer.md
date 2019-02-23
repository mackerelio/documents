---
Title: 'Creating plugins supported with mkr plugin install '
Date: 2017-11-27T18:18:58+09:00
URL: https://mackerel.io/docs/entry/advanced/make-plugin-corresponding-to-installer
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812321593891
---

When creating a Mackerel plugin, if the plugin installer specifications are met, you can easily install the plugin using the `mkr plugin install` command. This document explains how to do that and how to register your created plugin in the official registry.

For details on how to install plugins using mkr, see [Using mkr plugin install](https://mackerel.io/docs/entry/advanced/install-plugin-by-mkr). For details on how to implement plugins using Go, see [Using go-mackerel-plugin to create a custom metric plugin](https://mackerel.io/docs/entry/advanced/go-mackerel-plugin).

## Required specifications

In order to install with the `mkr plugin install` command, the Github repository of the created plugin must meet the following specifications.

- Release tags for each version in Github Releases of the plugin repository must be removed and an archive for each OS/Arch must be uploaded according to the naming convention
- The archive filename must follow the `(REPOSITORY_NAME)_(GOOS)_(GOARCH).zip` naming convention
    - For the strings contained in GOOS and GOARCH, refer to  https://golang.org/doc/install/source#environment
    - (Example) If https://github.com/mackerelio/mackerel-plugin-sample is the plugin repository, the archive for 64bit Linux would be `mackerel-plugin-sample_linux_amd64.zip`. - The archive for 32bit macOS would be `mackerel-plugin-sample_darwin_386.zip`
Within the archive, there must be a file with execution authority that starts with `mackerel-plugin-` or `check-`

Although not required, it is recommended that you also follow the specifications listed below.

- One repository should provide one execution file for the plugin
- The repository name and the execution file name should match
- The naming convention vx.y.z should be followed for release tags uploaded in Github Releases (Example: v1.2.3)
- The README.md and LICENSE file should be included in the archive

Additionally, as long as the required specifications are met, even plugins not implemented using Go can be installed using the `mkr plugin install` command.

Refer to [mackerelio/mackerel-plugin-sample](https://github.com/mackerelio/mackerel-plugin-sample) for samples that meet the recommended specifications.

## Configuring the plugin to meet specifications
So now let’s try configuring your plugin to correspond with the recommended specifications. We’ll use the sample repository [mackerelio/mackerel-plugin-sample](https://github.com/mackerelio/mackerel-plugin-sample)  as an example.

In order to meet the specifications of the plugin installer, you’ll need to do three things: create an executable file, create a release archive, and make a release in Github Releases. Since the mackerel-plugin-sample is implemented with Go, we’ll create the executable file and release archive with [goxz](https://github.com/Songmu/goxz) and make the release in Github Releases with [ghr](https://github.com/tcnksm/ghr). For this example, we’ll be using the tools goxz + ghr , but as long as the specifications are met, the type of tool is not significant.

### Install goxz and ghr
First, let’s install the tools, goxz and ghr. Go execution environment is necessary for installation.

```sh
$ go get -v -u github.com/Songmu/goxz/cmd/goxz
$ go get -v -u github.com/tcnksm/ghr
```


### Create an executable file and release archive
Next, let’s create an executable file and release archive using goxz.

In order to meet the spectifications of the plugins installer, execute `goxz` using following options.  Then a release archive is created below the specified directory.

```console
$ goxz -d dist/v0.0.1 -z -os windows,darwin,linux -arch amd64,386
$ ls -1 dist/v0.0.1
mackerel-plugin-sample_darwin_386.zip
mackerel-plugin-sample_darwin_amd64.zip
mackerel-plugin-sample_linux_386.zip
mackerel-plugin-sample_linux_amd64.zip
mackerel-plugin-sample_windows_386.zip
mackerel-plugin-sample_windows_amd64.zip
$ unzip dist/v0.0.1/mackerel-plugin-sample_linux_amd64.zip
$ ls -1 mackerel-plugin-sample_linux_amd64
CHANGELOG.md
LICENSE
README.md
mackerel-plugin-sample
```

Using windows/darwin/linux OS amd64/386 Architecture, we create an executable file that corresponds with a total of six platforms. You can adjust which platforms to support by the `-os` and `-arch` command line options of `goxz`.

Please refer to [official goxz documentation](https://github.com/Songmu/goxz) for configuration details.

### Release in Github Releases
Now that we’ve created the release archive by `goxz`, let’s release it in Github Releases. This can be done very easily using ghr. For example, if you want to release to mackerelio/mackerel-plugin-sample with the release tag v.0.0.1, just execute the following command.


```console
$ GITHUB_TOKEN=... ghr -u mackerelio -r mackerel-plugin-sample v0.0.1 dist/v0.0.1
```


The following will be displayed in the Github Releases page if successful.

https://github.com/mackerelio/mackerel-plugin-sample/releases
<p><span itemscope="" itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20171025/20171025162942.png" alt="f:id:mackerelio:20171025162942p:plain" title="f:id:mackerelio:20171025162942p:plain" class="hatena-fotolife" itemprop="image"></span></p>

### Creating a release utility
With the configurations made up until this point, we’ve met the recommended specifications of the plugin installer and made a release in Github Releases. However, performing all of the above-mentioned work can be tedious and it may be more convenient to create a release utility to do the work all at once.

https://github.com/mackerelio/mackerel-plugin-sample/blob/master/script/release
```sh
#!/bin/sh
set -e
latest_tag=$(git describe --abbrev=0 --tags)
goxz -d dist/$latest_tag -z -os windows,darwin,linux -arch amd64,386
ghr -u mackerelio -r mackerel-plugin-sample $latest_tag dist/$latest_tag
```


By using this, you can make a release with just the following command.

```console
$ git tag v0.0.1
$ GITHUB_TOKEN=... script/release
```

### Test mkr plugin install
After you complete the release, let’s check and see if you can install with `mkr plugin install`.

```console
$ sudo mkr plugin install mackerelio/mackerel-plugin-sample@v0.0.1
           Downloading https://github.com/mackerelio/mackerel-plugin-sample/releases/download/v0.0.1/mackerel-plugin-sample_darwin_amd64.zip
           Installing /opt/mackerel-agent/plugins/bin/mackerel-plugin-sample
           Successfully installed mackerelio/mackerel-plugin-sample@v0.0.1
$ /opt/mackerel-agent/plugins/bin/mackerel-plugin-sample
sample.dice.d6  1       1508917208
sample.dice.d20 16      1508917208
```


## Register in the official plugin registry
To make it easier for other users to find helpful plugins, Mackerel provides an [official plugin registry](https://github.com/mackerelio/plugin-registry). Registered plugins can be installed in the format  of `mkr plugin install <plugin_name>`.

To register, just send a Pull Request to the plugin registry’s Github repository.

- Create a file called `<plugin_name>.json` under the `plugins/` directory
- Write the location of the plugin using the format `<github owner>/<github repo>` in the field labeled `source`
    - The plugin of the specified repository needs to be installable with `mkr plugin install`
- Write a brief description of the plugin in the `description` field
- (Example) https://github.com/mackerelio/plugin-registry/blob/master/plugins/mackerel-plugin-sample.json

---

In this document, we’ve gone over how to make your own plugins compatible with `mkr plugin install` and how to register them in the official plugin registry. If you happen to create a useful plugin that other users might find helpful plugin, by all means, send a Pull Request to the [plugin registry](https://github.com/mackerelio/plugin-registry).
