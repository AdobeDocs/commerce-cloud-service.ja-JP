---
title: ECE-Tools リリースノート
description: ECE-Tools パッケージの最新の改善点の一覧を参照してください。
recommendations: noDisplay, catalog
last-substantial-update: 2024-04-08T00:00:00Z
exl-id: a464b940-c56e-4a7c-9948-559539e25361
source-git-commit: e21f21e34f89b62842bd22c99ff5705f984898e0
workflow-type: tm+mt
source-wordcount: '2905'
ht-degree: 0%

---

# ECE-Tools リリースノート

The [ece-tools](https://github.com/magento/ece-tools) パッケージは、クラウドプロジェクトを管理およびデプロイするために設計された一連のスクリプトおよびツールです。 これらのリリースノートでは、このパッケージに対する最新の改善点を説明します。これは、 [Cloud Tools Suite for Commerce](cloud-tools-suite.md).

>[!NOTE]
>
>詳しくは、 [ECE-Tools をアップグレード](../dev-tools/update-package.md) を参照してください。 `ece-tools` パッケージ。

The `ece-tools` パッケージでは、次のリリースのバージョン管理シーケンスが使用されます。 `200<major>.<minor>.<patch>`

リリースノートには次の内容が含まれます。

- ![新しいアイコン](../../assets/new.svg) 新機能
- ![修正アイコン](../../assets/fix.svg) 修正点および改善点

<!--Add release notes below-->


## v2002.1.18 {#latest}

リリース日： 2024 年 4 月 8 日

- ![新しいアイコン](../../assets/new.svg) **PHP** — PHP 8.3 のサポートを追加しました。
- ![修正アイコン](../../assets/fix.svg) バリデーター — EOL バリデーターを更新しました。

## v2002.1.17

リリース日： 2024 年 1 月 17 日

- ![修正アイコン](../../assets/fix.svg) **Elasticsearchと OpenSearch のバリデーター**— LiveSearch が有効な場合に、誤ったメッセージが表示されて検索サービスがインストールされる問題を修正しました。<!-- MCLOUD-10167 -->
- ![修正アイコン](../../assets/fix.svg) **デプロイメントの警告** — 空でないフォルダーに関するデプロイメント警告が発生する問題を修正しました。<!-- MCLOUD-8958 -->

## v2002.1.16

リリース日： 2023 年 10 月 17 日

- ![新しいアイコン](../../assets/new.svg) **ENABLE_WEBHOOKS グローバル環境変数** — が追加されました。 [ENABLE_WEBHOOKS](../environment/variables-global.md#enable_webhooks) 外部エンドポイント（App Builder ランタイムアクションやサードパーティの在庫管理システムなど）に接続するための、コマース Web フックで使用するグローバル変数。

## v2002.1.15

リリース日： 2023 年 7 月 31 日

- ![修正アイコン](../../assets/fix.svg) **エラーコード** — エラーコードスキーマとエラーコードドキュメントジェネレーターを更新しました。
- ![修正アイコン](../../assets/fix.svg) **カスタム Redis モデルのバリデーター** — カスタム Redis バックエンドモデルのバリデーターを更新しました。 [キャッシュの設定の例を参照してください。](../environment/variables-deploy.md#cache_configuration).
- ![修正アイコン](../../assets/fix.svg) **RabbitMQのバリデーター**- RabbitMQ 3.11 のサポートを追加
- ![修正アイコン](../../assets/fix.svg) **誤ったリンクを修正しました。** — お知らせメールテンプレートのオンボーディングドキュメントへの誤ったリンクを修正しました。

## v2002.1.14

リリース日： 2023 年 3 月 11 日

- ![新しいアイコン](../../assets/new.svg) **PHP**—PHP 8.2 のサポートを追加しました。
- ![新しいアイコン](../../assets/new.svg) **サービスのバリデータ**—Commerce 2.4.6 の必須サービス用のバリデータを更新しました：MariaDB 10.6、Redis 7.0、PHP 8.2、OpenSearch 2.x、RabbitMQ 3.9。
- ![修正アイコン](../../assets/fix.svg) **ece-tools db-dump**— `db-dump` 処理を早めに停止する必要があります。

## v2002.1.13

リリース日： 2022 年 10 月 27 日

- ![新しいアイコン](../../assets/new.svg) **Adobe CommerceのAdobe I/Oイベントのサポートを追加**. 拡張機能の開発者は、 [Adobe I/Oイベント](https://developer.adobe.com/events/docs/) コマースイベント情報をクラウドインスタンスから、次の目的で作成されたアプリケーションに送信するためのフレームワーク [AdobeApp Builder](https://developer.adobe.com/app-builder/docs/overview/). Adobe CommerceのAdobe I/Oイベントがパートナープレビューに表示されます。<!-- CEXT-932 -->
- ![新しいアイコン](../../assets/new.svg) **OPcache 設定のバリデーター** — 除外されたパスの OPcache 設定を確認するバリデーターを追加しました。<!-- MCLOUD-9485 -->
- ![修正アイコン](../../assets/fix.svg) **GraphQLキャッシュ設定の問題を修正しました**-ECE-Tools がGraphQLを保持するようになりました `id_salt` 値 `cache` 設定 `app/etc/env.php` ファイル。<!-- MCLOUD-9486 -->

## v2002.1.12

リリース日： 2022 年 9 月 14 日

- ![新しいアイコン](../../assets/new.svg) **有効にする`synchronous_replication`**—ECE-Tools セット `synchronous_replication=>true` （内） `app/etc/env.php` 次の場合に `MYSQL_USE_SLAVE_CONNECTION` が有効になっている。 この設定は Commerce 2.4.6 以降にのみ影響します。 詳しくは、 `MYSQL_USE_SLAVE_CONNECTION` 変数の説明 [変数をデプロイ](../environment/variables-deploy.md#mysql_use_slave_connection).<!-- MCLOUD-9142 -->
- ![新しいアイコン](../../assets/new.svg) **OpenSearch** — を設定および設定する機能が追加されました。 `opensearch` 次のAdobe Commerceリリース 2.4.6 のエンジン。詳しくは、 [OpenSearch サービスを設定する](../services/opensearch.md).<!-- MCLOUD-9236 -->

## v2002.1.11

リリース日： 2022 年 8 月 5 日

- ![修正アイコン](../../assets/fix.svg) **ElasticSuite バリデーターと OpenSearch**— OpenSearch がインストールされている場合の ElasticSuite 整合性チェックバリデーターの問題を修正しました。<!-- MCLOUD-8767 -->
- ![修正アイコン](../../assets/fix.svg) **配置コマンドの型を返す** — デプロイコマンドの戻り値の型を修正しました。<!-- AC-3208 -->
- ![修正アイコン](../../assets/fix.svg) **[!DNL RabbitMQ]新しい Commerce 2.4.5 のインストールに関する問題** — 固定 [!DNL RabbitMQ] 新しい Commerce 2.4.5 のインストール時にクラッシュする問題。<!-- MCLOUD-9059 -->

## v2002.1.10

リリース日： 2022 年 3 月 31 日

- ![修正アイコン](../../assets/fix.svg) **ELASTICSEARCH7.10** — バリデータを更新して、7.10 バージョンのElasticsearchをサポートしました。<!-- MCLOUD-8548 -->

## v2002.1.9

リリース日： 2022 年 3 月 11 日

- ![新しいアイコン](../../assets/new.svg) **OpenSearch**—Adobe Commerceバージョン 2.4.4、2.4.3-p2、2.3.7-p3 に対する OpenSearch のサポートが追加されました。<!-- MCLOUD-8296 -->
- ![新しいアイコン](../../assets/new.svg) **PHP**—PHP 8.1 のサポートを追加しました。
- ![修正アイコン](../../assets/fix.svg) **symfony/process**—symfony/process ^5.3 との互換性を追加しました。<!-- MCLOUD-8283 -->

- ![新しいアイコン](../../assets/new.svg) **複数のプロセスを消費** — が追加されました。 `multiple_processes` 」オプションを使用して、各コンシューマーに対して生成されるプロセスの数を指定できます。 詳しくは、 `CRON_CONSUMERS_RUNNER` 変数の説明 [変数をデプロイ](../environment/variables-deploy.md#cron_consumers_runner).<!-- MCLOUD-8295 -->
- ![新しいアイコン](../../assets/new.svg) **OpenSearch スキームと完全ホストパス**—Elasticsearch・スキームとフル・ホスト・パスを構成する機能が追加されました。
- ![修正アイコン](../../assets/fix.svg) **AWS S3**— AWS S3 の有効化の方法を変更しました。
- ![修正アイコン](../../assets/fix.svg) **driver_options リーダーを修正**—DB 接続用の driver_options 設定を `env.php` 次のファイルを作成 `ece-tools` バリデータ用。<!-- MCLOUD-8420 -->

## v2002.1.8

リリース日： 2021 年 10 月 26 日

- ![新しいアイコン](../../assets/new.svg) **代替ダンプの場所** — が追加されました。 `--dump-directory` オプションを使用して、DB ダンプのターゲットディレクトリを選択できます。 今すぐ `/app/var/dump-main` は、DB ダンプのデフォルトのターゲットディレクトリです。 詳しくは、 [バックアップ管理：データベースのダンプ](../storage/database-dump.md)<!-- MCLOUD-8063 -->
- ![修正アイコン](../../assets/fix.svg) **モノローグを更新** — に必要な最小バージョンを更新しました。 `monolog` ～にパッケージ化する `^2.3`.<!-- ACMP-1263 -->
- ![修正アイコン](../../assets/fix.svg) **Symfony の更新**—Symfony の依存関係をAdobe Commerce 2.4.4 との互換性を持つように更新しました。<!-- ACMP-1533 -->
- ![修正アイコン](../../assets/fix.svg) **自動読み込みの機能/解決** — 統合環境にデプロイし、 `CRITICAL: [9] Required configuration is missed in autoload section of composer.json file.` エラー。<!-- https://github.com/magento/ece-tools/pull/799 -->

## v2002.1.7

リリース日： 2021 年 7 月 30 日

**設定の更新**—

- ![新しいアイコン](../../assets/new.svg) Composer 2.0 のサポートを追加しました。<!--MCLOUD-8003-->

- ![修正アイコン](../../assets/fix.svg) **コンポーザーの要件を次のように更新しました。`symphony/console`**— ECE-Tools を更新しました。 `composer.json` のバージョン要件 `symphony/console` パッケージを使用して、 `di:compile` 次のエラーで失敗するコマンド： `Incompatible argument type: Required type: int. Actual type: string`<!--MC-42919-->

- ![修正アイコン](../../assets/fix.svg) 提供終了のソフトウェアチェック (`eol.yaml`) を追加して、Elasticsearch7.9.x を含めます。<!--MCLOUD-7938-->

## v2002.1.6

リリース日： 2021 年 4 月 21 日

- ![新しいアイコン](../../assets/new.svg) **Redis 認証資格情報**—Redis 認証資格情報を `relationships` プロパティを使用します。<!--MCLOUD-7694-->

- ![新しいアイコン](../../assets/new.svg) **Elasticsearch認証資格情報** — からElasticsearch認証資格情報を読み取る機能を追加しました。 `relationships` プロパティを使用します。<!--MCLOUD-7695-->

- ![新しいアイコン](../../assets/new.svg) **専用セッションストレージサービス** — 追加済み `redis-session` をセッションストレージの 2 番目のオプションとして使用します。 以下を使用すると、 `redis-session` セッション情報を保存し、 `redis` キャッシュのサービスを使用してパフォーマンスを向上させます。<!--MCLOUD-7698-->

- ![新しいアイコン](../../assets/new.svg) **非推奨の SPLIT_DB メッセージ** — 非推奨（廃止予定）のに対する警告と重要なメッセージを検証する機能を追加しました。 `SPLIT_DB` Adobe Commerce 2.4.2 のオプションと、Adobe Commerce 2.5.0 でのその削除です。<!--MCLOUD-7806-->

- ![修正アイコン](../../assets/fix.svg) **関係からのElasticsearchバージョン** — から正しいバージョンのElasticsearchを取得するようにサービスバリデーターを修正しました。 `relationships` プロパティを使用します。<!--MCLOUD-7572-->

- ![修正アイコン](../../assets/fix.svg) **柔軟な Redis ポート検証**—Redis は、 `server` URL。 例えば、次のようにポート番号をサーバー URL に追加できます。 `server: 'tcp://rfs-store-simple-page-cache:26379'`. これにより、 `port` オプションが見つからないか、正しくありません。<!--MCLOUD-7722-->

- ![修正アイコン](../../assets/fix.svg) **Adobe Commerce 2.4.2 へのアップグレード** — ユーザーが手動で実行する必要がある問題を修正しました。 `bin/magento setup:upgrade` Adobe Commerce 2.4.2 にアップグレードした後でサイトを動作可能にする<!--MCLOUD-7776-->

## v2002.1.5

リリース日： 2021 年 2 月 2 日

- ![新しいアイコン](../../assets/new.svg) **リモートストレージ** — が追加されました。 `REMOTE_STORAGE` 環境変数を使用して、AWS S3 などのストレージサービスを使用したメディアファイルのリモートストレージに関する Cloud Projects を有効にします。 この設定オプションは、ECE-Tools パッケージの一部ですが、クラウドインフラストラクチャ上のAdobe Commerceではサポートされていません。<!--MCLOUD-7153-->

- ![新しいアイコン](../../assets/new.svg) **新規 `cloud:config:validate` command** — コマンドを追加しました。 `php vendor/bin/ece-tools cloud:config:validate` 検証するには `.magento.env.yaml` 変更をリモートクラウド環境にプッシュする前の設定<!--MCLOUD-7120-->

- ![新しいアイコン](../../assets/new.svg) **opcache をフラッシュしています** — サポートを追加しました `opcache.enable_cli` デプロイフックを実行する前に OPcache をフラッシュする PHP オプション。 この設定は、現在の設定が各デプロイメントに適用されるようにキャッシュ設定をリセットします。<!--MCLOUD-7015-->

- ![新しいアイコン](../../assets/new.svg) **Aurora DB の検証**- Aurora データベースと互換性があるように、データベースサービスの検証を更新しました。<!--MCLOUD-7269-->

- ![新しいアイコン](../../assets/new.svg) **新しい SCD_NO_PARENT 環境変数** — が追加されました。 `SCD_NO_PARENT` 環境変数 (Adobe Commerce = 2.4.2 以降 ) を使用して、親テーマの静的コンテンツの生成を管理します。<!--MCLOUD-7284-->

- ![修正アイコン](../../assets/fix.svg) **メモリ制限とコマンド**— `php vendor/bin/ece-tools` コマンドは、 `cloud.log` ファイルが PHP memory_limit を超えました。 全体を読み取る代わりに、 `cloud.log` ファイルをメモリに読み込むと、ログファイルから読み込まれるデータの小さなサブセットのみが読み込まれるようになりました。<!--MCLOUD-7275--><!--MCLOUD-7400-->

- ![修正アイコン](../../assets/fix.svg) **カスタムデータベース接続** — 修正された `.magento.env.yaml` カスタムデータベース接続が次の目的で定義される設定の問題： `DATABASE_CONFIGURATION` が使用されていませんでした。 接続設定が次に追加されませんでした： `app/etc/env.php`.<!--MCLOUD-7426-->

- ![修正アイコン](../../assets/fix.svg) **空のエラーログ**— `cloud.error.log` は空でした。<!--MCLOUD-7296-->

- ![修正アイコン](../../assets/fix.svg) **MariaDB 10.3 の検証**—Adobe Commerce 2.3.6-p1 用の MariaDB 10.3 の検証を修正しました。<!--MCLOUD-7416-->

- ![修正アイコン](../../assets/fix.svg) **キャッシュ：フラッシュログ** — ログエントリを改善し、 `cache:flush` 手順<!--MCLOUD-7503-->

## v2002.1.4

リリース日： 2020 年 11 月 20 日

- ![修正アイコン](../../assets/fix.svg) 検索エンジンが `SEARCH_CONFIGURATION` 環境変数は次の値以外： `elasticsearch`.<!--MCLOUD-7283-->

## v2002.1.3

リリース日： 2020 年 11 月 10 日

**インフラストラクチャの更新**—

- ![新しいアイコン](../../assets/new.svg) 読み取り専用の ECE-Tools のサポートを追加しました。 `pub/static` ビルドステージで静的コンテンツがデプロイされるように設定されている場合のディレクトリ。<!--MC-37699-->

- ![新しいアイコン](../../assets/new.svg) 今後のAdobe Commerceリリースとの互換性を確保するため、Elasticsearch7.9 および Redis 6 のサポートを追加しました。<!--MCLOUD-7191-->

- ![修正アイコン](../../assets/fix.svg) ECE-Tools を更新しました。 `composer.json` をクリックして、クォリティパッチツールに必要な依存関係を追加します。 これにより、ECE-Tools パッケージと magento-cloud-patches パッケージの間に存在していた循環依存関係が修正されます。<!--MCLOUD-6910-->

**検証とログの改善**—

- ![新しいアイコン](../../assets/new.svg) 検索エンジンの検証を追加し、 `elasticsearch` は、クラウドインフラストラクチャ 2.4 以降のAdobe Commerceに対して設定されます。 検証が失敗した場合、問題の修正を提案する重要なエラーメッセージが表示され、デプロイメントが停止します。 詳しくは、 [重大なエラー、デプロイステージ](../dev-tools/error-reference.md#deploy-stage).<!--MCLOUD-6937-->

- ![新しいアイコン](../../assets/new.svg) ElasticsearchサービスバージョンとAdobe Commerceバージョンの互換性を確認するElasticsearch検証が追加されました。<!--MCLOUD-7193-->

- ![新しいアイコン](../../assets/new.svg) 「Elasticsearchの互換性」エラーメッセージが更新され、Adobe CommerceElasticsearchモジュールと互換性のあるElasticsearchのバージョンが表示されるようになりました。 Elasticsearchメッセージに、お使いのバージョンのAdobe Commerceで使用されるElasticsearchモジュールとの互換性を持たせるために、Cloud インフラストラクチャにインストールする特定のエラーバージョンが表示されるようになりました。 詳しくは、 [警告エラー、デプロイステージ](../dev-tools/error-reference.md#deploy-stage-1).<!--MCLOUD-6698-->

- ![新しいアイコン](../../assets/new.svg) 警告エラーを追加しました。 `2026` および `2027` 無効 `MAGE_MODE` 環境変数の設定。 有効な値は次のみです。 `production`. この修正の前に、 `MAGE_MODE` は次のように設定できます。 `developer` デプロイメントエラーが発生しない場合は、読み取り専用ファイルに書き込もうとした後でのみエラーが発生します。 詳しくは、 [警告エラー](../dev-tools/error-reference.md#warning-errors).<!--MCLOUD-6708-->

- ![修正アイコン](../../assets/fix.svg) Redis、RabbitMQ、MySQL の各サービスで、これらのバージョンがAdobe Commerceのバージョンと互換性があることを確認するための検証を修正しました。 これらのサービスの有効なバージョンが、 `cloud.log`.<!--MCLOUD-7098-->

- ![修正アイコン](../../assets/fix.svg) 更新された `cloud.log` ：キャッシュのウォームアップ中にリクエストを送信する際の同時リクエスト数の制限を含めます。 この値は、 [WARM_UP_CONCURRENCY](../environment/variables-post-deploy.md#warm_up_concurrency) デプロイ後の変数。<!--MCLOUD-5563-->

**CLI コマンドの更新**—

- ![新しいアイコン](../../assets/new.svg) CLI コマンド (`cloud:config:create` および `cloud:config:update`) をクリックして、 `.magento.env.yaml` ファイルを作成します。 詳しくは、 [CLI からの設定ファイルの作成](../environment/configure-env-yaml.md#create-configuration-file-from-cli).<!--MCLOUD-7072-->

**環境変数の更新**—

- ![新しいアイコン](../../assets/new.svg) 追加された [SKIP_COMPOSER_DUMP_AUTOLOAD](../environment/variables-build.md#skip_composer_dump_autoload) 変数を作成します。 変数をに設定します。 `true` アプリケーションが `composer dump-autoload` コマンドを使用して、Cloud Docker for Commerce のインストール中に実行する必要があります。 変数は、書き込み可能なファイルシステム（を使用したテストおよび開発用に作成）を含む Cloud Docker for Commerce コンテナにのみ関連します。 `./vendor/bin/ece-docker build:compose --with-test`) をクリックします。 このようなインストールでは、をスキップします。 `composer dump-autoload` コマンドは、削除されたファイルからファイルにアクセスしようとする他のコマンドを実行する際にエラーを防ぎます `generated` ディレクトリ。<!--MCLOUD-6939-->

## v2002.1.2

リリース日： 2020 年 8 月 6 日

**検証とログの改善**—

- ![新しいアイコン](../../assets/new.svg) 追加された `schema.error.yaml` ビルド、デプロイ、デプロイ後のプロセス中に発生する可能性のあるすべてのエラーおよび警告の通知と、エラーの解決方法の提案を含むファイル。 このファイルの情報は、 _コマース用クラウドガイド_. 詳しくは、 [ece-tools のエラーメッセージリファレンス](../dev-tools/error-reference.md).<!--MCLOUD-5878-->

- ![新しいアイコン](../../assets/new.svg) クラウドエラーログを変更しました (`/var/log/cloud.error.log`) を JSON 形式に変換し、ログをプログラムで簡単に解析できるようにします。<!--MCLOUD-5879-->

- ![新しいアイコン](../../assets/new.svg) デプロイ後の処理をビルド、デプロイ、および後処理するためのエラーチェックを追加し、既存のチェックを改善しました。

   - エラーコード 2026 — ビルドフェーズで生成された一部のデータをマウントされたディレクトリに復元できませんでした

   - エラーコード 3004 — バックアップファイルを作成できません

   - エラーコード 102 - `env.php` ファイルは書き込み可能ではありません <!--MCLOUD-6221-->

- ![新しいアイコン](../../assets/new.svg) 追加された **QUALITY_PATCH** 環境変数：デプロイメントプロセス中に適用する 1 つ以上の品質パッチを指定します。 詳しくは、 [変数の作成](../environment/variables-build.md#quality_patches).<!--MCLOUD-6375-->

## v2002.1.1

リリース日： 2020 年 6 月 26 日

- ![新しいアイコン](../../assets/new.svg) **インフラストラクチャの更新**—

   - ![新しいアイコン](../../assets/new.svg) **ログの改善** — ログトラッキング機能が強化され、終了コードを重要なデプロイエラーに割り当て、終了コードをエラーメッセージ通知とログイベントに公開するようになりました。 詳しくは、 [ece-tools のエラーメッセージリファレンス](../dev-tools/error-reference.md).<!-- MCLOUD-5637, 5531-->

   - ![新しいアイコン](../../assets/new.svg) データベースダンプのプロセスを改善しました (`vendor/bin/ece-tools db-dump`) とログメッセージを更新し、データベースダンプ操作でアプリケーションがメンテナンスモードに切り替わり、コンシューマーキュープロセスが停止し、ダンプが開始する前に cron ジョブが無効になることを明確にしました。<!--MCLOUD-5324, MCLOUD-2062-->

   - ![修正アイコン](../../assets/fix.svg) ステージングおよび実稼動環境にデプロイする際に、プロジェクト URL が正しく更新されるようにする問題を修正しました。 さて `ece-tools` はルートの URL を `primary:true` 属性がプロジェクトルート設定に設定されている。 詳しくは、 [変数をデプロイ](../environment/variables-deploy.md#update_urls).<!--MCLOUD-5883-->

   - ![修正アイコン](../../assets/fix.svg) 更新された `generate.xml` パッチを適用するシナリオワークフローを作成します。 Adobe Commerceを更新する前にパッチを適用して、原因となる可能性のある問題を修正する必要があります。 `di:compile` および `module:refresh` 失敗する手順を説明します。<!--MCLOUD-5941-->

   - ![修正アイコン](../../assets/fix.svg) インストールプロセスで、 `Crypt key missing` エラー。 The `crypt/key` の値は、インストール時に自動的に生成されます。<!--MCLOUD-6120-->

- ![新しいアイコン](../../assets/new.svg) **サービスの更新**—

   - ![新しいアイコン](../../assets/new.svg) PHP 7.4 および MariaDB 10.4 のサポートを追加しました。<!--MAGECLOUD-2957, MCLOUD-4144-->

- ![新しいアイコン](../../assets/new.svg) **環境変数の更新**—

   - ![新しいアイコン](../../assets/new.svg) 追加された **SCD_USE_BALER** 変数を使用して、Adobe Commerce on cloud infrastructure のビルドプロセス中に JavaScript のバンドル用の Baler モジュールを有効にします。 変数の説明については、 [変数を作成](../environment/variables-build.md#scd_use_baler).<!-- MCLOUD-3456, MCLOUD-3457-->

   - ![新しいアイコン](../../assets/new.svg) 追加された **REDIS_BACKEND** 環境変数を使用して、Adobe Commerce 2.3.5 以降の Redis キャッシュ用の Redis バックエンドモデルを設定します。 変数の説明については、 [変数をデプロイ](../environment/variables-deploy.md#redis_backend).<!--MCLOUD-5721, MCLOUD-5865-->

- ![新しいアイコン](../../assets/new.svg) **CLI コマンドの更新**—

   - ![新しいアイコン](../../assets/new.svg) 次の CLI コマンドを更新し、詳細なログのオプションを追加しました。

      - `app:config:dump`
      - `app:config:import`
      - `module:enable`

     各呼び出しのログレベルは、 [`VERBOSE_COMMANDS`](../environment/variables-build.md#verbose_commands) 変数を `.magento.env.yaml` ファイル。<!--MCLOUD-3503-->

- ![新しいアイコン](../../assets/new.svg) **検証の改善点**—

   - ![新しいアイコン](../../assets/new.svg) **Elasticsearch7.x の互換性チェック**-Elasticsearch7.x のElasticsearch互換性チェックの検証を更新しました。<!--MCLOUD-5542-->

   - ![新しいアイコン](../../assets/new.svg) **サービスのバージョンと EOL 検証チェックを更新しました** — インストールされているサービスのバージョンがAdobe Commerce 2.4 の要件を満たしているかどうかを確認するための検証を更新しました。<!--MCLOUD-6144-->

   - ![修正アイコン](../../assets/fix.svg) 検証の問題を修正し、デプロイ後の警告メッセージが `post-deploy` フック設定がにありません `.magento.app.yaml` ファイル：

     ```text
     Your application does not have the "post_deploy" hook enabled.
     ```

     <!--MCLOUD-4077-->

   - ![新しいアイコン](../../assets/new.svg) **Zend Framework の依存関係の検証を追加しました。**- Laminas プロジェクトに移行した Zend Framework 用に、コンポーザーの依存関係の検証を追加しました。 必要な依存関係が見つからない場合は、ビルドプロセス中に次のエラーメッセージが表示されます。

     ```text
     Required configuration is missing from the autoload section of the composer.json file.
     Add ("Laminas\Mvc\Controller\Zend\": "setupsrc/ Zend/Mvc/Controller/") to
     the `autoload -> psr-4` section. Then, re-run the "composer update" command locally, and
     commit the updated composer.json and composer.lock files.
     ```

     詳しくは、 [Zend フレームワークの依存関係の検証](../development/commerce-version.md#verify-zend-framework-composer-dependencies).<!--MCLOUD-4094-->

   - ![新しいアイコン](../../assets/new.svg) **次の検証を追加しました。 `env.php` ファイルとデータ**— `env.php` ファイルとデータは、インストールおよびアップグレードプロセス中に生成されます。<!--MCLOUD-5991-->

      - 次の場合、 `env.php` ファイルがインストールに見つからず、 `crypt/key` 値が `.magento.app.yaml` ファイルが見つからない場合、次の通知でデプロイメントが失敗します。

        ```text
        The crypt/key key value does not exist in the ./app/etc/env.php file or the CRYPT_KEY cloud environment variable``Missing crypt key for upgrading Magento`.
        ```

      - インストールに `env.php` ファイル、または設定に含まれるキャッシュタイプは 1 つだけ、 `cron:enable` コマンドは、アップグレードプロセス中に実行され、ファイルをすべて `cache_types`. 次の通知がログに追加されます。

        ```text
        Magento state indicated as installed but configuration file app/etc/env.php was empty or did not exist.
        Required data will be restored from environment configurations and from the .magento.env.yaml file.
        ```

## v2002.1.0

リリース日： 2020 年 2 月 7 日

- ![新しいアイコン](../../assets/new.svg) **インフラストラクチャの更新**—

   - ![新しいアイコン](../../assets/new.svg) **Cloud Docker for Commerce に別個のパッケージを追加しました。**—Docker パッケージを `ece-tools` コード品質を維持し、独立したリリースを提供するためのパッケージ。 に関する更新および修正点です。 `ece-tools` は、 [magento-cloud-docker](https://github.com/magento/magento-cloud-docker) GitHub リポジトリ。<!--MAGECLOUD-2927-->

   - ![新しいアイコン](../../assets/new.svg) **更新されたパッチ適用機能** — パッチ適用機能を ECE-Tools パッケージから別のに移動しました。 [magento-cloud-patches](https://github.com/magento/magento-cloud-patches) パッケージ。 デプロイ時に、 `ece-tools` は、新しいパッケージを使用してパッチを適用します。 詳しくは、 [クラウドパッチリリースノート](cloud-patches.md).<!--MAGECLOUD-4567-->

   - ![新しいアイコン](../../assets/new.svg) **コンポーザーの依存関係を更新しました** — を更新しました。 `composer.json` クラウドインフラストラクチャ上のAdobe Commerceのファイル ( `magento/magento-cloud-docker` パッケージ。 さて `ece-tools` には、 [`Cloud Tools Suite for Commerce`](cloud-tools-suite.md). これらのパッケージは、インストールまたは更新時に自動的にインストールおよび更新されます。 `ece-tools`.

- ![新しいアイコン](../../assets/new.svg) **シナリオベースのデプロイメントのサポート**—<!--MAGECLOUD-4101-->

   - ![新しいアイコン](../../assets/new.svg) XML 設定ファイルを使用して、ビルド、デプロイおよびデプロイ後のプロセスをカスタマイズし、デフォルトの設定を上書きまたはカスタマイズできるようになりました。

   - ![新しいアイコン](../../assets/new.svg) **変更された `hooks` の設定`.magento.app.yaml`** — 更新： `hooks` シナリオベースのデプロイメントをサポートする設定形式です。 以前の ECE-Tools 2002.0.x リリースのレガシー形式は、引き続きサポートされます。 ただし、シナリオベースのデプロイメント機能を使用するには、を新しい形式に更新する必要があります。 詳しくは、 [シナリオベースのデプロイメント](../deploy/scenario-based.md#add-scenarios-using-build-and-deploy-hooks).

>[!NOTE]
>
>ECE-Tools バージョン 2002.1.0 に更新する前に、 [後方互換性のない変更](backward-incompatible-changes.md) を参照して、Adobe Commerce on cloud infrastructure プロジェクトの設定またはプロセスの更新が必要になる可能性のある変更について確認してください。

- ![新しいアイコン](../../assets/new.svg) **サービスの更新**—

   - ![新しいアイコン](../../assets/new.svg) PHP 7.3 のサポートを追加しました。<!--MAGECLOUD-4022-->

   - ![新しいアイコン](../../assets/new.svg) RabbitMQ 3.8 のサポートを追加しました。<!--MAGECLOUD-4674-->

   - ![新しいアイコン](../../assets/new.svg) 各サービスの EOL 日に対して、インストールされているサービスのバージョンを確認するための検証機能を追加しました。 現在は、サービスバージョンが EOL 日から 3 ヶ月以内の場合に通知を受け取り、EOL 日が過去の場合に警告を受け取るようになりました。<!--MAGECLOUD-4076-->

   - ![修正アイコン](../../assets/fix.svg) Elasticsearch設定の問題を修正し、すべての環境で正しいElasticsearch設定が設定されるようにしました。<!--MAGECLOUD-4474-->

>[!NOTE]
>
>詳しくは、 [サービスバージョン](../services/services-yaml.md#service-versions) クラウドインフラストラクチャ上のAdobe Commerceで使用されるサービスと、クラウドテンプレートとのバージョンの互換性の一覧を参照してください。

- ![新しいアイコン](../../assets/new.svg) **環境変数の更新**—

   - ![新しいアイコン](../../assets/new.svg) の機能を拡張しました。 `WARM_UP_PAGES` 環境変数を使用して、特定の製品ページのキャッシュのプリロードをサポートします。 拡張された定義を [デプロイ後の変数](../environment/variables-post-deploy.md#warm_up_pages) トピック。<!--MAGECLOUD-4444-->

   - ![新しいアイコン](../../assets/new.svg) 追加された `ERROR_REPORT_DIR_NESTING_LEVEL` 環境変数を使用して、 `<magento_root>/var/report/` ディレクトリ。 変数の説明については、 [変数を作成](../environment/variables-build.md#error_report_dir_nesting_level) トピック。

   - ![修正アイコン](../../assets/fix.svg) を削除しました。 `SCD_EXCLUDE_THEMES`, `STATIC_CONTENT_THREADS`,`DO_DEPLOY_STATIC_CONTENT`、および `STATIC_CONTENT_SYMLINK` 環境変数。 詳しくは、 [後方互換性のない変更](backward-incompatible-changes.md#environment-configuration-changes).<!--MAGECLOUD-4407, MAGECLOUD-3873-->

   - ![修正アイコン](../../assets/fix.svg) Elastic Suite の設定プロセスで、デフォルトの設定が `ELASTICSUITE_CONFIGURATION` 変数を `_merge` オプション。<!--MAGECLOUD-4388-->

- ![新しいアイコン](../../assets/new.svg) **CLI コマンドの更新**—

   - ![新しいアイコン](../../assets/new.svg) **新しい cron コマンド** — を使用して、クラウドインフラストラクチャ環境上のAdobe Commerceで cron 処理を手動で管理できるようになりました。 `cron:disable` および `cron:enable` コマンド。 disable コマンドを使用して、すべてのアクティブな Cron プロセスを停止し、すべての Cron ジョブを無効にします。 準備が整ったら、enable コマンドを使用して、cron ジョブを再度有効にします。 詳しくは、 [cron ジョブを無効にする](../application/crons-property.md#disable-cron-jobs).

   - ![新しいアイコン](../../assets/new.svg) **エラーレポートの改善**— ECE-Tools の処理中に発生する CLI コマンドの失敗に対するログの作成が改善されました。<!--MAGECLOUD-4849-->

   - ![新しいアイコン](../../assets/new.svg) **非推奨のビルドコマンドを削除** — 次のビルドコマンドを削除しました。 `m2-ece-build`, `m2-ece-deploy`, `m2-ece-scd-dump`、名前を変更 `ece-tools docker` コマンド `ece-docker`. 詳しくは、 [後方互換性のない変更](backward-incompatible-changes.md)<!--MAGECLOUD-4392-->

- ![新しいアイコン](../../assets/new.svg) 非推奨の `build_options.ini` ファイルが存在する場合にビルドに失敗する検証を追加しました。 以下を使用します。 [.magento.env.yaml](../environment/configure-env-yaml.md) ファイルを参照してビルドオプションを設定します。

- ![修正アイコン](../../assets/fix.svg) ビルドプロセスが `config.php` ファイルが空です。<!--MAGECLOUD-4127-->

## 2002.0.23

リリース日： 2020 年 2 月 28 日

- ![修正アイコン](../../assets/fix.svg) との互換性の問題を修正しました。 `ece-tools` オンデマンドの静的コンテンツ生成を実稼動モードで正常に完了できなかった 2002.0.x リリースです。

## 以前のリリース

詳しくは、 [リリースノートアーカイブ](cloud-release-archive.md) （バージョン 2002.0.22 以前）。
