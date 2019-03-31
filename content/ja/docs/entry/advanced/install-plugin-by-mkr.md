---
Title: mkr plugin installでプラグインをインストールする
Date: 2017-10-25T15:13:26+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/install-plugin-by-mkr
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8599973812311199657
---

[mkr plugin installに対応したプラグインを作成する](https://mackerel.io/ja/docs/entry/advanced/make-plugin-corresponding-to-installer)のドキュメントの仕様を満たしたmackerelプラグイン及びcheckプラグインは、`mkr plugin install`コマンドでインストールできます。このコマンドは、コマンドを実行したOSやArchを考慮して対応する実行ファイルをGithub Releasesから取り出してくれるため、簡単にプラグインをインストールできます。

本ドキュメントでは`mkr plugin install`コマンドの利用方法について紹介します。

## Synopsis

```sh
# Usage: mkr plugin install [--prefix <prefix>] [--upgrade] [--overwrite] <install_target>

# Install mackerelio/mackerel-plugin-sample(v0.0.1 release) to /opt/mackerel-agent/plugins/bin
$ sudo mkr plugin install mackerelio/mackerel-plugin-sample@v0.0.1

# Install mackerel-plugin-sample(latest release) defined in plugin registry to /path/to/plugin_dir/bin
$ mkr plugin install --prefix /path/to/plugin_dir mackerel-plugin-sample
```

## Githubからプラグインをインストールする
`mkr plugin install`を使うと、Githubのレポジトリからプラグインをインストール出来ます。ただし、プラグインは[このドキュメント](https://mackerel.io/ja/docs/entry/advanced/make-plugin-corresponding-to-installer)に書かれた仕様を満たしている必要があります。

Githubからインストールしたいときは、次のようにGithubのowner名とrepository名、さらにGithub Releasesのタグ名を指定します。
```
$ mkr plugin install <owner>/<repo>[@<release_tag>]
```

例えば [mackerelio/mackerel-plugin-sample](https://github.com/mackerelio/mackerel-plugin-sample) の[リリース](https://github.com/mackerelio/mackerel-plugin-sample/releases)から、`v0.0.1`のリリースをインストールしたい場合、次のようにします。プラグインの実行ファイルはデフォルトでは`/opt/mackerel-agent/plugins/bin`以下に配置されます。

```sh
$ sudo mkr plugin install mackerelio/mackerel-plugin-sample@v0.0.1
           Downloading https://github.com/mackerelio/mackerel-plugin-sample/releases/download/v0.0.1/mackerel-plugin-sample_darwin_amd64.zip
           Installing /opt/mackerel-agent/plugins/bin/mackerel-plugin-sample
           Successfully installed mackerelio/mackerel-plugin-sample@v0.0.1
$ /opt/mackerel-agent/plugins/bin/mackerel-plugin-sample
sample.dice.d6  1       1508917208
sample.dice.d20 16      1508917208
```

もしGithub Releasesのタグ名を省略した場合は、Github Releasesから最新のリリースを探してインストールします。最新のリリースを取得するためにGithubのAPIにアクセスするので、[後述](#setting-github-token)のとおりGithubのトークンを設定する必要があることにご注意ください。

```sh
$ sudo mkr plugin install mackerelio/mackerel-plugin-sample
# 最新のリリースのv0.0.1がインストールされる
```

## レジストリに登録されたプラグインをインストールする
Mackerelでは有益なプラグインを探しやすいように公式で[プラグインレジストリ](https://github.com/mackerelio/plugin-registry)を用意しています。[plugins/ディレクトリ以下](https://github.com/mackerelio/plugin-registry/tree/master/plugins)に登録されているプラグインはmkrを使って簡単にインストールできます。

```sh
$ mkr plugin install <plugin_name>[@<release_tag>]
```

例えばレジストリには[mackerel-plugin-sample.json](https://github.com/mackerelio/plugin-registry/blob/master/plugins/mackerel-plugin-sample.json)というファイルがあるため、これをインストールするには次のようにします。

```sh
# mackerel-plugin-sampleのv0.0.1リリースをインストール
# 注) リリースタグはレジストリのsourceで定義された実際のGithubレポジトリを見に行く必要があります
$ sudo mkr plugin install mackerel-plugin-sample@v0.0.1
# mackerel-plugin-sampleの最新リリースをインストール
$ sudo mkr plugin install mackerel-plugin-sample
```

## プラグインを別の場所にインストールする
デフォルトではプラグインの実行ファイルは`/opt/mackerel-agent/plugins/bin`にインストールされますが、`--prefix`オプションを使うことで別の場所にインストール可能です。

```sh
$ mkr plugin install --prefix /path/to/plugin_dir mackerel-plugin-sample
# /path/to/plugin_dir/bin 以下にコマンドがインストールされる
```

## 指定したリリースタグのプラグインがインストールされていない場合のみインストールする
`--upgrade`オプションを利用することで、指定したリリースタグのプラグインがインストールされていない場合のみインストールできます。

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

このオプションを付けることで無意味なダウンロードが避けられるため、プロビジョニングツールでのプラグインインストール時にはupgradeオプションを付けることをおすすめします。

なお、upgradeオプションを利用するとダウングレードをすることもできます。たとえば、v0.0.2がインストールされている状況でv0.0.1を指定して実行すると、v0.0.1をインストールできます。

## 既に同じ名前の実行ファイルがあっても上書きする
デフォルトではmkrは同じ名前の実行ファイルがインストール先にあった場合、そのファイルのインストールをスキップします。`--overwrite`オプションを付けることで、スキップせずに必ず上書きすることが出来ます。

```sh
# 既に実行ファイルが存在する時スキップする
$ sudo mkr plugin install mackerel-plugin-sample
           Downloading https://github.com/mackerelio/mackerel-plugin-sample/releases/download/v0.0.1/mackerel-plugin-sample_darwin_amd64.zip
           /opt/mackerel-agent/plugins/bin/mackerel-plugin-sample already exists. Skip installing for now
           Successfully installed mackerel-plugin-sample
# --overwriteオプション付きなら強制的に上書きする
$ sudo mkr plugin install mackerel-plugin-sample --overwrite
           Downloading https://github.com/mackerelio/mackerel-plugin-sample/releases/download/v0.0.1/mackerel-plugin-sample_darwin_amd64.zip
           Installing /opt/mackerel-agent/plugins/bin/mackerel-plugin-sample
           Successfully installed mackerel-plugin-sample
```

このオプションは同一バージョンのプラグインを再インストールしたい場合に有用です。それ以外の場合は`--upgrade`オプションをご利用ください。

<h2 id="setting-github-token">Githubのトークンを設定する</h2>

`mkr plugin install`はGithubから最新のリリースを探すためにGithub APIを利用します。そのため、[Githubの設定画面](https://github.com/settings/tokens)から取得できるアクセストークンを指定しておかなければ、[Github APIのRate Limit](https://developer.github.com/v3/#rate-limiting)の制限にあたり、インストールが失敗する可能性があります。

mkrでは`GITHUB_TOKEN`環境変数もしくは`.gitconfig`の`github.token`をアクセストークンとして利用します。このどちらかを指定してください。

```sh
# 環境変数での設定
$ export GITHUB_TOKEN=<github_access_token>
# .gitconfigでの設定
$ git config --global github.token <github_access_token>
```

## サーバプロビジョニングツールからmkr plugin installを使うときは
Githubのアクセストークンを設定したとしても、ChefやAnsibleなどのサーバプロビジョニングツールを使って大量のサーバから一斉に`mkr plugin install`した場合、Github APIのRate Limitの制限にかかる事があります。

`mkr plugin install`ではGithub Releasesのリリースタグが明示的に指定された場合、GithubのAPIにアクセスしないため、Rate Limitの制限にかかることはありません。そのため、サーバプロビジョニングツールから利用するときは、リリースタグを明示的に指定することをおすすめします。

## mkr plugin installでインストールできるプラグインを作成する
`mkr plugin install`でインストールできるプラグインを作成したい場合、次のドキュメントを参照してください。

- [go-mackerel-pluginを利用してカスタムメトリックプラグインを作成する](https://mackerel.io/ja/docs/entry/advanced/go-mackerel-plugin)
- [mkr plugin installに対応したプラグインを作成する](https://mackerel.io/ja/docs/entry/advanced/make-plugin-corresponding-to-installer)

有益なプラグインを作成したら、是非インストーラへの対応や[プラグインレジストリ](https://github.com/mackerelio/plugin-registry)への登録をして頂けたらと思います。
