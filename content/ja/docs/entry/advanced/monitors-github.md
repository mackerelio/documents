---
Title: 監視ルールをGitHubで管理しよう
Date: 2015-08-13T10:52:15+09:00
URL: https://mackerel.io/ja/docs/entry/advanced/monitors-github
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/8454420450105423570
---

mkr を使うことで監視ルールをローカルファイルに保存したり(`pull`)、ローカルファイルとMackerelの設定の差分を確認したり(`diff`)、ローカルファイルの内容をMackerelの設定に反映させる(`push`)ことができます。

各コマンドの詳細は`cli`の[ヘルプ](./cli)を参照してください。

ここでは mkr を利用して[GitHub](https://github.com/)で監視ルールを管理する方法を紹介します。

## mkr による監視ルールの管理

mkr による監視ルールの管理方法には、WebUIとコードの両方で変更を行うか、それともコードのみで変更するかどうかで大きく次の2パターンがあります。

- WebUIとコードの両方で監視ルールを変更する
  - `pull`と`push`を利用する
- WebUIでは変更せず、Codeのみで監視ルールを変更する
  - `push`のみを利用し、`pull`は利用しない

mkr monitors では monitor rule の特定に`id`もしくは`name`を利用します。
`id`は監視ルールを新規登録した時にMackerel側で付与するものです。
`name`はユーザーが命名します。

前者では`id`ベースで管理しますので、管理するjsonファイルにも`id`を含める必要があります。
後者は`name`ベースで管理しますので、jsonに`id`を含める必要がありません。ただし`name`が重複していないことが必要です。

もしjsonやMackerel側で`name`が重複していた場合、mkrは`id`ベースで監視ルールを特定しようとします。
`name`が重複しているにもかかわらず、jsonの各監視ルールに`id`が存在しない場合はmkrは不正なjsonと判定します。

## `pull`と`push`を利用する

`id`ベースで監視ルールを管理します。そのため監視ルールを新規登録した後で、Mackerel側で付与された`id`を取得する手順が必要となります。

### 初期化
- GitHubにリポジトリを作成する
- git clone する
  ```
  git clone <repo-url>
  cd <repo-path>
  ```
- 監視ルールをMackerelから取得する
  ```
  mkr monitors pull
  ```
- GitHub上のリポジトリにコミット・プッシュする
  ```
  git add monitors.json
  git commit -m '<commit-msg>'
  git push
  ```

### リポジトリとMackerelの設定の同期が取れていることの確認
- GitHubから最新のデータをもってくる
  ```
  cd <repo-path>
  git pull
  ```
- Mackerelとの差分を確認する
  ```
  mkr monitors diff
  ```
  差分がない場合、以下のような結果が得られます
  ```
  Summary: 0 modify, 0 append, 0 remove
  ```

### ルールを変更する(Web)
- Web側で監視ルールを変更する
- 変更された監視ルールをMackerelから取得する
  ```
  cd <repo-path>
  mkr monitors pull
  ```
- GitHub上のリポジトリにコミット・プッシュする
  ```
  git add monitors.json
  git commit -m '<commit-msg>'
  git push
  ```

### 既存の監視ルールを変更する(Code)
- GitHub上で変更・レビューなどを行い、masterブランチ(など)に反映する
- 変更された監視ルールをMackerelから取得する
  ```
  cd <repo-path>
  mkr monitors pull
  ```
- Mackerelとの差分を確認する
  ```
  mkr monitors diff
  ```
- 変更された監視ルールをMackerelに反映する
  ```
  mkr monitors push
  ```

### 監視ルールを新規追加する(Code)
- jsonを作成後、GitHub上で変更・レビューなどを行い、masterブランチ(など)に反映する
  - ここでのjsonはidなしのものを作成
  - jsonフォーマットについては、[API仕様の「監視ルールの登録」](https://mackerel.io/ja/api-docs/entry/monitors#create)を参照してください
- Mackerelとの差分を確認する
  ```
  mkr monitors diff
  ```
- 追加された監視ルールをMackerelに反映する
  ```
  mkr monitors push
  ```
- 追加された監視ルールにMackerelが付与した`id`を付きで取得する
  ```
  mkr monitors pull
  ```
- GitHub上のリポジトリにコミット・プッシュする
  ```
  git add monitors.json
  git commit -m '<commit-msg>'
  git push
  ```

## `push`のみを利用する

`name`ベースで監視ルールを管理します。
各監視ルールで`name`が重複しないようにする必要があります。
またWebUI側での監視ルールの変更が非推奨となります。
もし変更してしまった場合は手動でjsonを組み立てるか、pullを実行する必要があります。

### 初期化
- GitHubにリポジトリを作成する
- git clone する
  ```
  git clone <repo-url>
  cd <repo-path>
  ```
- 監視ルールをMackerelから取得する
  ```
  mkr monitors pull
  ```
- GitHub上のリポジトリにコミット・プッシュする
  ```
  git add monitors.json
  git commit -m '<commit-msg>'
  git push
  ```

### リポジトリとMackerelの設定の同期が取れていることの確認
- GitHubから最新のデータをもってくる
  ```
  cd <repo-path>
  git pull
  ```
- Mackerelとの差分を確認する
  ```
  mkr monitors diff
  ```
  差分がない場合、以下のような結果が得られます
  ```
  Summary: 0 modify, 0 append, 0 remove
  ```

### 既存の監視ルールを変更する(Code)
- GitHub上で変更・レビューなどを行い、masterブランチ(など)に反映する
- 変更された監視ルールをMackerelから取得する
  ```
  cd <repo-path>
  mkr monitors pull
  ```
- Mackerelとの差分を確認する
  ```
  mkr monitors diff
  ```
- 変更された監視ルールをMackerelに反映する
  ```
  mkr monitors push
  ```

### 監視ルールを新規追加する(Code)
- jsonを作成後、GitHub上で変更・レビューなどを行い、masterブランチ(など)に反映する
  - ここでのjsonはidなしのものを作成
  - jsonフォーマットについては、[API仕様の「監視ルールの登録」](https://mackerel.io/ja/api-docs/entry/monitors#create)を参照してください
- Mackerelとの差分を確認する
  ```
  mkr monitors diff
  ```
- 追加された監視ルールをMackerelに反映する
  ```
  mkr monitors push
  ```
  - `name`ベースで監視ルールの同一判定をするため、`id`反映のためのpullは不要となります

### Mackerel側のルールを反映する

WebUI側で変更してしまった場合は、次のフローで最新化します。

- 変更された監視ルールをMackerelから取得する
  ```
  cd <repo-path>
  mkr monitors pull
  ```
  - pullすることで`id`付きのjsonとなる
- jsonから`id`フィールドを削除する
  - `id`フィールドが存在しても動作に支障はありませんので、放置しても問題はありません。
- Mackerelとの差分を確認する
  ```
  mkr monitors diff
  ```
- GitHub上のリポジトリにコミット・プッシュする
  ```
  git add monitors.json
  git commit -m '<commit-msg>'
  git push
  ```
