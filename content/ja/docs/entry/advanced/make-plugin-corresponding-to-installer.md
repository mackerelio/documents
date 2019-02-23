---
Title: mkr plugin installに対応したプラグインを作成する
Date: 2017-10-25T15:13:16+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/make-plugin-corresponding-to-installer
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8599973812311199209
---

Mackerelのプラグインを作った際に、プラグインインストーラの仕様に対応しておくと、`mkr plugin install`コマンドを利用して簡単にプラグインをインストール出来るようになります。このドキュメントではその対応方法や、作成したプラグインを公式レジストリに登録する方法について説明します。

mkrを使ったプラグインのインストール方法については[mkr plugin installでプラグインをインストールする](https://mackerel.io/ja/docs/entry/advanced/install-plugin-by-mkr)を参照してください。Goを使ったプラグインの実装方法については [go-mackerel-pluginを利用してカスタムメトリックプラグインを作成する](https://mackerel.io/ja/docs/entry/advanced/go-mackerel-plugin)を参照してください。

## 対応すべき仕様
`mkr plugin install`コマンドでインストールできるようにするためには、作成したプラグインのGithubレポジトリが以下の仕様を必ず満たす必要があります。

- プラグインのレポジトリのGithub Releasesでバージョンごとにリリースタグが切られ、そこに命名規則に従ってOS/Archごとのアーカイブがアップロードされていること
- アーカイブのファイル名が`(REPOSITORY_NAME)_(GOOS)_(GOARCH).zip`という命名規則に従っていること
    - GOOSやGOARCHに入る文字列は https://golang.org/doc/install/source#environment を参照してください
    - 例) https://github.com/mackerelio/mackerel-plugin-sample がプラグインのレポジトリとすると、64bit Linux用のアーカイブは`mackerel-plugin-sample_linux_amd64.zip`、32bit macOS用のアーカイブは `mackerel-plugin-sample_darwin_386.zip`
- アーカイブの中に、`mackerel-plugin-`または`check-`から始まる、実行権限があるファイルが存在すること

必須ではありませんが、次の仕様に従うことを推奨します。

- 1つのレポジトリが1つのプラグインの実行ファイルを提供すること
- レポジトリ名と実行ファイル名が一致していること
- Github Releasesにアップロードするリリースタグはvx.y.zという命名規則で付けること(例: v1.2.3)
- README.mdとLICENSEファイルがアーカイブに含まれること

また、必須の仕様を満たしてさえいれば、プラグインをGoで実装していなくても`mkr plugin install`コマンドでインストール可能です。

推奨仕様まで満たしたサンプルは[mackerelio/mackerel-plugin-sample](https://github.com/mackerelio/mackerel-plugin-sample)に置いています。こちらもご参照ください。

## プラグインが仕様を満たすように設定する
それでは実際に自作プラグインを推奨仕様に対応するように設定してみましょう。[mackerelio/mackerel-plugin-sample](https://github.com/mackerelio/mackerel-plugin-sample) のサンプルレポジトリを例に紹介します。

プラグインインストーラの仕様を満たすためには、実行ファイルの作成、リリースアーカイブの作成、Github Releasesへのリリースの三つを行う必要があります。mackerel-plugin-sampleはGoで実装されているため、実行ファイルとリリースアーカイブの作成は[goxz](https://github.com/Songmu/goxz)で、Github Releasesへのリリースは[ghr](https://github.com/tcnksm/ghr)で行います。今回はgoxz + ghrでリリースを行っていますが、仕様さえ満たしていればどんなツールを用いても問題ありません。

### goxzとghrをインストールする
まず必要なツールである、goxzとghrをインストールしておきます。インストールにはGoの実行環境が必要です。

```sh
$ go get -v -u github.com/Songmu/goxz/cmd/goxz
$ go get -v -u github.com/tcnksm/ghr
```

### 実行ファイルとリリースアーカイブの作成
続いて、goxzを用いて実行ファイルとリリースアーカイブの作成を行います。

プラグインインストーラが推奨している仕様を満たすため、以下のオプションを用いてgoxzを実行してください。そうすると、指定したディレクトリ以下にリリースアーカイブが作られます。

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

今回はwindows/darwin/linux OSのamd64/386 Archで、計6つのプラットフォームに対応した実行ファイルを作成しています。どのプラットフォームに対応するかはgoxzの`-os`と`-arch`というコマンドラインオプションで調整できるため、適宜調整してください。

goxzの設定について詳しくは[goxzの公式ドキュメント](https://github.com/Songmu/goxz)を参照してください。

### Github Releasesへのリリース
goxzによるリリースアーカイブの作成ができました。続いてこれらをGithub Releasesへリリースします。これはghrを使えば非常に簡単に行なえます。例えばmackerelio/mackerel-plugin-sampleにv0.0.1というリリースタグでリリースしたい場合、以下のコマンドを実行するだけです。

```console
$ GITHUB_TOKEN=... ghr -u mackerelio -r mackerel-plugin-sample v0.0.1 dist/v0.0.1
```

これで、Github Releasesのページで次のように表示されていたら成功です。

https://github.com/mackerelio/mackerel-plugin-sample/releases
<p><span itemscope="" itemtype="http://schema.org/Photograph"><img src="https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20171025/20171025162942.png" alt="f:id:mackerelio:20171025162942p:plain" title="f:id:mackerelio:20171025162942p:plain" class="hatena-fotolife" itemprop="image"></span></p>

### リリースユーティリティを作る
これまでの設定でプラグインインストーラの推奨仕様まで満たして、Github Releasesにリリースすることができました。しかし、手作業で上記の全ての作業を行なうと煩雑なので、作業を一気に行うリリースユーティリティを作っておくとさらに便利です。

https://github.com/mackerelio/mackerel-plugin-sample/blob/master/script/release
```sh
#!/bin/sh
set -e
latest_tag=$(git describe --abbrev=0 --tags)
goxz -d dist/$latest_tag -z -os windows,darwin,linux -arch amd64,386
ghr -u mackerelio -r mackerel-plugin-sample $latest_tag dist/$latest_tag
```

これを使うと次のコマンドのみでリリースできます。

```console
$ git tag v0.0.1
$ GITHUB_TOKEN=... script/release
```

### mkr plugin installでインストールできることを確認する
リリースが完了したら、最後に`mkr plugin install`でインストールできるか確認しましょう。

```console
$ sudo mkr plugin install mackerelio/mackerel-plugin-sample@v0.0.1
           Downloading https://github.com/mackerelio/mackerel-plugin-sample/releases/download/v0.0.1/mackerel-plugin-sample_darwin_amd64.zip
           Installing /opt/mackerel-agent/plugins/bin/mackerel-plugin-sample
           Successfully installed mackerelio/mackerel-plugin-sample@v0.0.1
$ /opt/mackerel-agent/plugins/bin/mackerel-plugin-sample
sample.dice.d6  1       1508917208
sample.dice.d20 16      1508917208
```

## 公式のプラグインレジストリに登録する
他のユーザーでも簡単に有益なプラグインを見つけられるように、Mackerelでは公式で[プラグインレジストリ](https://github.com/mackerelio/plugin-registry)を用意しています。プラグインレジストリに登録されたプラグインは`mkr plugin install <plugin_name>`という形式でインストール出来るようになります。

登録はプラグインレジストリのGithubレポジトリにPull Requestを送るだけです。

- `<plugin_name>.json`というファイルを`plugins/`ディレクトリ配下に作成する
- `source`というフィールドに`<github owner>/<github repo>`の形式でプラグインの場所を記述する
    - 指定したレポジトリのプラグインが`mkr plugin install`でインストールできる必要があります
- `description`というフィールドにそのプラグインの簡易的な説明を記述する
- 例) https://github.com/mackerelio/plugin-registry/blob/master/plugins/mackerel-plugin-sample.json

---

本ドキュメントでは、自作のプラグインを`mkr plugin install`に対応する方法と、公式のプラグインレジストリに登録する方法について説明しました。有益なプラグインで他のユーザーにも役立ちそうなものができたら、是非[プラグインレジストリ](https://github.com/mackerelio/plugin-registry)にPull Requestを送ってみてください。
