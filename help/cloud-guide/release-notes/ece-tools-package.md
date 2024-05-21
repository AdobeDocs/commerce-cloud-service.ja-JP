---
title: ECE-Tools リリースノート
description: ECE-Tools パッケージの最新の改善点のリストを確認してください。
recommendations: noDisplay, catalog
last-substantial-update: 2024-05-21T00:00:00Z
exl-id: a464b940-c56e-4a7c-9948-559539e25361
source-git-commit: 923e2114270df22e134e0676ac97f84d770bb226
workflow-type: tm+mt
source-wordcount: '2929'
ht-degree: 0%

---

# ECE-Tools リリースノート

この [ece-tools](https://github.com/magento/ece-tools) パッケージは、クラウドプロジェクトを管理およびデプロイするために設計された一連のスクリプトとツールです。 これらのリリースノートでは、の一部であるこのパッケージの最新の改善点について説明します [Commerce用 Cloud Tools スイート](cloud-tools-suite.md).

>[!NOTE]
>
>参照： [ECE ツールのアップグレード](../dev-tools/update-package.md) の最新リリースへの更新に関する情報 `ece-tools` パッケージ。

この `ece-tools` パッケージでは、次のリリースバージョン順が使用されます。 `200<major>.<minor>.<patch>`

リリースノートには次のものが含まれます。

- ![新規アイコン](../../assets/new.svg) 新機能
- ![修正アイコン](../../assets/fix.svg) 修正点および改善点

<!--Add release notes below-->

## v2002.1.19 {#latest}

リリース日：2024 年 5 月 21 日（PT）

- ![新規アイコン](../../assets/new.svg) **Lua**—CACHE_CONFIGURATION に useLua オプションを追加しました。
- ![修正アイコン](../../assets/fix.svg) **バリデーター** – 新しいバージョンの Redis とRabbitMQのバリデータを更新しました。

## v2002.1.18

リリース日：2024 年 4 月 8 日（PT）

- ![新規アイコン](../../assets/new.svg) **PHP** — PHP 8.3 のサポートを追加。
- ![修正アイコン](../../assets/fix.svg) バリデーター – EOL バリデーターを更新しました。

## v2002.1.17

リリース日：2024 年 1 月 16 日（PT）

- ![修正アイコン](../../assets/fix.svg) **Elasticsearchのバリデーター&amp; OpenSearch**- LiveSearch が有効な場合に、検索サービスをインストールすると誤解を招くメッセージが表示されるバリデーターを修正しました。<!-- MCLOUD-10167 -->
- ![修正アイコン](../../assets/fix.svg) **デプロイメントの警告** – 空でないフォルダーに関するデプロイメントの警告が表示される問題を修正しました。<!-- MCLOUD-8958 -->

## v2002.1.16

リリース日：2023 年 10 月 16 日（PT）

- ![新規アイコン](../../assets/new.svg) **ENABLE_WEBHOOK グローバル環境変数** – が追加されました [ENABLE_WEBHOOK](../environment/variables-global.md#enable_webhooks) Commerce Webhook で使用して、App Builder ランタイムアクションやサードパーティの在庫管理システムなどの外部エンドポイントに接続するためのグローバル変数。

## v2002.1.15

リリース日：2023 年 7 月 31 日（PT）

- ![修正アイコン](../../assets/fix.svg) **エラーコード**- エラーコードスキーマとエラーコードドキュメントジェネレーターを更新しました。
- ![修正アイコン](../../assets/fix.svg) **カスタム Redis モデルのバリデーター**- カスタム Redis バックエンドモデルのバリデーターを更新しました。 [キャッシュ設定の例を参照してください](../environment/variables-deploy.md#cache_configuration).
- ![修正アイコン](../../assets/fix.svg) **RabbitMQのバリデーター**-RabbitMQ 3.11 がサポートされるようになりました。
- ![修正アイコン](../../assets/fix.svg) **誤ったリンクを修正しました** – お知らせメールテンプレートのオンボーディングドキュメントへの間違ったリンクを修正しました。

## v2002.1.14

リリース日：2023 年 3 月 10 日（PT）

- ![新規アイコン](../../assets/new.svg) **PHP**—PHP 8.2 のサポートを追加しました。
- ![新規アイコン](../../assets/new.svg) **サービスのバリデーター**—Commerce 2.4.6 の必要なサービスのバリデータを更新：MariaDB 10.6、Redis 7.0、PHP 8.2、OpenSearch 2.x、RabbitMQ 3.9。
- ![修正アイコン](../../assets/fix.svg) **ece-tools db-dump** – が発生する問題を修正しました。 `db-dump` 途中で停止する操作です。

## v2002.1.13

リリース日：2022 年 10 月 27 日（PT）

- ![新規アイコン](../../assets/new.svg) **Adobe CommerceのAdobe I/Oイベントをサポートするようになりました**. 拡張機能の開発者は、 [Adobe I/Oイベント](https://developer.adobe.com/events/docs/) クラウドインスタンスから、用に作成されたアプリケーションにCommerce イベント情報を送信するフレームワーク [Adobe App Builder](https://developer.adobe.com/app-builder/docs/overview/). Adobe CommerceのAdobe I/Oイベントは、パートナープレビュー中です。<!-- CEXT-932 -->
- ![新規アイコン](../../assets/new.svg) **OPcache 設定のバリデーター** – 除外パスの OPcache 設定を確認するバリデーターを追加しました。<!-- MCLOUD-9485 -->
- ![修正アイコン](../../assets/fix.svg) **GraphQLのキャッシュ設定に関する問題を修正しました**—ECE-Tools がGraphQLを保持するようになりました。 `id_salt` 値： `cache` での設定 `app/etc/env.php` ファイル。<!-- MCLOUD-9486 -->

## v2002.1.12

リリース日：2022 年 9 月 13 日（PT）

- ![新規アイコン](../../assets/new.svg) **Enable （有効）`synchronous_replication`**—ECE-Tools セット `synchronous_replication=>true` が含まれる `app/etc/env.php` ファイル条件 `MYSQL_USE_SLAVE_CONNECTION` が有効になっています。 この設定は、Commerce 2.4.6 以降にのみ影響します。 を参照してください。 `MYSQL_USE_SLAVE_CONNECTION` での変数の説明 [変数のデプロイ](../environment/variables-deploy.md#mysql_use_slave_connection).<!-- MCLOUD-9142 -->
- ![新規アイコン](../../assets/new.svg) **OpenSearch** – を設定および設定する機能を追加しました。 `opensearch` 次回のAdobe Commerce リリース 2.4.6 のエンジン。参照： [OpenSearch サービスの設定](../services/opensearch.md).<!-- MCLOUD-9236 -->

## v2002.1.11

リリース日：2022 年 8 月 4 日（PT）

- ![修正アイコン](../../assets/fix.svg) **ElasticSuite バリデーターと OpenSearch**- OpenSearch がインストールされている場合の ElasticSuite 整合性チェックバリデーターの問題を修正しました。<!-- MCLOUD-8767 -->
- ![修正アイコン](../../assets/fix.svg) **deploy コマンドの戻り値の型**—deploy コマンドの戻り値の型を修正しました。<!-- AC-3208 -->
- ![修正アイコン](../../assets/fix.svg) **[!DNL RabbitMQ]新しいCommerce 2.4.5 のインストールに関する問題** – 固定 [!DNL RabbitMQ] 新しいCommerce 2.4.5 でのクラッシュの問題。<!-- MCLOUD-9059 -->

## v2002.1.10

リリース日：2022 年 3 月 31 日（PT）

- ![修正アイコン](../../assets/fix.svg) **Elasticsearch 7.10**- 7.10 バージョンのElasticsearchをサポートするようにバリデーターを更新しました。<!-- MCLOUD-8548 -->

## v2002.1.9

リリース日：2022 年 3 月 10 日（PT）

- ![新規アイコン](../../assets/new.svg) **OpenSearch**—Adobe Commerce バージョン 2.4.4、2.4.3-p2 および 2.3.7-p3 の OpenSearch がサポートされるようになりました。<!-- MCLOUD-8296 -->
- ![新規アイコン](../../assets/new.svg) **PHP**—PHP 8.1 のサポートを追加しました。
- ![修正アイコン](../../assets/fix.svg) **交信/プロセス**— symfony/process ^5.3 との互換性を追加しました。<!-- MCLOUD-8283 -->

- ![新規アイコン](../../assets/new.svg) **コンシューマーの複数のプロセス** – が追加されました `multiple_processes` 各コンシューマーに対して発生するプロセスの数を指定できるオプション。 を参照してください。 `CRON_CONSUMERS_RUNNER` での変数の説明 [変数のデプロイ](../environment/variables-deploy.md#cron_consumers_runner).<!-- MCLOUD-8295 -->
- ![新規アイコン](../../assets/new.svg) **OpenSearch スキームと完全なホスト パス**—Elasticsearchスキームとフルホストパスを設定する機能が追加されました。
- ![修正アイコン](../../assets/fix.svg) **AWS S3**—AWS S3 有効化の手法を変更しました。
- ![修正アイコン](../../assets/fix.svg) **driver_options リーダーの修正** – から DB 接続のための driver_options 設定の読み込みを追加しました。 `env.php` ファイル作成者 `ece-tools` （バリデーター用）。<!-- MCLOUD-8420 -->

## v2002.1.8

リリース日：2021 年 10 月 25 日（PT）

- ![新規アイコン](../../assets/new.svg) **代替ダンプの場所** – が追加されました `--dump-directory` DB ダンプのターゲットディレクトリを選択するためのオプション。 今すぐ `/app/var/dump-main` は、DB ダンプのデフォルトのターゲットディレクトリです。 参照： [バックアップ管理：データベースをダンプします](../storage/database-dump.md)<!-- MCLOUD-8063 -->
- ![修正アイコン](../../assets/fix.svg) **モノログの更新** – に必要な最小バージョンを更新しました。 `monolog` パッケージ先 `^2.3`.<!-- ACMP-1263 -->
- ![修正アイコン](../../assets/fix.svg) **署名を更新**—Adobe Commerce 2.4.4 と互換性を持たせるために、Symfony 依存関係を更新しました。<!-- ACMP-1533 -->
- ![修正アイコン](../../assets/fix.svg) **自動ロードの機能/解決** – 統合環境にデプロイしてを表示する際の問題を修正しました `CRITICAL: [9] Required configuration is missed in autoload section of composer.json file.` エラー。<!-- https://github.com/magento/ece-tools/pull/799 -->

## v2002.1.7

リリース日：2021 年 7 月 29 日（PT）

**設定の更新**—

- ![新規アイコン](../../assets/new.svg) Composer 2.0 がサポートされるようになりました。<!--MCLOUD-8003-->

- ![修正アイコン](../../assets/fix.svg) **のコンポーザー要件を更新しました`symphony/console`**— ECE ツールを更新しました。 `composer.json` のバージョン要件 `symphony/console` の原因となった問題を修正するパッケージ `di:compile` 次のエラーで失敗するコマンド： `Incompatible argument type: Required type: int. Actual type: string`<!--MC-42919-->

- ![修正アイコン](../../assets/fix.svg) サポート終了のソフトウェアチェック （`eol.yaml`）を選択して、Elasticsearch 7.9.x を含めます。<!--MCLOUD-7938-->

## v2002.1.6

リリース日：2021 年 4 月 20 日（PT）

- ![新規アイコン](../../assets/new.svg) **Redis 認証資格情報** – から Redis 認証資格情報を読み取る機能が追加されました。 `relationships` デプロイフェーズ中のプロパティ。<!--MCLOUD-7694-->

- ![新規アイコン](../../assets/new.svg) **Elasticsearch認証資格情報** – からElasticsearch認証資格情報を読み取る機能が追加されました `relationships` デプロイフェーズ中のプロパティ。<!--MCLOUD-7695-->

- ![新規アイコン](../../assets/new.svg) **専用セッションストレージサービス** – 追加されました `redis-session` セッションストレージの 2 番目のオプションとして。 を使用できます `redis-session` セッション情報を保存して使用するサービス `redis` パフォーマンスを向上させるためのキャッシュのサービス。<!--MCLOUD-7698-->

- ![新規アイコン](../../assets/new.svg) **非推奨の SPLIT_DB メッセージ** – 非推奨のについて、バリデーターの警告と重要なメッセージを追加しました `SPLIT_DB` Adobe Commerce 2.4.2 のオプションとAdobe Commerce 2.5.0 での削除。<!--MCLOUD-7806-->

- ![修正アイコン](../../assets/fix.svg) **関係からのElasticsearchバージョン** – から正しいバージョンのElasticsearchを取得するサービスバリデーターを修正しました。 `relationships` cloud Docker および統合環境のプロパティ。<!--MCLOUD-7572-->

- ![修正アイコン](../../assets/fix.svg) **柔軟な Redis ポート検証**— Redis は、からカスタム キャッシュ接続のポートを検証できるようになりました。 `server` URL。 例えば、次のようにポート番号をサーバー URL に追加できます。 `server: 'tcp://rfs-store-simple-page-cache:26379'`. これは、以下の場所で検証エラーが発生するのを防ぐのに役立ちます `port` オプションがないか、間違っています。<!--MCLOUD-7722-->

- ![修正アイコン](../../assets/fix.svg) **Adobe Commerce 2.4.2 へのアップグレード** – ユーザーが手動で実行する必要があった問題を修正しました `bin/magento setup:upgrade` Adobe Commerce 2.4.2 へのアップグレード後にサイトを使用可能にする場合<!--MCLOUD-7776-->

## v2002.1.5

リリース日：2021 年 2 月 1 日（PT）

- ![新規アイコン](../../assets/new.svg) **リモートストレージ** – が追加されました `REMOTE_STORAGE` AWS S3 などのストレージサービスを使用して、メディアファイルをリモートストレージするための Cloud Projects を有効にする環境変数。 この設定オプションは ECE-Tools パッケージの一部ですが、クラウドインフラストラクチャー上のAdobe Commerceではサポートされていません。<!--MCLOUD-7153-->

- ![新規アイコン](../../assets/new.svg) **新規 `cloud:config:validate` コマンド** – 追加されたコマンド `php vendor/bin/ece-tools cloud:config:validate` を検証します `.magento.env.yaml` 変更をリモートクラウド環境にプッシュする前の設定。<!--MCLOUD-7120-->

- ![新規アイコン](../../assets/new.svg) **opcache のフラッシュ** – のサポートを追加 `opcache.enable_cli` デプロイフックを実行する前に OPcache をフラッシュする PHP オプション。 この設定では、キャッシュ設定がリセットされ、各デプロイメントに現在の設定が確実に適用されます。<!--MCLOUD-7015-->

- ![新規アイコン](../../assets/new.svg) **Aurora DB の検証**- Aurora データベースと互換性を持つように、データベースサービスの検証を更新しました。<!--MCLOUD-7269-->

- ![新規アイコン](../../assets/new.svg) **新しい SCD_NO_PARENT 環境変数** – が追加されました `SCD_NO_PARENT` 親テーマの静的コンテンツの生成を管理する環境変数（Adobe Commerce >=2.4.2 用）。<!--MCLOUD-7284-->

- ![修正アイコン](../../assets/fix.svg) **メモリ制限とコマンド** – 問題を修正しました。 `php vendor/bin/ece-tools` のサイズがの場合、コマンドが機能しない `cloud.log` ファイルが PHP の memory_limit を超えました。 全体を読む代わりに `cloud.log` ファイルをメモリに読み込む場合、ログファイルから読み取られるデータは、比較的小さくなっています。<!--MCLOUD-7275--><!--MCLOUD-7400-->

- ![修正アイコン](../../assets/fix.svg) **カスタムデータベース接続** – を修正しました。 `.magento.env.yaml` カスタムデータベース接続が定義される設定の問題 `DATABASE_CONFIGURATION` は使用されませんでした。 接続設定がに追加されませんでした `app/etc/env.php`.<!--MCLOUD-7426-->

- ![修正アイコン](../../assets/fix.svg) **エラーログが空です** – 以下の場合、デプロイメントが失敗する問題を修正しました `cloud.error.log` は空でした。<!--MCLOUD-7296-->

- ![修正アイコン](../../assets/fix.svg) **MariaDB 10.3 検証**- Adobe Commerce 2.3.6-p1 の MariaDB 10.3 の検証を修正しました。<!--MCLOUD-7416-->

- ![修正アイコン](../../assets/fix.svg) **キャッシュ：フラッシュ ログ** – の開始と終了を示すログエントリを改善しました。 `cache:flush` ステップ。<!--MCLOUD-7503-->

## v2002.1.4

リリース日：2020 年 11 月 19 日（PT）

- ![修正アイコン](../../assets/fix.svg) で指定された検索エンジンのデプロイに失敗する問題を修正しました `SEARCH_CONFIGURATION` 環境変数が以外の値です `elasticsearch`.<!--MCLOUD-7283-->

## v2002.1.3

リリース日：2020 年 11 月 9 日（PT）

**インフラストラクチャの更新**—

- ![新規アイコン](../../assets/new.svg) 読み取り専用のの ECE ツールのサポートを追加 `pub/static` ビルドステージで静的コンテンツがデプロイされるように設定されている場合のディレクトリ。<!--MC-37699-->

- ![新規アイコン](../../assets/new.svg) 今後のAdobe Commerce リリースとの互換性のために、Elasticsearch 7.9 および Redis 6 がサポートされるようになりました。<!--MCLOUD-7191-->

- ![修正アイコン](../../assets/fix.svg) ECE ツールを更新しました `composer.json` 品質向上パッチツールに必要な依存関係を追加します。 これにより、ECE-Tools パッケージと magento-cloud-patches パッケージの間に存在していた循環依存関係が修正されます。<!--MCLOUD-6910-->

**検証とログの改善**—

- ![新規アイコン](../../assets/new.svg) を確認するための検索エンジン検証を追加しました `elasticsearch` は、cloud infrastructure 2.4 以降のAdobe Commerce用に設定されています。 検証に失敗した場合、デプロイメントは停止され、問題の修正を示唆する重要なエラーメッセージが表示されます。 参照： [重大なエラー、展開ステージ](../dev-tools/error-reference.md#deploy-stage).<!--MCLOUD-6937-->

- ![新規アイコン](../../assets/new.svg) ElasticsearchサービスのバージョンとAdobe Commerceのバージョンの間のElasticsearchを確認するための互換性の検証が追加されました。<!--MCLOUD-7193-->

- ![新規アイコン](../../assets/new.svg) Adobe Commerce Elasticsearchモジュールと互換性のあるElasticsearchのバージョンを示すように、Elasticsearch互換性エラーメッセージを更新しました。 お使いのバージョンのAdobe Commerceで使用されているElasticsearchモジュールと互換性を持たせるために、エラーメッセージに、クラウドインフラストラクチャにインストールする具体的なElasticsearchバージョンが表示されるようになりました。 参照： [警告エラー、ステージのデプロイ](../dev-tools/error-reference.md#deploy-stage-1).<!--MCLOUD-6698-->

- ![新規アイコン](../../assets/new.svg) 警告エラーを追加しました `2026` および `2027` 無効の `MAGE_MODE` 環境変数の設定 有効な値はのみです。 `production`. この修正の前に、 `MAGE_MODE` はに設定できます。 `developer` 配置エラーがない場合は、読み取り専用ファイルに書き込もうとした後でエラーが発生する場合にのみ使用します。 参照： [警告エラー](../dev-tools/error-reference.md#warning-errors).<!--MCLOUD-6708-->

- ![修正アイコン](../../assets/fix.svg) Redis、RabbitMQ、MySQL サービスの検証を修正し、これらのバージョンがAdobe Commerceのバージョンと互換性があることを確認しました。 これらのサービスの有効なバージョンが、に書き込まれるようになりました `cloud.log`.<!--MCLOUD-7098-->

- ![修正アイコン](../../assets/fix.svg) が更新されました `cloud.log` キャッシュウォームアップ中に送信リクエストの同時リクエスト制限を含めるには、 この値は、 [WARM_UP_CONCURRENCY](../environment/variables-post-deploy.md#warm_up_concurrency) デプロイ後変数。<!--MCLOUD-5563-->

**CLI コマンドの更新**—

- ![新規アイコン](../../assets/new.svg) CLI コマンド （`cloud:config:create` および `cloud:config:update`）にアクセスして、を作成および更新します `.magento.env.yaml` 1 つ以上のビルド、デプロイ、デプロイ後変数を含めることができる設定を持つファイル。 参照： [CLI からの構成ファイルの作成](../environment/configure-env-yaml.md#create-configuration-file-from-cli).<!--MCLOUD-7072-->

**環境変数の更新**—

- ![新規アイコン](../../assets/new.svg) さんがを追加しました [SKIP_COMPOSER_DUMP_AUTOLOAD](../environment/variables-build.md#skip_composer_dump_autoload) ビルド変数。 変数の設定 `true` アプリケーションによる `composer dump-autoload` cloud Docker for Commerceのインストール中にコマンドを実行します。 変数は、書き込み可能なファイルシステムを持つCommerce コンテナの Cloud Docker にのみ関連します（を使用したテストおよび開発用に作成されます） `./vendor/bin/ece-docker build:compose --with-test`）に設定します。 このようなインストールでは、をスキップします `composer dump-autoload` コマンドは、削除されたからファイルにアクセスしようとする他のコマンドを実行する際にエラーが発生するのを防ぎます `generated` ディレクトリ。<!--MCLOUD-6939-->

## v2002.1.2

リリース日：2020 年 8 月 5 日（PT）

**検証とログの改善**—

- ![新規アイコン](../../assets/new.svg) さんがを追加しました `schema.error.yaml` ビルド、デプロイ、デプロイ後のプロセス中に発生する可能性のあるすべてのエラーおよび警告通知と、エラーを解決するための提案を含むファイルです。 このファイル内の情報は、 _Commerceのクラウドガイド_. 参照： [ece-tools のエラーメッセージ参照](../dev-tools/error-reference.md).<!--MCLOUD-5878-->

- ![新規アイコン](../../assets/new.svg) クラウドエラーログ（`/var/log/cloud.error.log`） エントリを JSON 形式に変換して、ログをプログラムで解析しやすくします。<!--MCLOUD-5879-->

- ![新規アイコン](../../assets/new.svg) ビルド、デプロイ、デプロイ後の処理に対するエラーチェックを追加し、既存のチェックを改善しました。

   - エラーコード 2026 - ビルドフェーズで生成された一部のデータを、マウントされたディレクトリに復元できませんでした

   - エラーコード 3004 - バックアップファイルを作成できない

   - エラーコード 102 – が `env.php` ファイルは書き込み可能ではありません <!--MCLOUD-6221-->

- ![新規アイコン](../../assets/new.svg) さんがを追加しました **QUALITY_PATCH** 環境変数。デプロイメントプロセス中に適用する 1 つ以上の品質向上パッチを指定します。 参照： [ビルド変数](../environment/variables-build.md#quality_patches).<!--MCLOUD-6375-->

## v2002.1.1

リリース日：2020 年 6 月 25 日（PT）

- ![新規アイコン](../../assets/new.svg) **インフラストラクチャの更新**—

   - ![新規アイコン](../../assets/new.svg) **ログの改善** – 重要なデプロイ・エラーに終了コードを割り当て、エラー・メッセージ通知とログ・イベントに終了コードを公開することで、ログ・トラッキング機能を向上させます。 参照： [ece-tools のエラーメッセージ参照](../dev-tools/error-reference.md).<!-- MCLOUD-5637, 5531-->

   - ![新規アイコン](../../assets/new.svg) データベースダンプのプロセスの改善（`vendor/bin/ece-tools db-dump`）を追加し、データベースダンプ操作がアプリケーションをメンテナンスモードに切り替え、コンシューマーキュープロセスを停止し、ダンプを開始する前に cron ジョブを無効にするように、ログメッセージを更新しました。<!--MCLOUD-5324, MCLOUD-2062-->

   - ![修正アイコン](../../assets/fix.svg) ステージング環境および実稼動環境にデプロイする場合に、プロジェクト URL が正しく更新されるように問題を修正しました。 さて、 `ece-tools` は、を使用してルートの URL を指定します `primary:true` プロジェクトのルート設定で設定された属性。 参照： [変数のデプロイ](../environment/variables-deploy.md#update_urls).<!--MCLOUD-5883-->

   - ![修正アイコン](../../assets/fix.svg) が更新されました `generate.xml` パッチを適用するためのシナリオ作成ワークフローを作成します。 Adobe Commerceを更新して、 `di:compile` および `module:refresh` 失敗する手順。<!--MCLOUD-5941-->

   - ![修正アイコン](../../assets/fix.svg) インストールプロセスでを誤って返す問題を修正しました `Crypt key missing` エラー。 この `crypt/key` 値は、インストール時に自動的に生成されます。<!--MCLOUD-6120-->

- ![新規アイコン](../../assets/new.svg) **サービスの更新**—

   - ![新規アイコン](../../assets/new.svg) PHP 7.4 と MariaDB 10.4 のサポートを追加しました。<!--MAGECLOUD-2957, MCLOUD-4144-->

- ![新規アイコン](../../assets/new.svg) **環境変数の更新**—

   - ![新規アイコン](../../assets/new.svg) さんがを追加しました **SCD_USE_BALER** クラウドインフラストラクチャー上のAdobe Commerceのビルドプロセス中に、JavaScript のバンドル用の Baler モジュールを有効にするための変数です。 の変数の説明を参照してください。 [ビルド変数](../environment/variables-build.md#scd_use_baler).<!-- MCLOUD-3456, MCLOUD-3457-->

   - ![新規アイコン](../../assets/new.svg) さんがを追加しました **REDIS_BACKEND** Adobe Commerce 2.3.5 以降の Redis キャッシュ用 Redis バックエンドモデルを設定するための環境変数。 の変数の説明を参照してください。 [変数のデプロイ](../environment/variables-deploy.md#redis_backend).<!--MCLOUD-5721, MCLOUD-5865-->

- ![新規アイコン](../../assets/new.svg) **CLI コマンドの更新**—

   - ![新規アイコン](../../assets/new.svg) より詳細なログを記録するためのオプションを追加して、次の CLI コマンドを更新しました。

      - `app:config:dump`
      - `app:config:import`
      - `module:enable`

     各呼び出しのログレベルは、の設定によって決まります [`VERBOSE_COMMANDS`](../environment/variables-build.md#verbose_commands) 内の変数 `.magento.env.yaml` ファイル。<!--MCLOUD-3503-->

- ![新規アイコン](../../assets/new.svg) **検証の改善**—

   - ![新規アイコン](../../assets/new.svg) **Elasticsearch 7.x の互換性チェック**- Elasticsearch 7.x ソフトウェアの互換性チェックのElasticsearch検証を更新しました。<!--MCLOUD-5542-->

   - ![新規アイコン](../../assets/new.svg) **サービスバージョンと EOL 検証チェックを更新しました** – インストールされているサービスバージョンをAdobe Commerce 2.4 の要件と照合するように検証を更新しました。<!--MCLOUD-6144-->

   - ![修正アイコン](../../assets/fix.svg) 検証の問題を修正して、デプロイ後の警告メッセージが `post-deploy` にフック設定がありません `.magento.app.yaml` ファイル：

     ```text
     Your application does not have the "post_deploy" hook enabled.
     ```

     <!--MCLOUD-4077-->

   - ![新規アイコン](../../assets/new.svg) **Zend フレームワークの依存関係の検証を追加しました**- Laminas プロジェクトに移行した Zend フレームワークに composer 依存関係の検証を追加しました。 必要な依存関係が見つからない場合は、ビルドプロセス中に次のエラーメッセージが表示されます。

     ```text
     Required configuration is missing from the autoload section of the composer.json file.
     Add ("Laminas\Mvc\Controller\Zend\": "setupsrc/ Zend/Mvc/Controller/") to
     the `autoload -> psr-4` section. Then, re-run the "composer update" command locally, and
     commit the updated composer.json and composer.lock files.
     ```

     参照： [Zend フレームワークの依存関係の検証](../development/commerce-version.md#verify-zend-framework-composer-dependencies).<!--MCLOUD-4094-->

   - ![新規アイコン](../../assets/new.svg) **の検証を追加しました `env.php` ファイルとデータ** – のチェックを追加しました。 `env.php` インストールおよびアップグレードプロセス中にファイルとデータが生成されます。<!--MCLOUD-5991-->

      - 次の場合 `env.php` インストールにファイルがありません。また、 `crypt/key` の値が指定されていません `.magento.app.yaml` ファイルの場合、デプロイメントは次の通知で失敗します。

        ```text
        The crypt/key key value does not exist in the ./app/etc/env.php file or the CRYPT_KEY cloud environment variable``Missing crypt key for upgrading Magento`.
        ```

      - インストールにが含まれていない場合 `env.php` ファイルが存在するか、設定にキャッシュタイプが 1 つだけ含まれる場合、 `cron:enable` アップグレード プロセス中にコマンドを実行し、ファイルを復元します `cache_types`. 次の通知がログに追加されます。

        ```text
        Magento state indicated as installed but configuration file app/etc/env.php was empty or did not exist.
        Required data will be restored from environment configurations and from the .magento.env.yaml file.
        ```

## v2002.1.0

リリース日：2020 年 2 月 6 日（PT）

- ![新規アイコン](../../assets/new.svg) **インフラストラクチャの更新**—

   - ![新規アイコン](../../assets/new.svg) **Cloud Docker for Commerce用に個別のパッケージを追加しました。**—Docker パッケージをから分離しました `ece-tools` コード品質を維持し、独立したリリースを提供するためのパッケージ。 に関連する更新と修正 `ece-tools` は、以下から管理されます [magento-cloud-docker](https://github.com/magento/magento-cloud-docker) GitHub リポジトリ。<!--MAGECLOUD-2927-->

   - ![新規アイコン](../../assets/new.svg) **更新されたパッチ適用機能**— パッチ適用機能を ECE-Tools パッケージから別のパッケージに移動しました。 [magento-cloud-patches](https://github.com/magento/magento-cloud-patches) パッケージ。 デプロイメント中、 `ece-tools` は、新しいパッケージを使用してパッチを適用します。 参照： [クラウドパッチのリリースノート](cloud-patches.md).<!--MAGECLOUD-4567-->

   - ![新規アイコン](../../assets/new.svg) **Composer の依存関係を更新** – さんが、 `composer.json` の依存関係を持つ、クラウドインフラストラクチャー上のAdobe Commerceのファイル `magento/magento-cloud-docker` パッケージ。 さて、 `ece-tools` に含まれるすべてのパッケージの依存関係を次に示します [`Cloud Tools Suite for Commerce`](cloud-tools-suite.md). これらのパッケージは、のインストール時または更新時に自動的にインストールおよび更新されます `ece-tools`.

- ![新規アイコン](../../assets/new.svg) **シナリオベースのデプロイメントのサポート**—<!--MAGECLOUD-4101-->

   - ![新規アイコン](../../assets/new.svg) これで、XML 設定ファイルを使用して、ビルド、デプロイ、デプロイ後のプロセスをカスタマイズし、デフォルトの設定を上書きまたはカスタマイズできるようになりました。

   - ![新規アイコン](../../assets/new.svg) **がを変更しました `hooks` での設定`.magento.app.yaml`** – を更新しました `hooks` シナリオベースのデプロイメントをサポートする設定形式。 以前の ECE-Tools 2002.0.x リリースのレガシー形式は、引き続きサポートされます。 ただし、シナリオベースのデプロイメント機能を使用するには、新しい形式に更新する必要があります。 参照： [シナリオベースのデプロイメント](../deploy/scenario-based.md#add-scenarios-using-build-and-deploy-hooks).

>[!NOTE]
>
>ECE-Tools バージョン 2002.1.0 に更新する前に、を確認してください。 [後方互換性のない変更](backward-incompatible-changes.md) クラウドインフラストラクチャー上のAdobe Commerce プロジェクト設定またはプロセスの更新が必要になる可能性のある変更点について説明します。

- ![新規アイコン](../../assets/new.svg) **サービスの更新**—

   - ![新規アイコン](../../assets/new.svg) PHP 7.3 のサポートを追加。<!--MAGECLOUD-4022-->

   - ![新規アイコン](../../assets/new.svg) RabbitMQ 3.8 がサポートされるようになりました。<!--MAGECLOUD-4674-->

   - ![新規アイコン](../../assets/new.svg) 各サービスの EOL 日付に対してインストール済みサービスバージョンを確認するための検証を追加しました。 現在は、サービスのバージョンが提供終了（EOL）日から 3 か月以内の場合は通知が届き、提供終了（EOL）日が過去の場合は警告が届きます。<!--MAGECLOUD-4076-->

   - ![修正アイコン](../../assets/fix.svg) Elasticsearch設定の問題を修正して、すべての環境で正しいElasticsearchが設定されるようにしました。<!--MAGECLOUD-4474-->

>[!NOTE]
>
>参照： [サービスのバージョン](../services/services-yaml.md#service-versions) クラウドインフラストラクチャー上のAdobe Commerceで使用されるサービスのリストと、クラウドテンプレートとのバージョン互換性について説明します。

- ![新規アイコン](../../assets/new.svg) **環境変数の更新**—

   - ![新規アイコン](../../assets/new.svg) の機能を拡張しました `WARM_UP_PAGES` 特定の製品ページのキャッシュのプリロードをサポートする環境変数。 で展開された定義を参照してください。 [デプロイ後変数](../environment/variables-post-deploy.md#warm_up_pages) トピック。<!--MAGECLOUD-4444-->

   - ![新規アイコン](../../assets/new.svg) さんがを追加しました `ERROR_REPORT_DIR_NESTING_LEVEL` でのエラーレポートデータ管理を簡素化する環境変数 `<magento_root>/var/report/` ディレクトリ。 の変数の説明を参照してください。 [ビルド変数](../environment/variables-build.md#error_report_dir_nesting_level) トピック。

   - ![修正アイコン](../../assets/fix.svg) がを削除しました `SCD_EXCLUDE_THEMES`, `STATIC_CONTENT_THREADS`,`DO_DEPLOY_STATIC_CONTENT`、および `STATIC_CONTENT_SYMLINK` 環境変数。 参照： [後方互換性のない変更](backward-incompatible-changes.md#environment-configuration-changes).<!--MAGECLOUD-4407, MAGECLOUD-3873-->

   - ![修正アイコン](../../assets/fix.svg) Elastic Suite 設定プロセスの問題を修正して、設定時にデフォルト設定が期待どおりに上書きされるようにしました `ELASTICSUITE_CONFIGURATION` を使用せずに変数をデプロイ `_merge` オプション。<!--MAGECLOUD-4388-->

- ![新規アイコン](../../assets/new.svg) **CLI コマンドの更新**—

   - ![新規アイコン](../../assets/new.svg) **新しい cron コマンド** – クラウドインフラストラクチャ上のAdobe Commerceで、を使用して、cron 処理を手動で管理できるようになりました `cron:disable` および `cron:enable` コマンド。 disable コマンドを使用して、アクティブな cron プロセスをすべて停止し、すべての cron ジョブを無効にします。 準備が整ったら enable コマンドを使用して、cron ジョブを再度有効にします。 参照： [Cron ジョブの無効化](../application/crons-property.md#disable-cron-jobs).

   - ![新規アイコン](../../assets/new.svg) **エラーレポートの改善**—ECE-Tools の処理中に発生する CLI コマンドの障害に対するログ機能が向上しました。<!--MAGECLOUD-4849-->

   - ![新規アイコン](../../assets/new.svg) **非推奨のビルドコマンドの削除** – 次のビルド コマンドを削除しました。 `m2-ece-build`, `m2-ece-deploy`, `m2-ece-scd-dump`、および名前の変更 `ece-tools docker` に対するコマンド `ece-docker`. 参照： [後方互換性のない変更](backward-incompatible-changes.md)<!--MAGECLOUD-4392-->

- ![新規アイコン](../../assets/new.svg) 非推奨（廃止予定）を削除 `build_options.ini` ファイルおよびが存在する場合にビルドに失敗する検証を追加しました。 の使用 [.magento.env.yaml](../environment/configure-env-yaml.md) ファイル：ビルドオプションを設定します。

- ![修正アイコン](../../assets/fix.svg) でビルドプロセスが失敗する問題を修正しました `config.php` ファイルは空です。<!--MAGECLOUD-4127-->

## 2002.0.23

リリース日：2020 年 2 月 27 日（PT）

- ![修正アイコン](../../assets/fix.svg) との互換性の問題を修正しました `ece-tools` 実稼動モードでオンデマンド静的コンテンツ生成が正常に完了しなかった 2002.0.x リリース。

## 以前のリリース

を参照してください。 [リリースノートアーカイブ](cloud-release-archive.md) バージョン 2002.0.22 以前の場合。
