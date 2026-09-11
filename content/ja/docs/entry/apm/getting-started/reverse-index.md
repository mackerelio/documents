---
Title: Mackerel APM導入ガイド ― 逆引きリファレンス
Date: 2026-09-11T11:02:49+09:00
URL: https://mackerel.io/ja/docs/entry/apm/getting-started/reverse-index
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs-ja.hatenablog.mackerel.io/atom/entry/14945776032076293750
---

「こういうことをしたい」という目的から、該当するセクションを引くためのリファレンスです。

[:contents]

## APMの基本を理解する

- APMとは何か、全体像を知りたい → [Mackerel APM の基礎理解](./mackerel-apm-basics)
- APMとインフラ監視の違いを知りたい → [インフラ監視との違い](./mackerel-apm-basics#インフラ監視との違い)
- トレース・スパン・メトリックの概念を理解したい → [APMが扱うデータの種類](./mackerel-apm-basics#APMが扱うデータの種類)
- REDメトリックとは何か知りたい → [REDメトリック](./mackerel-apm-basics#REDメトリック)
- サービスマップで何がわかるか知りたい → [サービスマップ](./mackerel-apm-basics#サービスマップ)
- 分散トレーシングの仕組みを理解したい → [分散トレーシング](./mackerel-apm-basics#分散トレーシング)
- OpenTelemetryとは何か知りたい → [OpenTelemetryによるデータ収集](./mackerel-apm-basics#OpenTelemetryによるデータ収集)
- 計装方法の種類（ゼロコード・コードベース）を比較したい → [計装の方法](./mackerel-apm-basics#計装の方法)
- Mackerel APMとサーバー監視の関係を理解したい → [Mackerelのサーバー監視との関係](./mackerel-apm-basics#Mackerelのサーバー監視との関係)
- OpenTelemetry標準規格への対応について知りたい → [OpenTelemetry標準規格への対応](./mackerel-apm-basics#OpenTelemetry標準規格への対応)
- システムの複雑化に対してAPMがなぜ有効か知りたい → [なぜ今APMが必要なのか](./mackerel-apm-basics#なぜ今APMが必要なのか)
- 「どこが遅いかわからない」をAPMで解決したい → [「どこが遅いかわからない」問題](./mackerel-apm-basics#どこが遅いかわからない問題)
- 「エラーの原因がつかめない」をAPMで解決したい → [「エラーの原因がつかめない」問題](./mackerel-apm-basics#エラーの原因がつかめない問題)
- パフォーマンスの傾向を可視化したい → [「パフォーマンスの傾向が見えない」問題](./mackerel-apm-basics#パフォーマンスの傾向が見えない問題)
- チーム間の認識のずれを解消したい → [「チーム間の認識がずれる」問題](./mackerel-apm-basics#チーム間の認識がずれる問題)
- Mackerel OpenTelemetryコレクターの概要を知りたい → [Mackerel OpenTelemetryコレクター](./mackerel-apm-basics#Mackerel-OpenTelemetryコレクター)
- クライアントトークンの設定方法を知りたい → [クライアントトークン](./mackerel-apm-basics#クライアントトークン)

## 最初のトレースを取得する

- ゼロコード計装の種類と違いを知りたい → [ゼロコード計装とは](./zero-code-instrumentation#ゼロコード計装とは)
- ライブラリ計装の仕組みを知りたい → [ライブラリ計装](./zero-code-instrumentation#ライブラリ計装)
- OBI（eBPF計装）の仕組みを知りたい → [OBI（OpenTelemetry eBPF Instrumentation）](./zero-code-instrumentation#OBIOpenTelemetry-eBPF-Instrumentation)
- ライブラリ計装とOBIを比較したい → [計装方式の比較](./zero-code-instrumentation#計装方式の比較)
- ライブラリ計装とOBIの使い分けを知りたい → [使い分けの指針](./zero-code-instrumentation#使い分けの指針)
- OBIのメリットと制約を知りたい → [メリット](./zero-code-instrumentation#メリット)、[制約](./zero-code-instrumentation#制約)
- ゼロコード計装で何が取れるか具体的に知りたい → [ゼロコード計装で取得できるデータの例](./zero-code-instrumentation#ゼロコード計装で取得できるデータの例)
- Mackerelのトライアルを始めたい → [Mackerelのトライアルオーガニゼーションを用意する](./zero-code-instrumentation#Mackerelのトライアルオーガニゼーションを用意する)
- コレクターをインストールしたい → [Mackerel OpenTelemetryコレクターの準備](./zero-code-instrumentation#Mackerel-OpenTelemetryコレクターの準備)
- 検証環境の前提条件を確認したい → [検証環境の前提条件](./zero-code-instrumentation#検証環境の前提条件)
- 検証時の安全なガードレールを設定したい → [安全な検証のためのガードレール](./zero-code-instrumentation#安全な検証のためのガードレール)
- 環境変数でエクスポート先を設定したい → [環境変数による設定](./zero-code-instrumentation#環境変数による設定)
- 直接送信とコレクター経由の違いを知りたい → [送信先 ― Mackerel OpenTelemetry コレクター経由を推奨](./zero-code-instrumentation#送信先--Mackerel-OpenTelemetry-コレクター経由を推奨)
- コンテナ環境（ECS・Kubernetes）で計装したい → [コンテナ環境での計装（ECS・Kubernetes）](./zero-code-instrumentation#コンテナ環境での計装ECSKubernetes)
- コンテナイメージに計装ライブラリを組み込みたい → [コンテナイメージへの計装ライブラリの組み込み](./zero-code-instrumentation#コンテナイメージへの計装ライブラリの組み込み)
- コンテナ環境でのコレクターの配置を知りたい → [コレクターの配置](./zero-code-instrumentation#コレクターの配置)
- 各言語のセットアップを概要レベルで比較したい → [言語別のセットアップ概要](./zero-code-instrumentation#言語別のセットアップ概要)
- 最初のトレースをMackerel APM画面で確認したい → [最初のトレースを確認してみよう](./zero-code-instrumentation#最初のトレースを確認してみよう)
- トレースとスパンの読み方を知りたい → [何が見えるか ― トレースとスパンの読み解き方](./zero-code-instrumentation#何が見えるか--トレースとスパンの読み解き方)

## 言語・フレームワーク別にセットアップする

##### Node.js

- セットアップ手順の全体 → [Node.js におけるゼロコード計装](./nodejs-zero-code-instrumentation)
- パッケージのインストール → [パッケージのインストール](./nodejs-zero-code-instrumentation#1-パッケージのインストール)
- 環境変数の設定 → [環境変数の設定](./nodejs-zero-code-instrumentation#2-環境変数の設定)
- 自動計装の対象ライブラリ → [自動計装の対象](./nodejs-zero-code-instrumentation#自動計装の対象)
- ESM（import構文）を使っている → [ESMモジュール（import構文）を使っている場合](./nodejs-zero-code-instrumentation#ESMモジュールimport構文を使っている場合)
- 特定ライブラリの計装を無効化したい → [自動計装の対象](./nodejs-zero-code-instrumentation#自動計装の対象)

##### PHP

- セットアップ手順の全体 → [PHPにおけるゼロコード計装](./php-zero-code-instrumentation)
- PHP拡張のインストール → [PHP拡張のインストール](./php-zero-code-instrumentation#1-PHP拡張のインストール)
- 計装ライブラリのインストール → [計装ライブラリのインストール](./php-zero-code-instrumentation#2-計装ライブラリのインストール)
- 自動計装の対象ライブラリ → [自動計装の対象](./php-zero-code-instrumentation#自動計装の対象)
- PHP-FPM環境での注意点 → [PHP-FPMを使っている場合](./php-zero-code-instrumentation#PHP-FPMを使っている場合)

##### Java（Java Agent）

- セットアップ手順の全体 → [Java Agent によるゼロコード計装](./java-agent-instrumentation)
- Java Agentのダウンロード → [Java Agentのダウンロード](./java-agent-instrumentation#1-Java-Agentのダウンロード)
- 自動計装の対象ライブラリ → [自動計装の対象](./java-agent-instrumentation#自動計装の対象)
- Java AgentとSpring Boot Starterの使い分け → [Java AgentとSpring Boot Starterの使い分け](./java-agent-instrumentation#Java-AgentとSpring-Boot-Starterの使い分け)
- Tomcat などサーブレットコンテナで使いたい → [サーブレットコンテナ（Tomcat など）の場合](./java-agent-instrumentation#サーブレットコンテナTomcat-などの場合)
- 特定ライブラリの計装を無効化したい → [自動計装の対象](./java-agent-instrumentation#自動計装の対象)

##### Spring Boot

- セットアップ手順の全体 → [Spring Boot における OpenTelemetry 計装](./spring-boot-instrumentation)
- 依存関係の追加 → [依存関係の追加](./spring-boot-instrumentation#1-依存関係の追加)
- Spring Initializrを使う → [Spring Initializrを使う場合](./spring-boot-instrumentation#Spring-Initializrを使う場合)
- 設定ファイルの編集 → [設定ファイルの編集](./spring-boot-instrumentation#2-設定ファイルの編集)
- 自動計装の対象ライブラリ → [自動計装の対象](./spring-boot-instrumentation#自動計装の対象)

##### Python

- セットアップ手順の全体 → [Python におけるゼロコード計装](./python-zero-code-instrumentation)
- パッケージのインストール → [パッケージのインストール](./python-zero-code-instrumentation#1-パッケージのインストール)
- 計装ライブラリの自動インストール → [計装ライブラリの自動インストール](./python-zero-code-instrumentation#2-計装ライブラリの自動インストール)
- 自動計装の対象ライブラリ → [自動計装の対象](./python-zero-code-instrumentation#自動計装の対象)
- 仮想環境での注意 → [仮想環境を使っている場合](./python-zero-code-instrumentation#仮想環境を使っている場合)
- 特定ライブラリの計装を無効化したい → [自動計装の対象](./python-zero-code-instrumentation#自動計装の対象)

##### .NET

- セットアップ手順の全体 → [.NET におけるゼロコード計装](./dotnet-zero-code-instrumentation)
- Linux / macOSでのセットアップ → [Linux / macOSの場合](./dotnet-zero-code-instrumentation#Linux--macOSの場合)
- Windowsでのセットアップ → [Windowsの場合](./dotnet-zero-code-instrumentation#Windowsの場合)
- 自動計装の対象ライブラリ → [自動計装の対象](./dotnet-zero-code-instrumentation#自動計装の対象)
- self-containedデプロイメントの場合 → [self-contained デプロイメントの場合](./dotnet-zero-code-instrumentation#self-contained-デプロイメントの場合)
- 特定ライブラリの計装を無効化したい → [自動計装の対象](./dotnet-zero-code-instrumentation#自動計装の対象)

##### Ruby on Rails

- セットアップ手順の全体 → [Ruby on Rails における OpenTelemetry 計装](./rails-instrumentation)
- Gemのインストール → [Gemのインストール](./rails-instrumentation#1-Gemのインストール)
- イニシャライザーの作成 → [イニシャライザーの作成](./rails-instrumentation#2-イニシャライザーの作成)
- 自動計装の対象ライブラリ → [自動計装の対象](./rails-instrumentation#自動計装の対象)
- 個別の計装ライブラリを選択する → [個別の計装ライブラリを選択する場合](./rails-instrumentation#個別の計装ライブラリを選択する場合)
- Puma・Unicornとの組み合わせ → [PumaやUnicornとの組み合わせ](./rails-instrumentation#PumaやUnicornとの組み合わせ)

## コレクターを設定・運用する

- コレクターの役割を理解したい → [コレクターの役割](./otel-collector-setup#コレクターの役割)
- パイプライン（レシーバー・プロセッサー・エクスポーター）の仕組みを知りたい → [パイプラインの構成要素](./otel-collector-setup#パイプラインの構成要素)
- コレクターを導入するメリットを知りたい → [コレクターを導入するメリット](./otel-collector-setup#コレクターを導入するメリット)
- Mackerel OpenTelemetryコレクターの特徴を知りたい → [Mackerel専用ディストリビューション](./otel-collector-setup#Mackerel専用ディストリビューション)
- 設定ファイルなしでコレクターを使いたい → [最大の特徴：設定ファイル不要で始められる](./otel-collector-setup#最大の特徴設定ファイル不要で始められる)
- 環境変数でコレクターをカスタマイズしたい → [環境変数によるカスタマイズ](./otel-collector-setup#環境変数によるカスタマイズ)
- 組み込み済みのコンポーネント一覧を知りたい → [組み込み済みの主要コンポーネント](./otel-collector-setup#組み込み済みの主要コンポーネント)
- config.yamlを書いてカスタム設定にしたい → [デフォルト設定からカスタム設定へ ― config.yamlを書いてみる](./otel-collector-setup#デフォルト設定からカスタム設定へ--configyamlを書いてみる)
- いつカスタム設定が必要か判断したい → [いつカスタム設定が必要になるか](./otel-collector-setup#いつカスタム設定が必要になるか)
- 最小限のカスタム設定の書き方を知りたい → [最小限のカスタム設定](./otel-collector-setup#最小限のカスタム設定)
- プロセッサーを追加した構成にしたい → [プロセッサーを追加した構成](./otel-collector-setup#プロセッサーを追加した構成)
- カスタム設定でコレクターを起動したい → [カスタム設定での起動方法](./otel-collector-setup#カスタム設定での起動方法)
- コレクターの正常動作を確認したい → [正常動作の確認ポイント](./otel-collector-setup#正常動作の確認ポイント)
- サイドカー型・ゲートウェイ型の配置パターンを検討したい → [コレクターの配置パターン](./otel-collector-setup#コレクターの配置パターン)
- コレクターの可用性を確保したい → [可用性の確保](./otel-collector-setup#可用性の確保)
- Windows環境でコレクターを使いたい → [Windows環境での利用](./otel-collector-setup#Windows環境での利用)

## トレースデータを分析する

- 短期検証の計画を立てたい → [短期検証の進め方 ― 計画を立てる](./verification-analysis#短期検証の進め方--計画を立てる)
- 検証の目的を整理したい → [検証の目的を明確にする](./verification-analysis#検証の目的を明確にする)
- 検証期間をどのくらいにすべきか知りたい → [検証期間の設定](./verification-analysis#検証期間の設定)
- 検証対象サービスの選び方を知りたい → [検証対象の選び方](./verification-analysis#検証対象の選び方)
- APM画面の全体構成を知りたい → [Mackerel APMの画面構成](./verification-analysis#Mackerel-APMの画面構成)
- フィルターの使い方を知りたい → [フィルター](./verification-analysis#フィルター)
- データの保持期間を知りたい → [データの保持期間](./verification-analysis#データの保持期間)
- REDメトリックのグラフで全体像を把握したい → [サマリータブ ― REDメトリックで全体像を把握する](./verification-analysis#サマリータブ--REDメトリックで全体像を把握する)
- トレース一覧からリクエストの流れを追いたい → [トレースタブ ― リクエストの流れを追う](./verification-analysis#トレースタブ--リクエストの流れを追う)
- トレースの集計機能を使いたい → [集計機能](./verification-analysis#集計機能)
- 個別のトレースを詳しく見たい → [個別トレースを展開する](./verification-analysis#個別トレースを展開する)
- エンドポイントごとの統計を見たい → [HTTPサーバータブ ― エンドポイントごとの統計](./verification-analysis#HTTPサーバータブ--エンドポイントごとの統計)
- データベースクエリの統計を見たい → [データベースタブ ― クエリごとの統計](./verification-analysis#データベースタブ--クエリごとの統計)
- エラーを発見・追跡したい → [課題タブ ― エラーの発見と追跡](./verification-analysis#課題タブ--エラーの発見と追跡)
- サービス間の依存関係を把握したい → [サービスマップタブ ― サービス間の関係を俯瞰する](./verification-analysis#サービスマップタブ--サービス間の関係を俯瞰する)
- 検証中に試してみるとよいことを知りたい → [検証中に試してみるとよいこと](./verification-analysis#検証中に試してみるとよいこと)
- ボトルネックを特定したい → [パフォーマンスのボトルネック特定](./verification-analysis#パフォーマンスのボトルネック特定)
- 障害時の原因特定・復旧を早めたい → [障害時の原因特定と復旧時間の短縮](./verification-analysis#障害時の原因特定と復旧時間の短縮)
- APMのデータでチーム間の認識を合わせたい → [チーム間の認識合わせと意思決定](./verification-analysis#チーム間の認識合わせと意思決定)

## コストを見積もる

- APMのコスト構造を理解したい → [APMのコスト構造を理解する](./cost-estimation#APMのコスト構造を理解する)
- コストが何で決まるか知りたい → [コストは何で決まるのか](./cost-estimation#コストは何で決まるのか)
- スパン数が増える要因を知りたい → [スパン数が増える要因](./cost-estimation#スパン数が増える要因)
- 「1リクエストあたりのスパン数」の考え方を知りたい → [「1リクエストあたりのスパン数」という考え方](./cost-estimation#1リクエストあたりのスパン数という考え方)
- 検証データからスパン数を確認したい → [検証データからスパン数を確認する](./cost-estimation#検証データからスパン数を確認する)
- オーガニゼーションの利用状況でスパン日次推移を見たい → [方法1：オーガニゼーションの利用状況タブで日次推移を見る](./cost-estimation#方法1オーガニゼーションの利用状況タブで日次推移を見る)
- メトリックエクスプローラーで分単位の投稿量を見たい → [方法2：メトリックエクスプローラーで分単位の投稿量を見る](./cost-estimation#方法2メトリックエクスプローラーで分単位の投稿量を見る)
- APMのトレースタブでスパン数推移を見たい → [方法3：APMのトレースタブでスパン数の推移を見る](./cost-estimation#方法3APMのトレースタブでスパン数の推移を見る)
- 本番トラフィックでのコストを概算したい → [本番規模のコストを見積もる](./cost-estimation#本番規模のコストを見積もる)
- 月額コストを算出したい → [見積もり結果から月額コストを算出する](./cost-estimation#見積もり結果から月額コストを算出する)
- コストが想定を超えたときの選択肢を知りたい → [コストが想定を超えたら ― 最適化の選択肢](./cost-estimation#コストが想定を超えたら--最適化の選択肢)
- 最適化後のコスト見込みを概算したい → [最適化後のコスト見込みを概算する](./cost-estimation#最適化後のコスト見込みを概算する)
- 導入判断の資料をまとめたい → [導入判断の整理 ― チームで共有するために](./cost-estimation#導入判断の整理--チームで共有するために)
- 検証結果のまとめ方を知りたい → [検証結果をまとめる観点](./cost-estimation#検証結果をまとめる観点)
- 関係者（マネージャー・開発者）向けに情報を整理したい → [関係者ごとの関心事に応える情報整理](./cost-estimation#関係者ごとの関心事に応える情報整理)
- 段階的な導入を提案したい → [段階的な導入の提案](./cost-estimation#段階的な導入の提案)

## データ量・コストを最適化する

- なぜデータを絞る必要があるか知りたい → [なぜデータを絞る必要があるのか](./filtering-and-sampling#なぜデータを絞る必要があるのか)
- 計装レベルでスパンを減らしたい → [計装レベルでの制御もある](./filtering-and-sampling#計装レベルでの制御もある)
- フィルタリングとサンプリングの違いを知りたい → [フィルタリングとサンプリング ― 2つのアプローチ](./filtering-and-sampling#フィルタリングとサンプリング--2つのアプローチ)
- フィルタリングの仕組みを知りたい → [フィルタリングとは](./filtering-and-sampling#フィルタリングとは)
- サンプリングの仕組みを知りたい → [サンプリングとは](./filtering-and-sampling#サンプリングとは)
- フィルタリングとサンプリングの使い分けを知りたい → [フィルタリングとサンプリングの使い分け](./filtering-and-sampling#フィルタリングとサンプリングの使い分け)
- ヘッドベースサンプリングの仕組みと設定を知りたい → [ヘッドベースサンプリング](./filtering-and-sampling#ヘッドベースサンプリング)
- コレクターの環境変数でヘッドサンプリングを設定したい → [方法1：Mackerel OpenTelemetryコレクターの環境変数で設定する](./filtering-and-sampling#方法1Mackerel-OpenTelemetryコレクターの環境変数で設定する)
- アプリケーション側の環境変数でサンプリングを設定したい → [方法2：アプリケーション側の環境変数で設定する](./filtering-and-sampling#方法2アプリケーション側の環境変数で設定する)
- テイルベースサンプリングの仕組みと設定を知りたい → [テイルベースサンプリング](./filtering-and-sampling#テイルベースサンプリング)
- ヘッドベースとテイルベースのどちらを選ぶか判断したい → [どちらを選ぶか](./filtering-and-sampling#どちらを選ぶか)
- サンプリングと統計のバイアスについて知りたい → [サンプリングと統計のバイアス](./filtering-and-sampling#サンプリングと統計のバイアス)
- 「残すべきデータ」と「削ってよいデータ」の判断基準を知りたい → [「残すべきデータ」と「削ってよいデータ」の判断基準](./filtering-and-sampling#残すべきデータと削ってよいデータの判断基準)
- ヘルスチェックなど不要なトレースを除外したい → [フィルタリングの設定例](./filtering-and-sampling#フィルタリングの設定例)
- テイルベースサンプリングを設定したい → [テイルベースサンプリングの設定例](./filtering-and-sampling#テイルベースサンプリングの設定例)
- フィルタリングとサンプリングを組み合わせた構成にしたい → [フィルタリングとサンプリングを組み合わせた構成](./filtering-and-sampling#フィルタリングとサンプリングを組み合わせた構成)
- 設定変更後の確認方法を知りたい → [設定変更後の確認](./filtering-and-sampling#設定変更後の確認)
- 最適化の効果を確認したい → [最適化の効果を確認する](./filtering-and-sampling#最適化の効果を確認する)
- コスト削減効果を算出したい → [コスト削減効果を算出する](./filtering-and-sampling#コスト削減効果を算出する)
- 最適化が可観測性に与える影響を確認したい → [可観測性への影響を確認する](./filtering-and-sampling#可観測性への影響を確認する)

## 本番導入の準備をする

- 導入前チェックリストを確認したい → [Mackerel APM 本番導入チェックリスト](./production-readiness-checklist)
- APMの基礎理解の確認 → [APMの基礎理解](./production-readiness-checklist#APMの基礎理解)
- データ取得と送信構成の確認 → [データ取得と送信構成](./production-readiness-checklist#データ取得と送信構成)
- 可視化とデータ分析の確認 → [可視化とデータ分析](./production-readiness-checklist#可視化とデータ分析)
- コストの見積もりの確認 → [コストの見積もり](./production-readiness-checklist#コストの見積もり)
- データ量・コストの最適化の確認 → [データ量・コストの最適化](./production-readiness-checklist#データ量コストの最適化)
- 本番導入の準備の確認 → [本番導入の準備](./production-readiness-checklist#本番導入の準備)

## トラブルシューティング

##### トレースが表示されない

- 全般的な確認手順 → [トレースが表示されない場合](./zero-code-instrumentation#トレースが表示されない場合)
- コレクターにデータが届かない場合 → [コレクターにデータが届かない場合](./otel-collector-setup#コレクターにデータが届かない場合)
- コレクターからMackerel APMにデータが届かない場合 → [コレクターは受信しているが、Mackerel APMにデータが届かない場合](./otel-collector-setup#コレクターは受信しているがMackerel-APMにデータが届かない場合)
- Node.js → [トレースが表示されない](./nodejs-zero-code-instrumentation#トレースが表示されない)
- PHP → [トレースが表示されない](./php-zero-code-instrumentation#トレースが表示されない)
- Java → [トレースが表示されない](./java-agent-instrumentation#トレースが表示されない)
- Spring Boot → [トレースが表示されない](./spring-boot-instrumentation#トレースが表示されない)
- Python → [トレースが表示されない](./python-zero-code-instrumentation#トレースが表示されない)
- .NET → [トレースが表示されない](./dotnet-zero-code-instrumentation#トレースが表示されない)
- Ruby on Rails → [トレースが表示されない](./rails-instrumentation#トレースが表示されない)

##### 期待したスパンが出ない・計装されない

- 全般的な確認手順 → [期待したスパンが出ない場合](./zero-code-instrumentation#期待したスパンが出ない場合)
- Node.js：特定ライブラリが計装されない → [特定のライブラリが計装されない](./nodejs-zero-code-instrumentation#特定のライブラリが計装されない)
- Java：特定ライブラリが計装されない → [特定のライブラリが計装されない](./java-agent-instrumentation#特定のライブラリが計装されない)

##### データ量の問題

- データ量が多すぎる → [データ量が多すぎると感じたら](./zero-code-instrumentation#データ量が多すぎると感じたら)

##### コレクターの問題

- 設定ファイルのエラー → [設定ファイルのエラー](./otel-collector-setup#設定ファイルのエラー)
- 正常動作の確認方法 → [正常動作の確認ポイント](./otel-collector-setup#正常動作の確認ポイント)

##### 言語固有の問題

- Node.js：起動時に警告が出る → [起動時に警告が出る](./nodejs-zero-code-instrumentation#起動時に警告が出る)
- PHP：Composerオートローダーが見つからない → [Composerのオートローダーが見つからない](./php-zero-code-instrumentation#Composerのオートローダーが見つからない)
- Java：起動が遅くなった → [起動が遅くなった](./java-agent-instrumentation#起動が遅くなった)
- Python：仮想環境での問題 → [仮想環境を使っている場合](./python-zero-code-instrumentation#仮想環境を使っている場合)
- .NET：self-containedデプロイメント → [self-contained デプロイメントの場合](./dotnet-zero-code-instrumentation#self-contained-デプロイメントの場合)
- Rails：PumaやUnicornとの組み合わせ → [PumaやUnicornとの組み合わせ](./rails-instrumentation#PumaやUnicornとの組み合わせ)

<nav>
<ul style="display:flex;justify-content:space-between;padding:0;margin:0">
<li style="list-style:none">前の記事：<a href="https://mackerel.io/ja/docs/entry/apm/getting-started/rails-instrumentation" rel="prev">Ruby on Rails における OpenTelemetry 計装</a></li>
</ul>
</nav>
