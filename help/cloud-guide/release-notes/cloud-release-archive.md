---
title: ece-tools のリリースノートアーカイブ
description: ece-tools のアーカイブされた改善点について説明します。
hide: true
hidefromtoc: true
recommendations: noDisplay, noCatalog
exl-id: 96663d2f-ef00-4490-ad2e-06e8041b228e
source-git-commit: 8a0523f1714b6ea41887e99b5c31294cf5e5255e
workflow-type: tm+mt
source-wordcount: '7147'
ht-degree: 0%

---

# ece-tools のリリースノートアーカイブ

>[!NOTE]
>
>以下のリリースノートでは、 `ece-tools` v2002.0.22 以降。 詳しくは、 [Cloud Tools Suite のリリースノート](cloud-tools-suite.md) 最新の更新を取得するには `ece-tools` および他のクラウドパッケージ。

## v2002.0.22

The `ece-tools` 2002.0.22 リリースでは、 `ece-tools` ～の放出を切り離すためのパッケージ `Adobe Commerce on cloud infrastructure` ECE-Tools リリースのパッチ。 このリリース以降、パッチと重要な修正は、 [`magento/magento-cloud-patches`](https://github.com/magento/magento-cloud-patches) パッケージ ( `ece-tools` パッケージ。 リリースの更新をスケジュールしたり、コミュニティへの貢献を活用したりする際の複雑さを軽減するために、以下の変更を加えました。

- ![新しいアイコン](../../assets/new.svg) **ECE-Tools パッケージの変更**

   - ![新しいアイコン](../../assets/new.svg) Adobe Commerceパッチを `ece-tools` 新しい [`magento/magento-cloud-patches`](https://github.com/magento/magento-cloud-patches) コンポーザーパッケージ。

   - ![新しいアイコン](../../assets/new.svg) 更新された `composer.json` ファイルを `ece-tools` パッケージを使用して、 `magento/magento-cloud-patches` v1.0.0 パッケージ。

   - ![修正アイコン](../../assets/fix.svg) 次の問題を修正しました： `ece-tools` バージョン 2.3.2-p2 以降のセキュリティ専用リリース上にパッチセットを適用すると、パッチ適用プロセスが破損する問題を修正しました。 この問題は、 [セキュリティ専用パッチ](https://devdocs.magento.com/guides/v2.3/release-notes/bk-release-notes.html#security-only-patches).<!--MAGECLOUD-4661-->

- ![修正アイコン](../../assets/fix.svg) **パッチと重要な修正** — クラウド環境をに更新します。 `ece-tools` バージョン 2002.0.22：次のパッチと重要な修正を適用する場合。 これらのパッチは、 `magento/magento-cloud-patches` v1.0.0 パッケージ。

   - ![修正アイコン](../../assets/fix.svg) **2.3.1.x および 2.3.2.x リリース用の Page Builder セキュリティパッチ**- Page Builder プレビューで、未認証ユーザーがネットワーク (RCE) を介した任意のコード実行のトリガーに使用できる一部のテンプレートメソッドにアクセスでき、グローバルな情報リークが発生する問題を修正しました。 この問題は、Adobe Commerceバージョン 2.3.1 および 2.3.2 でサポートされていないバージョンの Page Builder を使用している場合に発生する可能性があります。<!--MAGECLOUD-4649-->

   - ![修正アイコン](../../assets/fix.svg) **MSI パッチ** — 在庫管理にデフォルトの在庫設定を使用する際にインデックス作成エラーやパフォーマンスの問題が発生する問題を修正しました。<!--MAGECLOUD-4428-->

   - ![修正アイコン](../../assets/fix.svg) **新しいメールインターフェイスの後方互換性**- `Magento\Framework\Mail\EmailMessageInterface` Adobe Commerce v2.3.3 で導入された PHP インターフェイス。このパッチの範囲では、新しい `EmailMessageInterface` は、古い `MessageInterface`、およびAdobe Commerceコアモジュールは、 `MessageInterface`.<!--MAGECLOUD-4422-->

   - ![修正アイコン](../../assets/fix.svg) **カタログのページネーションはElasticsearch6.x では機能しません** — カタログ検索エンジンとしてElasticsearch6.x を使用しているお客様に影響する、検索結果のページネーションに関する重大な問題を修正しました。<!--MAGECLOUD-4448-->

## v2002.0.21

- ![新しいアイコン](../../assets/new.svg) **Docker の更新**—

   - ![新しいアイコン](../../assets/new.svg) **新しい Docker 画像** — バージョン 2.3.3 以降でサポートされます。<!-- MAGECLOUD-3345 -->

      - PHP バージョン 7.3.<!-- MAGECLOUD-4017 -->

      - Vanish キャッシュ 6.2.0<!-- MAGECLOUD-4017 -->

   - ![新しいアイコン](../../assets/new.svg) で指定したカスタムフック設定を適用するサポートを追加しました。 `.magento.app.yaml` Docker 環境で使用できます。 以前は、Docker 環境ではデフォルトのフック設定のみがサポートされていました。<!-- MAGECLOUD-3505-->

   - ![新しいアイコン](../../assets/new.svg) Docker のビルド中に Docker ENV ファイルは生成されなくなり、 `docker:config:convert` コマンドは非推奨です。 対応するデータが `docker-compose.yml` ファイル。<!-- MAGECLOUD-3816-->

   - ![新しいアイコン](../../assets/new.svg) **更新された PHP イメージ**-PHP Docker イメージに Node.js を追加し、node、npm、および grunt-cli の機能をサポートしました。<!-- MAGECLOUD-3953 -->

- ![新しいアイコン](../../assets/new.svg) **環境変数の更新**-

   - ![新しいアイコン](../../assets/new.svg) 追加された **LOCK_PROVIDER** deploy 変数を使用して、ロックプロバイダーを設定します。これにより、重複する cron ジョブや cron グループが起動されるのを防ぎます。 変数の説明については、 [変数をデプロイ](../environment/variables-deploy.md#lock_provider) トピック。<!-- MAGECLOUD-4052 -->

   - ![新しいアイコン](../../assets/new.svg) 追加された **CONSUMERS_WAIT_FOR_MAX_MESSAGES** 環境変数を使用して、コンシューマーがメッセージキューからのメッセージを、 `CRON_CONSUMERS_RUNNER` 環境変数を使用して、cron ジョブを管理します。 変数の説明については、 [変数をデプロイ](../environment/variables-deploy.md#consumers_wait_for_max_messages) トピック。<!-- MAGECLOUD-4071 -->

   - ![修正アイコン](../../assets/fix.svg) データベースのデッドロックエラーが発生する原因となっていた問題を修正しました。 `consumers_runner` cron ジョブは、異なるノードで同じコンシューマーの複数のインスタンスを開始します。 次に、 [**CRON_CONSUMERS_RUNNER**](../environment/variables-deploy.md#cron_consumers_runner) 変数を環境にデプロイする場合は、 `consumers_runner` ジョブが `single-thread` オプションを使用して、1 つのノードで各コンシューマーの 1 つのインスタンスを起動します。<!-- MAGECLOUD-3913 -->

   - ![修正アイコン](../../assets/fix.svg) 次の項目に影響する問題を修正しました： [**WARM_UP_PAGES**](../environment/variables-post-deploy.md#warm_up_pages) の機能です。デフォルトのストア URL を使用します。 ここで、 `config:show:default-url` コマンドはベース URL を取得できないので、MAGENTO_CLOUD_ROUTES 変数の URL が使用されます。<!-- MAGECLOUD-3866 -->

- ![新しいアイコン](../../assets/new.svg) が返すログ情報を更新しました。 `module:refresh` コマンドを使用します。 これで、 `cloud.log` ファイル。<!-- MAGECLOUD-2514 -->

- ![新しいアイコン](../../assets/new.svg) バージョンの互換性の検証と、Adobe Commerceのバージョンとインストール済みのサービス (Elasticsearch、など ) の互換性の問題に関する警告通知が改善されました。 [!DNL RabbitMQ]、レディス、DB。<!-- MAGECLOUD-3535 -->

- ![新しいアイコン](../../assets/new.svg) RabitMQ バージョン 3.8 のサポートを追加しました。<!-- MAGECLOUD-4674-->

- ![新しいアイコン](../../assets/new.svg) 新しいAdobe Commerce 2.3.3 および 2.2.10 リリースでサポートされているバージョンを反映するよう、サービスの互換性に関するインタラクティブ検証を更新しました。 詳しくは、 [必要システム構成](https://devdocs.magento.com/guides/v2.4/install-gde/system-requirements.html) （内） _インストールガイド_ を参照してください。<!-- MAGECLOUD-4018 -->

- ![修正アイコン](../../assets/fix.svg) デプロイフェーズの cron ジョブ管理プロセスが、既に完了している cron ジョブを停止しようとした場合に返されるログメッセージを改善し、この問題がエラーでないことを明確にしました。 ログレベルを `INFO` から `DEBUG`.<!-- MAGECLOUD-3653-->

- ![修正アイコン](../../assets/fix.svg) を実行する際の問題を修正しました。 `setup:upgrade` コマンドを使用して、 `app:config:import` タスク。<!-- MAGECLOUD-3806 -->

- ![新しいアイコン](../../assets/new.svg) ファイルハンドラーのデフォルトのログレベルをに変更しました。 `debug` ログの詳細を減らすには、 [!DNL Cloud Console]を介して呼び出すことができます。<!-- MAGECLOUD-3871 -->

- ![修正アイコン](../../assets/fix.svg) ビルド中に静的コンテンツのデプロイメントでエラーが発生する問題を修正しました。 インストール後、および `ece-tools` configdump。管理者ユーザーのロケールが `config.php` ファイル。 現在は、管理者ユーザーのデフォルトロケールが `config.php` ファイル。<!-- MAGECLOUD-3957 -->

- ![修正アイコン](../../assets/fix.svg) 修正された `Undefined index error` それは `magento-cloud` セキュア URL(https) が設定されていない環境では、CLI コマンドが失敗します。 これで、セキュア URL が利用できない場合、ECE-Tools パッケージはベース URL(http) を使用します。<!-- MAGECLOUD-4009 -->

## v2002.0.20

- ![新しいアイコン](../../assets/new.svg) **Docker の更新**—

   - ![新しいアイコン](../../assets/new.svg) これで、 `ece-tools` パッケージを Docker 環境で作成します。 詳しくは、 [アプリケーションテスト](https://devdocs.magento.com/cloud/docker/docker-test-magecloud-pkg-code.html).<!-- MAGECLOUD-3129/3684 -->

   - ![新しいアイコン](../../assets/new.svg) を使用して PHP モジュールを設定するサポートを追加しました。 `.magento.app.yaml` ファイル。 任意 [PHP 拡張は、 `.magento.app.yaml` ファイル](https://devdocs.magento.com/cloud/project/magento-app-php-application.html#php-extensions) は、Docker PHP コンテナで使用できるようになります。<!-- MAGECLOUD-3357 -->

   - ![新しいアイコン](../../assets/new.svg) Docker コマンドラインの操作性を向上させるための新しいコマンドが追加されました。 詳しくは、 [`bin/magento-docker` Docker リファレンスの「 」セクション](https://devdocs.magento.com/cloud/docker/docker-quick-reference.html#magento-cloud-docker-cli).<!-- MAGECLOUD-3569 -->

   - ![新しいアイコン](../../assets/new.svg) ローカルホストと Docker の間で開発中に Mutalen.io を使用してファイルを同期する機能が追加されました。<!-- MAGECLOUD-3559 -->

   - ![修正アイコン](../../assets/fix.svg) Docker 環境を使用する際のデフォルトパスを修正しました。 これで、SSH を使用して Docker コンテナにログインすると、 `/app` ディレクトリに格納されます。<!-- MAGECLOUD-3582 -->

   - ![修正アイコン](../../assets/fix.svg) Sodium ライブラリをバージョン 1.0.11 からバージョン 1.0.18 に更新し、Sodium PHP 拡張機能を更新しました。<!-- MAGECLOUD-3832 -->

     >[!WARNING]
     >
     >Adobe Commerce an cloud infrastructure のお客様が必要とする要件 [Adobe Commerceサポートチケットを送信する](https://support.magento.com/hc/en-us/articles/360000913794#submit-ticket) :Adobe Commerce 2.3.2 にアップグレードする前に、Pro 実稼動およびステージング環境で libsodium パッケージをアップグレードする場合。現在、スターター環境をAdobe Commerce 2.3.2 にアップグレードすることはできません。

   - ![修正アイコン](../../assets/fix.svg) 追加された `analysis-icu` そして `analysis-phonetic` Elasticsearchプラグインをすべての Docker 画像に使用できます。<!-- MAGECLOUD-3446 -->

   - ![修正アイコン](../../assets/fix.svg) 検証機能の向上： `docker:build` 」コマンドを使用する場合、オプションを使用する際に値を指定する必要があります。 また、 `docker:build run` コマンドを使用します。<!-- MAGECLOUD-3486 & MAGECLOUD-3678 -->

- ![新しいアイコン](../../assets/new.svg) **環境変数の更新**—

   - ![新しいアイコン](../../assets/new.svg) を使用して、データベーステーブルのプレフィックスのサポートを追加しました。 [DATABASE_CONFIGURATION 環境変数](../environment/variables-deploy.md#database_configuration).<!-- MAGECLOUD-2901 -->

   - ![新しいアイコン](../../assets/new.svg) 追加された **FORCE_UPDATE_URLS** 変数をデプロイして、Pro および Starter の実稼動およびステージング環境にデプロイする際にベース URL を更新します。 定義については、 [変数をデプロイ](../environment/variables-deploy.md#force_update_urls) コンテンツ。<!-- MAGECLOUD-3602 -->

   - ![新しいアイコン](../../assets/new.svg) 追加された **TTFB_TESTED_PAGES** デプロイ後の変数を設定する _最初のバイトまでの時間_ ページテストを使用して、クラウドインフラストラクチャにデプロイされたサイトでのアプリケーションのパフォーマンスを確認します。 変数の説明については、 [デプロイ後の変数](../environment/variables-post-deploy.md).<!-- MAGECLOUD-3643 -->

   - ![修正アイコン](../../assets/fix.svg) マルチスレッド SCD で、静的コンテンツのデプロイメントでランダムエラーが発生する問題を修正しました。 回避策には、 **SCD_THREADS** 変数を `1`. 必要に応じてカウントを増やすことができます。 詳しくは、 [変数をデプロイ](../environment/variables-deploy.md#scd_threads) そして [変数を作成](../environment/variables-build.md#scd_threads).<!-- MAGECLOUD-3611 -->

   - ![修正アイコン](../../assets/fix.svg) 次の項目を設定できます。 **WARM_UP_PAGES** 環境変数を使用して、単一のページ、複数のドメインおよび複数のページをキャッシュします。 拡張された定義を [デプロイ後の変数](../environment/variables-post-deploy.md#warm_up_pages) コンテンツ。<!-- MAGECLOUD-3258 -->

- ![修正アイコン](../../assets/fix.svg) 追加された `pub/static/.htaccess` ファイルを除外リストに追加します。 [PHOENIX MEDIA GmbH の Björn Kraus によって提出された修正](https://github.com/magento/ece-tools/pull/455).<!-- MAGECLOUD-3545/Github#455 -->

- ![修正アイコン](../../assets/fix.svg) すべての検証メッセージが「 」として表示されていたエラーを修正しました。 `Critical` 1 つ以上の重要レベルバリデーターがエラーを返した場合。<!-- MAGECLOUD-3178 -->

- ![修正アイコン](../../assets/fix.svg) ベース URL がデータベースに存在しない場合にデプロイメントに失敗する問題を修正しました。<!-- MAGECLOUD-3075 -->

- ![新しいアイコン](../../assets/new.svg) 新しい **`env:config:show`command** から `ece-tools` 環境サービス、ルートまたは変数を表示するパッケージ。 詳しくは、 [サービス、ルート、変数](https://devdocs.magento.com/cloud/reference/ece-tools-reference.html#services-routes-and-variables). [Vladimir Kerkhoff が提出した機能](https://github.com/magento/ece-tools/pull/486).<!-- MAGECLOUD-3451 -->

- ![修正アイコン](../../assets/fix.svg) でAdobe Commerce 2.2.6 以前をインストールしようとすると重大なエラーが発生する問題を修正しました。 `ece-tools` シェルリファクタリング後に開発。<!-- MAGECLOUD-3665 -->

- ![修正アイコン](../../assets/fix.svg) Adobe Commerce 2.1.x および 2.2.x のインストールで、廃止されたバージョンの Carbon の使用に関する警告が表示されて失敗する問題を修正しました。<!-- MAGECLOUD-3704 -->

- ![修正アイコン](../../assets/fix.svg) の値が減少しました `cloud.log` シェル出力のログレベル `info` から `debug`.<!-- MAGECLOUD-3277 -->

- ![修正アイコン](../../assets/fix.svg) 追加された `--remove-definers (-d)` オプションを `ece-tools db-dump` コマンドを使用して、ダンプファイルから定義子を削除します。<!-- MAGECLOUD-3510 -->

## v2002.0.19

- ![修正アイコン](../../assets/fix.svg) を上書きする問題を修正しました。 `env.php` ファイルをデプロイする際に、カスタム設定が失われます。 この更新により、Adobe Commerce on cloud インフラストラクチャで、 `env.php` ファイルをすべてのデプロイメントに含め、カスタム設定を保存します。<!-- MAGECLOUD-3668 -->

## v2002.0.18

- ![新しいアイコン](../../assets/new.svg) **Docker の更新**—

   - ![新しいアイコン](../../assets/new.svg) これで、Docker 環境は、 [.magento.app.yaml ファイルの crons プロパティ](https://devdocs.magento.com/cloud/project/magento-app-properties.html#crons).<!-- MAGECLOUD-3150 -->

   - ![新しいアイコン](../../assets/new.svg) **新しい Docker コンテナ** — が追加されました。 [TLS 終了プロキシコンテナ](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#varnish-container) HTTPS 経由での Vanish SSL の終了を容易にする。<!-- MAGECLOUD-2890 -->

   - ![新しいアイコン](../../assets/new.svg) **新しい Docker イメージ**—Gulp および Jasmine JS ユニットテストなどのその他の機能をサポートするために、Node.js イメージを追加しました。<!-- MAGECLOUD-3345 -->

   - ![新しいアイコン](../../assets/new.svg) **Docker ビルドモード** — これで、 [実稼動モードまたは開発者モード](https://devdocs.magento.com/cloud/docker/docker-launch.html#set-the-launch-mode). 開発者モードは、書き込み可能なファイルシステム権限をフルに持つアクティブな開発をサポートします。<!-- MAGECLOUD-3152/3511 -->

   - ![修正アイコン](../../assets/fix.svg) Docker のデプロイが `Name or service not known` 使用できないサービスに対してキャッシュが設定されている場合にエラーが発生します。 これで、 [`.magento/services.yaml` ファイル](https://devdocs.magento.com/cloud/project/services.html). Docker 設定ジェネレーターが、 `docker/config.php.dist` ファイルを自動的に作成します。<!-- MAGECLOUD-3369 -->

   - ![新しいアイコン](../../assets/new.svg) サービスの互換性を確保するためのインタラクティブな検証機能を追加しました。 現在は、要求されたサービスがAdobe Commerceのバージョンまたは他のサービスと互換性がない場合、 _インタラクティブモード_ 続行するには、メッセージと選択肢をユーザーに表示します。 詳しくは、 [サービスバージョン](https://devdocs.magento.com/cloud/docker/docker-containers.html#service-containers) は Docker で使用できます。 以下を使用します。 `-n` CICD の目的でインタラクティビティをスキップするオプションが含まれています。<!-- MAGECLOUD-3251 -->

   - ![修正アイコン](../../assets/fix.svg) Docker の作成の問題を修正しました `db-dump` 既存のダンプを消去するコマンド。<!-- MAGECLOUD-3366 -->

   - ![修正アイコン](../../assets/fix.svg) Redis が割り当てられていた問題を修正しました。 `session`, `default`、および `page_cache` 同じデータベース ID にキャッシュストレージを保存します。<!-- MAGECLOUD-3172 -->

- ![新しいアイコン](../../assets/new.svg) **環境変数の更新**—

   - ![新しいアイコン](../../assets/new.svg) 新しい **ELASTICSUITE\_CONFIGURATION** 環境変数は、デプロイメント間でカスタマイズしたサービス設定を保持します。 定義については、 [変数をデプロイ](../environment/variables-deploy.md#elasticsuite_configuration) コンテンツ。<!-- MAGECLOUD-3205 -->

   - ![新しいアイコン](../../assets/new.svg) 追加された **SCD_MAX_EXECUTION_TIMEOUT** 環境変数を使用して、静的コンテンツのデプロイメントを完了するまでの時間を `.magento.env.yaml` ファイル。 定義については、 [変数をデプロイ](../environment/variables-deploy.md#scd_max_execution_time)、 [変数を作成](../environment/variables-build.md#scd_max_execution_time)、および [グローバル変数](../environment/variables-global.md#scd_max_execution_time).<!-- MAGECLOUD-2822 -->

      - ![新しいアイコン](../../assets/new.svg) 追加された **MAGENTO_CLOUD_LOCKS_DIR** 環境変数を使用して、クラウドインフラストラクチャ上のロックプロバイダーのマウントポイントへのパスを設定します。 ロックプロバイダーは、重複する cron ジョブや cron グループの起動を防ぎます。 この変数は、Adobe Commerceバージョン 2.2.5 以降でサポートされ、自動的に設定されます。 の定義を参照してください。 [クラウド変数](../environment/variables-cloud.md).<!-- MAGECLOUD-3135 -->

      - ![修正アイコン](../../assets/fix.svg) 変更された **SCD_THREADS** 環境変数のデフォルト値を使用して、検出された CPU スレッド数に基づいて最適な値を自動的に決定します。 更新された定義については、 [変数をデプロイ](../environment/variables-deploy.md#scd_threads) そして [変数を作成](../environment/variables-build.md#scd_threads).<!-- MAGECLOUD-3382 -->

- ![修正アイコン](../../assets/fix.svg) クラウドインフラストラクチャバージョン 2002.0.16 でAdobe Commerceにアップグレードする際にエラーが発生する、DB 分離メカニズムのパッチの問題を修正しました。<!-- MAGECLOUD-3383 -->

- ![修正アイコン](../../assets/fix.svg) を置き換えるパッチを追加しました。 _Google Image Charts_ 次を使用 _画像グラフ_. DevBlog の記事を参照してください。 [M1 のGoogle Image Charts の廃止と更新](https://community.magento.com/t5/Magento-DevBlog/Google-Image-Charts-deprecation-and-update-for-M1/ba-p/125006).<!-- MAGECLOUD-3456 -->

- ![修正アイコン](../../assets/fix.svg) 次の検証が追加されました： [SEARCH_CONFIGURATION 変数](../environment/variables-deploy.md#search_configuration). 「エンジン」オプションが設定されていない場合にデプロイが失敗し、 `_merge` は必須ではありません。<!-- MAGECLOUD-3470 -->

- ![修正アイコン](../../assets/fix.svg) 例外の発生後に機密データが公開される問題を修正しました。 これで、機密情報が適切にマスクされるようになりました。<!-- MAGECLOUD-3525 -->

- ![修正アイコン](../../assets/fix.svg) Magento Open Sourceパッケージの耐障害性設定を改善しました。 Adobe Commerceが Redis からデータを読み取れない場合 `slave` 例えば、レディスから読み取られる `master` インスタンス。 詳しくは、 [REDIS_USE_SLAVE_CONNECTION](../environment/variables-deploy.md#redis_use_slave_connection).<!-- MAGECLOUD-2899 -->

## v2002.0.17

>[!NOTE]
>
>The `ece-tools` バージョン 2002.0.17 には、重要なセキュリティパッチが含まれています。 詳しくは、 [テクニカルリソース：Magento Open Sourceパッチ](https://magento.com/tech-resources/download#download2288).

- ![新しいアイコン](../../assets/new.svg) **サービスの更新** — 次のAdobe Commerceバージョンでサポートされます： 2.2.8 以降、2.2.x、2.3.1 以降、2.3.x

   - Elasticsearchバージョン 6.x のサポートを追加しました。<!-- MAGECLOUD-3196 -->

   - Redis バージョン 5.0 のサポートを追加しました。

- ![新しいアイコン](../../assets/new.svg) **新しい Docker 画像** — 次のサービスを Docker ビルドに追加しました。

   - ELASTICSEARCH6.5<!-- MAGECLOUD-3196 -->

   - Redis 5.0<!-- MAGECLOUD-3223 -->

- ![新しいアイコン](../../assets/new.svg) **新しい環境変数** — 以前は、SCD 圧縮に対してハードコードされたタイムアウトがありました。 これで、 **SCD_COMPRESSION_TIMEOUT** 環境変数。 詳しくは、 [変数を作成](../environment/variables-build.md#scd_compression_timeout) そして [変数をデプロイ](../environment/variables-deploy.md#scd_compression_timeout) コンテンツ。<!-- MAGECLOUD-2870 -->

- ![修正アイコン](../../assets/fix.svg) 追加された `--use-rewrites` オプションを install コマンドに設定し、ストアフロントおよび管理者アクセスで生成されたリンクに対する web サーバーの書き換えを使用してセキュリティと顧客体験を向上させます。<!-- MAGECLOUD-3246 -->

- ![修正アイコン](../../assets/fix.svg) タイムスタンプを `var/log/install_upgrade.log` ファイルに保存し、インストールおよびアップグレードイベントの日付を表示します。<!-- MAGECLOUD-2895 -->

## v2002.0.16

- ![新しいアイコン](../../assets/new.svg) **Docker の更新**—

   - 現在は、Docker 環境で生成されるデフォルトのサービス設定は、クラウドテンプレートのデフォルト設定と同じです。<!-- MAGECLOUD-3025 -->

   - を使用して、Docker 環境からメールを送信できます。 `sendmail` サービス。<!-- MAGECLOUD-2907 -->

   - 次の機能を追加しました。 [Xdebug の設定](https://devdocs.magento.com/cloud/docker/docker-development-debug.html) Cloud Docker 環境でデバッグする場合。<!-- MAGECLOUD-2891 -->

   - Web サービスの権限で、 `docker-compose.yml` ファイル。<!-- MAGECLOUD-2883 -->

- ![新しいアイコン](../../assets/new.svg) **アップグレードの改善** — 次の条件を満たしていることを確認する検証機能が追加されました。 `autoload` プロパティを `composer.json` ファイルには、Adobe Commerce v2.3 にアップグレードする前に必要な設定変更が含まれています。詳しくは、 [バージョンをアップグレード](https://devdocs.magento.com/cloud/project/project-upgrade.html).<!-- MAGECLOUD-2392 -->

- ![新しいアイコン](../../assets/new.svg) 静的コンテンツをデプロイする際の圧縮プロセスに、（ネイティブに生成またはカスタマイズされた）すべてのアセットが含まれるようになり、 [`build:transfer` セクション](https://devdocs.magento.com/cloud/project/magento-app-properties.html#hooks). 以前は、静的アセットのカスタム縮小とバンドルを適用する前に圧縮プロセスが実行されていました。 [Tryzens Limited の Rafael Garcia Lepper によって提出されたフィックス](https://github.com/magento/ece-tools/pull/413).<!-- MAGECLOUD-3104 -->

- ![修正アイコン](../../assets/fix.svg) 追加のデータベースとサービスの関係を設定した直後にデプロイメント中に発生していたデータベース接続エラーを修正しました。 また、この修正は、Starter 用の Commerce Reporting の設定プロセス中に発生した問題にも対処します。 スターターの場合、このアップグレードは、コマースレポートを使用するための「必須」です。<!-- MAGECLOUD-3035 -->

- ![修正アイコン](../../assets/fix.svg) デプロイプロセスが失敗する原因となっていた、データベース設定の検証の問題を修正しました。<!-- MAGECLOUD-3003 -->

- ![修正アイコン](../../assets/fix.svg) 制約を適切なバージョンの `symfony/yaml` 使用するパッケージ [PHP 定数](https://devdocs.magento.com/cloud/project/magento-env-yaml.html#php-constants). 定数解析は、 `symfony/yaml` 3.2 より前のパッケージバージョン。 [Vladimir Kerkhoff が提出した修正](https://github.com/magento/ece-tools/pull/404).<!-- MAGECLOUD-2956 -->

- ![新しいアイコン](../../assets/new.svg) **環境設定の確認**—PHP のバージョンを確認し、最新の推奨バージョンを使用していない場合に警告を表示する検証機能を追加しました。<!--MAGECLOUD-2903-->

- ![修正アイコン](../../assets/fix.svg) JSON 変数の処理形式が正しくない問題を修正しました。 現在は、JSON 変数が構文エラーを引き起こした場合、 `cloud.log` ファイルとデプロイメントは、引き続きデフォルトの変数を使用します。<!-- MAGECLOUD-2851 -->

- ![修正アイコン](../../assets/fix.svg) Redis サービスを無効にした直後に、デプロイメント中に発生していた接続エラーを修正しました。<!-- MAGECLOUD-2747 -->

- ![新しいアイコン](../../assets/new.svg) **変更のログ記録** — を更新しました。 [ログレベル](../environment/log-handlers.md#log-levels) から `Info` から `Notice` 次のビルドおよびデプロイプロセスイベントの場合：<!--MAGECLOUD-2925-->

   - でインストールされたモジュールを調整するプロセスの開始と終了 `composer.json` 共有設定を使用 `app/etc/config.php` ファイル

   - 設定の検証プロセスの開始と終了

   - の開始と終了 `setup:di:compile` クラスを生成するプロセス

- ![新しいアイコン](../../assets/new.svg) **新しい環境変数**—

   - **[RESOURCE_CONFIGURATION デプロイ変数](../environment/variables-deploy.md#resource_configuration)** — この変数を使用して、リソース名をデータベース接続にマッピングします。<!-- MAGECLOUD-3026 & MAGECLOUD-2963-->

   - **[X_FRAME_CONFIGURATION グローバル変数](../environment/variables-global.md#x_frame_configuration)** — この変数を使用して `X-Frame-Options` ページ内でAdobe Commerceページをレンダリングするためのヘッダーの設定 `<frame>`, `<iframe>`または `<object>`.<!-- MAGECLOUD-3048 -->

- ![修正アイコン](../../assets/fix.svg) **環境変数の更新** — 次の環境変数を変更しました。

   - **[WARM_UP_PAGES](../environment/variables-post-deploy.md)**— Adobe Commerceストアに定義されているすべてのドメインで、指定したページのキャッシュをプリロードする機能が追加されました。 以前は、サイトが複数のドメインで設定されていた場合、デプロイ後処理でデフォルト以外のドメインで指定したページのキャッシュをプリロードできず、デプロイ後のログに次のエラーが返されていました。 `ERROR: Warming up failed: <uri>`<!-- MAGECLOUD-2466 -->

   - **SCD_COMPRESSION_LEVEL** — ドキュメントとサンプルを更新しました。 `.magento.env.yaml` SCD 圧縮レベルの正しいデフォルト値を持つファイル。 詳しくは、 [変数を作成](../environment/variables-build.md#scd_compression_level) そして [変数をデプロイ](../environment/variables-deploy.md#scd_compression_level) コンテンツ。<!-- MAGECLOUD-2823 -->

   - **SCD_EXCLUDE_THEMES** — この環境変数は非推奨です。 以下を使用します。 [SCD_MATRIX](../environment/variables-build.md#scd_matrix) を使用して、テーマの設定を制御します。<!--MAGECLOUD-2882-->

   - **SCD\_MATRIX**- SCD_MATRIX が異なる文字ケースを含むテーマ値を無視した場合に発生する問題を防ぐために、検証プロセスを修正しました。 詳しくは、 [変数を作成](../environment/variables-build.md#scd_matrix) そして [変数をデプロイ](../environment/variables-deploy.md#scd_matrix) コンテンツ。<!-- MAGECLOUD-2904 -->

   - **管理変数**—<!-- MAGECLOUD-2573/MAGECLOUD-2848 -->

      - 環境変数を使用して管理者ユーザーの資格情報を管理する際のセキュリティを強化しました。 アップグレード時に管理者の資格情報を上書きするために、環境変数 ADMIN_EMAIL、ADMIN_USERNAME、ADMIN_PASSWORD を使用できなくなりました。 管理パネルにアクセスできない場合は、 _パスワードを忘れた場合_ 機能または `admin:user:create` 新しい管理者ユーザーを作成する CLI コマンド。 詳しくは、 [管理パネルへのアクセス](https://devdocs.magento.com/cloud/onboarding/onboarding-tasks.html#admin).

      - パッチをアップグレードまたは適用する際に ADMIN_EMAIL は不要になりました。

## v2002.0.15

- ![新しいアイコン](../../assets/new.svg) **Docker の更新**—

   - これで、Docker Generator は、 `.magento.app.yaml` および `.magento/services.yaml` 次の場合の設定ファイル [Docker 環境の構築](https://devdocs.magento.com/cloud/docker/docker-config.html). ビルドパラメーターを使用して、別のサービスバージョンを選択できます。<!-- MAGECLOUD-2888 -->

   - PHP 7.2 イメージの追加 — Cloud Docker に PHP 7.2 のサポートを追加し、 [Launch Docker 設定](https://devdocs.magento.com/cloud/docker/docker-config.html) を含めるには、 `docker:build --php` オプションを使用して、お使いのバージョンのAdobe Commerceと互換性のある PHP のバージョンを指定します。<!-- MAGECLOUD-2799 -->

   - が追加されました。 [Cron コンテナ](https://devdocs.magento.com/cloud/docker/docker-containers-cli.html#cron-container) PHP-CLI イメージに基づく。<!-- MAGECLOUD-2565 -->

   - Docker のビルドに次のサービスを追加しました。

      - [!DNL RabbitMQ] 3.5 および 3.7<!-- MAGECLOUD-2567 & 2889-->

      - Elasticsearch1.7、2.4 および 5.2<!-- MAGECLOUD-2569 & 2887 -->

      - Redis 3.2 および 4.0<!-- MAGECLOUD-2886 -->

- ![新しいアイコン](../../assets/new.svg) **PHP 定数を使用した設定** — のサポートが追加されました。 [PHP 定数](https://devdocs.magento.com/cloud/project/magento-env-yaml.html#php-constants) （内） `.magento.env.yaml` 設定ファイル。<!-- MAGECLOUD- 2575 -->

- ![新しいアイコン](../../assets/new.svg) **新しい環境変数** — デフォルトでは、実稼動環境でのみ有効なGoogle Analyticsです。 ステージング環境と統合環境でGoogle Analyticsを有効にするには、  [ENABLE_GOOGLE_ANALYTICS 環境変数](../environment/variables-deploy.md#enable_google_analytics).<!--MAGECLOUD-2879-->

- ![修正アイコン](../../assets/fix.svg) カスタマイズした Cron 設定が `env.php` ファイルを再デプロイした後に作成します。 現在は、カスタム cron 設定は、安全に `env.php` ファイル。<!-- MAGECLOUD-2923 -->

- ![修正アイコン](../../assets/fix.svg) メッセージの不整合および [ログレベル](../environment/log-handlers.md#log-levels) ビルド、デプロイ、およびデプロイ後のフェーズの場合。 開始および終了ログメッセージレベルを **情報** から **通知** すべてのフェーズとサブフェーズに対して。 必要に応じて、ログメッセージの開始と終了を追加しました。<!-- MAGECLOUD-2919 -->

- ![修正アイコン](../../assets/fix.svg) 設定時にデプロイ後段階を開始できない cron プロセスに関する問題を修正しました。 これで、デプロイ後のフックを有効にした場合、デプロイ後の段階の最初に cron プロセスが再度有効になります。<!-- MAGECLOUD-2862 -->

- ![修正アイコン](../../assets/fix.svg) カスタムデータベース設定を指定した場合に、Adobe Commerceが正常にインストールされなかった問題を修正しました。 以前は、インストールプロセスで、 [MAGENTO_CLOUD_RELATIONSHIP 変数](../environment/variables-cloud.md) カスタマイズした接続情報を [DATABASE_CONFIGURATION 環境変数](../environment/variables-deploy.md#database_configuration).<!--MAGECLOUD-2736-->

- ![修正アイコン](../../assets/fix.svg) 修正済み `config:dump` コマンドを使用して、各 web サイトロケールが `system` のセクション `config.php` ファイル。<!--MAGECLOUD-2740-->

- ![修正アイコン](../../assets/fix.svg) の問題を修正しました。 _ウォームアップ_ デプロイ後の段階でエラーが発生しました。<!--MAGECLOUD-2797-->

- ![修正アイコン](../../assets/fix.svg) ファイルが正しく生成されない問題を修正しました。 `setup:di:compile` プロセスがAmazon Pay モジュールに影響を与えた問題を修正しました。<!--MAGECLOUD-2850-->

## v2002.0.14

- ![新しいアイコン](../../assets/new.svg) **理想的な状態を確認**- `ideal-state` ウィザードで、各デプロイメント中に現在の設定を検証し、ダウンタイムなしで迅速なデプロイメントを実現するために、設定の更新に関する明確な手順を提供するようになりました。<!--MAGECLOUD-2372-->

- ![修正アイコン](../../assets/fix.svg) **PCI コンプライアンス** — サードパーティのメッセージングサービスと接続する際に Transport Layer Security(TLS) バージョン 1.2 が必要となるように、クラウドインフラストラクチャ上のAdobe Commerceのメッセージングプロトコルを更新しました。 TLS バージョン 1.2 をサポートしていないメッセージサービスを使用している場合は、サービスをアップグレードする必要があります。 そうしないと、Adobe Commerceアプリケーションが E メールを送信するために Message Server に接続しようとすると、次のエラーメッセージが表示されます。 `Unable to connect via TLS`.<!--MAGECLOUD-2521-->

- ![新しいアイコン](../../assets/new.svg) **デプロイメントの改善** — ステージング環境または実稼動環境に `dev`, `debug`または `debug_logging` オプションを有効にして、過剰なログアクティビティによるパフォーマンスの問題を防ぎます。<!--MAGECLOUD-2517-->

- ![修正アイコン](../../assets/fix.svg) **デプロイメントの修正点**—

   - 現在は、デプロイフェーズの開始時にメンテナンスモードが有効になり、終了時に無効になります。 展開が失敗した場合、展開の問題が解決されるまで、サイトはメンテナンスモードのままです。 以前は、デプロイメントに失敗した場合でも、サイトが実稼動モードに戻っていました。<!--MAGECLOUD-2603-->

      - デプロイメントの次の問題に対して、デプロイフェーズの検証チェックを修正し、エラーレベルをからダウングレードしました。 `CRITICAL` から `WARNING` を設定して、デプロイメントを完了します。 以前は、これらの問題によりデプロイメントが失敗していました。

      - 環境設定に、デプロイまたはクラウド変数に間違った値が含まれています。

   - クラウドインフラストラクチャ上のElasticsearchのバージョンは、クラウドインフラストラクチャ上のAdobe Commerceでサポートされている elasticsearch/elasticsearch モジュールのバージョンとは互換性がありません。 詳しくは、 [Elasticsearchのトラブルシューティング記事](https://support.magento.com/hc/en-us/articles/360015758471-Deployment-fails-or-interrupts-with-cloud-log-error-Elasticsearch-version-is-not-compatible-with-current-version-of-magento) ( Adobe Commerce Support Knowledgebase )。<!--MAGECLOUD-2600-->

   - 共有設定の問題を修正しました。 `app/etc/config.php` ～を引き起こしたファイル `recursion detected` デプロイ中にエラーが発生しました。<!--MAGECLOUD-2173-->

- ![修正アイコン](../../assets/fix.svg) **Cron 関連の修正点**—

   - デフォルト（1 分）以外の cron 頻度を指定した場合にジョブが実行されない cron スケジュールの問題を修正しました。<!--MAGECLOUD-2602-->

   - デプロイフェーズで、デプロイメント中に cron ジョブの実行を続行できる問題が修正されました。これにより、データベースのロックやその他の重要な問題が発生する場合がありました。 現在は、デプロイフェーズが開始する前にすべての cron ジョブが停止し、デプロイメントが完了した後に再起動します。&lt;!—MAGECLOUD—2537—>

   - バージョン 2.2.x の cron ジョブワークフローで、フリーズした cron ジョブのロックが解除され、デプロイメントを開始する前に停止できるようになりました。 以前は、凍結された Cron ジョブによって、デプロイメントが停止していました。<!--MAGECLOUD-2501-->

- ![修正アイコン](../../assets/fix.svg) のフォーマットを変更しました。 `config.php` によって生成されたファイル `vendor/bin/ece-tools config:dump` Adobe Commerceのコーディング標準に準拠するために、短い配列構文と 4 スペースのインデントを使用するコマンドを使用します。<!--MAGECLOUD-2527-->

- ![修正アイコン](../../assets/fix.svg) 次の場合に発生するデプロイメントエラーを修正しました： `.magento.env.yaml` 次を含む `{{ base_url }}` および `{{ unsecure_base_url }}` Adobe Commerce on cloud infrastructure プロジェクトのデフォルトの URL 設定の代わりに、web 設定のプレースホルダーを使用します。/<!--MAGECLOUD-2607-->

## v2002.0.13

- ![新しいアイコン](../../assets/new.svg) **ダウンタイムなしのデプロイメントを有効にする**—Adobe Commerce on cloud infrastructure は、デプロイメント中に必要なデータベース変更を含むリクエストをキューに追加し、デプロイメントが完了するとすぐに変更を適用するようになりました。 リクエストは、セッションが失われないように、最大 5 分間保持できます。 詳しくは、 [静的コンテンツデプロイメントオプションを使用して、クラウドでのデプロイメントのダウンタイムを短縮](https://support.magento.com/hc/en-us/articles/360004861194-Static-content-deployment-options-to-reduce-deployment-downtime-on-Cloud).<!--MAGECLOUD-2169-->

- ![新しいアイコン](../../assets/new.svg) **Docker Compose for Cloud** — 次の改善を行いました： [Docker のセットアップと設定](https://devdocs.magento.com/cloud/docker/docker-config.html) プロセス：

   - コマンド — を追加しました。`docker:config:convert` を使用して、PHP 設定ファイルを Docker ENV 形式に変換し、環境設定を簡単に行うことができます。 次に、PHP の設定ファイルを Docker ディレクトリにコピーし、Docker ENV ファイルに変換します。 詳しくは、 [Docker を起動](https://devdocs.magento.com/cloud/docker/docker-config.html).<!--MAGECLOUD-2359-->

   - クラウドインフラストラクチャのAdobe Commerceのインストールプロセスで、読み取り専用ファイルシステムと読み取り/書き込み可能ファイルシステムの両方へのデプロイがサポートされるようになり、クラウドファイルシステムをより緊密にエミュレートできます。 詳しくは、 [Docker の設定](https://devdocs.magento.com/cloud/docker/docker-config.html).&lt;!—MAGECLOUD—2357—>

   - **Redis サービスのサポート**—Redis イメージが追加され、Docker コンテナにデプロイされ、Docker のインストールと連携するように自動的に設定されます。&lt;!—MAGECLOUD—2442—>

   - これで、Cloud Docker を使用する際の DB ダンプ機能が可能になりました [データベースコンテナ](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#database-container). また、 [ファイルを共有](https://devdocs.magento.com/cloud/docker/docker-containers.html#sharing-data-between-host-machine-and-container) ホストマシンとこれを用いたコンテナ間 `docker/mnt` ディレクトリ。<!-- MAGECLOUD-2577 -->

   - **Vanish サービスのサポート**— Wanish イメージが追加され、Docker コンテナに自動的にデプロイされます。 デプロイ後は、Adobe Commerceのベストプラクティスに従って手動で Vanrish を設定できます。 詳しくは、 [Vanish の設定と使用](https://devdocs.magento.com/guides/v2.3/config-guide/varnish/config-varnish.html).&lt;!—MAGECLOUD—2358—>

   - セキュアサイトアクセス — Adobe Commerceストアおよび管理パネルにアクセスするための SSL サポートが追加されました。&lt;!—MAGECLOUD—2360—>

- ![修正アイコン](../../assets/fix.svg) **Adobe Commerce on cloud infrastructure 拡張機能のサポートの改善** — クラウドインフラストラクチャ上のAdobe Commerceの guzzlehttp/guzzle パッケージの最小バージョン要件をダウングレードしました。 [composer.json ファイル](https://devdocs.magento.com/cloud/reference/cloud-composer.html) をバージョン 6.2 に設定し、 `ece-tools` パッケージは、より多くの拡張機能と互換性があります。<!--MAGECLOUD-2205-->

- ![新しいアイコン](../../assets/new.svg) **ビルドフェーズ中にAdobe Commerceアプリケーションにカスタム変更を適用する** — ビルドフェーズを 2 つの異なるプロセスに分割し、デプロイメント用にアプリケーションをパッケージ化する前に、生成された静的コンテンツにフックを使用してカスタムの変更を適用できるようにします。 The _build:generate_ プロセスは、コードを生成し、パッチを適用して、静的コンテンツを生成します。 The _ビルド：転送_ プロセスは、生成されたコードと静的コンテンツを最終的な宛先に転送します。 詳しくは、 [アプリケーションフック](https://devdocs.magento.com/cloud/project/magento-app-properties.html#hooks).<!--MAGECLOUD-2363-->

- ![修正アイコン](../../assets/fix.svg) **環境設定のチェック** — クラウドインフラストラクチャ上にAdobe Commerceを構築してデプロイする前に、バージョンの非互換性と設定エラーに関する警告をお客様に表示するよう、環境設定の検証を改善しました。

   - サポートされていない、または非推奨の環境変数および値を識別するための、バージョン固有の検証を追加しました。<!--MAGECLOUD-2183-->

   - ユーザーにElasticsearch設定の問題を警告するElasticsearchの互換性チェックを追加しました。 現在は、サーバー上のElasticsearchサービスのバージョンがAdobe Commerceと互換性がない場合、デプロイメントが失敗します。 以前は、Elasticsearchのバージョンに互換性がない場合でも展開が成功し、サイトの展開後に製品カタログの問題が発生していました。<!--MAGECLOUD-2389-->

     非互換性は、次の方法で解決できます： [サポートチケットの送信](https://devdocs.magento.com/cloud/trouble/trouble.html) Elasticsearchを互換性のあるバージョンにアップグレードする場合、またはAdobe Commerce設定を変更して、ElasticsearchPHP クライアントの互換性のあるバージョンを指定する場合。

      - Adobe Commerceバージョン 2.1.x から 2.2.2 にアップグレードするには、Elasticsearchをバージョン 2.4 にアップグレードします。

      - Adobe Commerceバージョン 2.2.3 以降の場合は、Elasticsearchをバージョン 5.2 にアップグレードします。

      - Elasticsearch1.x または 2.x があり、アップグレードしない場合は、composer.json のAdobe CommerceElasticsearchPHP クライアントのバージョン要件を、次のように更新します。 `"elasticsearch/elasticsearch": "~2.0"`.

   - 環境変数の検証を改善し、ビルド、デプロイ、デプロイ後のフェーズで競合を引き起こす可能性のある設定を特定しました。 例えば、静的コンテンツデプロイメントのグローバル設定がビルドまたはデプロイフェーズの設定と競合する場合は、インストールおよびアップグレードプロセス中に警告メッセージが表示されます。<!--MAGECLOUD-2156-->

- ![修正アイコン](../../assets/fix.svg) **環境変数の更新** — 次の環境変数を変更しました。

   - **[SKIP_MINIFICATION グローバルHTML](../environment/variables-global.md#skip_html_minification)** — デフォルト値をに変更しました。 `true` オンデマンドHTMLコンテンツの縮小を有効にして、ステージング環境および実稼動環境にデプロイする際のダウンタイムを最小限に抑えます。 この設定は、ダウンタイムなしのデプロイメントに必要です。<!--MAGECLOUD-2435-->

   - **[CLEAN_STATIC_FILES デプロイ変数](../environment/variables-deploy.md#clean_static_files)** — 環境変数 CLEAN_STATIC_FILES の設定に基づいて、ビルドフェーズで生成された静的コンテンツのクリーン静的ファイル処理を管理する機能を追加しました。 以前は、ビルドフェーズで生成された静的コンテンツファイルは、常にクリーンアップされていました。<!--MAGECLOUD-1506-->

- ![修正アイコン](../../assets/fix.svg) **ログ** — ログメッセージを改善し、ログサイズを小さくするために、次の変更を行いました。

   - デプロイメントの失敗ログエントリに、環境設定でデバッグレベルのログが指定されていない場合でも、失敗を引き起こす操作からのコマンド出力が含まれるようになりました。 詳しくは、 [`MIN_LOGGING_LEVEL`](../environment/variables-global.md#min_logging_level).<!--MAGECLOUD-2489-->

   - ファイルシステムが読み取り専用状態になっているため、一部の拡張子で必要な生成されたファクトリを正しく生成できない場合に発生するデプロイメントエラーのログを追加しました。<!--MAGECLOUD-2209-->

   - インタラクティブなプログレスバーを使用するセットアップコマンドによって発生する、デプロイログのサイズが減少し、フォーマットの問題が修正されました。<!--MAGECLOUD-2402-->

   - 不要な詳細を排除し、一部のログ文の優先度レベルを更新しました。<!--MAGECLOUD-2227-->

- ![修正アイコン](../../assets/fix.svg) **Cron 固有の修正**—

   - cron キューが急速にいっぱいになった場合に発生する可能性のあるパフォーマンスの問題やデプロイメントの失敗を防ぐために、履歴有効期間のデフォルト cron ジョブ設定を 3d（4320 分）から 1 時間（60 分）に変更しました。<!--MAGECLOUD-2427-->

   - デプロイフェーズ中の cron ジョブ管理プロセスを改善し、データベースのロックやその他の重要な問題を防ぎました。 現在は、デプロイフェーズ中にすべての cron ジョブが停止し、デプロイメントが完了した後に再起動します。<!--MAGECLOUD-2445-->

   - Adobe Commerceバージョン 2.2.0 以降の cron ジョブで起動されたコンシューマーをスケジュールするロックメカニズムの問題を修正し、cron ジョブが重複するコンシューマーを起動しないようにしました。<!--MAGECLOUD-2464-->

- ![修正アイコン](../../assets/fix.svg) の問題を修正しました。 [静的コンテンツ圧縮プロセス](../environment/variables-intro.md) (`gzip`) が引き起こした `not overwritten` および `no such file or directory` デプロイメントプロセス中に圧縮ファイルを参照する際にエラーが発生しました。<!-- MAGECLOUD-2182-->

- ![修正アイコン](../../assets/fix.svg) を修正しました。 `php ./vendor/bin/ece-tools config:dump` 余分なセクションを削除するコマンド `config.php` ストアのロケールが指定されていない場合、ダンププロセス中にファイルを保存します。 これで、環境間で設定ファイルを簡単に移動できます。 次に更新した後： `ece-tools` v2002.0.13、古いバージョンを再生成 `config.php` 改善された `config:dump` コマンドを使用します。 詳しくは、 [ストア設定の構成管理](https://devdocs.magento.com/cloud/live/sens-data-over.html).<!--MAGECLOUD-2444-->

- ![修正アイコン](../../assets/fix.svg) デプロイフェーズで、 `.magento/routes.yaml` からのファイルのリダイレクト [心尖](https://blog.cloudflare.com/zone-apex-naked-domain-root-domain-cname-supp/) ドメインから `www` ドメイン。<!--MAGECLOUD-2556-->

- ![修正アイコン](../../assets/fix.svg) の問題を修正しました。 `_merge` オプション [`SEARCH_CONFIGURATION`](../environment/variables-deploy.md#search_configuration) 変数を使用して、 `engine` 更新された `.magento.env.yaml` 設定ファイル。 現在は、結合操作によって、更新された `.magento.env.yaml` あなたが `engine` パラメーター。<!--MAGECLOUD-2520-->

- ![修正アイコン](../../assets/fix.svg) クラウドインフラストラクチャバージョン 2.2.1 以降でAdobe Commerceのセッションロックが誤って有効になっていたため、パフォーマンスが低下し、タイムアウトが発生する可能性がある Redis 設定の問題を修正しました。 現在は、セッションロックはデフォルトで無効になっています。 この問題は、 `disable_locking` Redis セッションハンドラーパッケージ v1.3.4 で導入されたパラメーター。 詳しくは、 [colinmollenhour/php-redis-session-abstract パッケージ](https://github.com/colinmollenhour/php-redis-session-abstract).<!-- MAGECLOUD-2515-->

## v2002.0.12

- ![新しいアイコン](../../assets/new.svg) **Docker Compose for Cloud** — コマンドを追加しました —`docker:build` — を生成します。 [Docker Compose](https://devdocs.magento.com/cloud/docker/docker-config.html) クラウドからの設定 `ece-tools` リポジトリ。<!-- MAGECLOUD-2250 -->

- ![新しいアイコン](../../assets/new.svg) **ロケールを変更** — 設定の書き出しと読み込みを行わなくても、ストアのロケールを変更できるようになりました。 アプリケーションが「実稼動」で、SCD_ON_DEMAND が有効な場合は、store および admin ロケールのオプションを使用できます。<!-- MAGECLOUD-2019 -->

- ![新しいアイコン](../../assets/new.svg) <!-- MAGECLOU-1998 -->**サイトマップとロボット**— [workflow](../store/robots-sitemap.md) を追加する `robots.txt` ファイルを作成し、 `sitemap.xml` ファイルを作成し、インフラストラクチャに変更を加える必要がない。

- ![新しいアイコン](../../assets/new.svg) **ウィザード**—2 つ追加しました。 [ウィザード](../deploy/smart-wizards.md) クラウド設定に役立つ情報を以下に示します。<!-- MAGECLOUD-1910 -->

   - `ideal-state` — デプロイメントのダウンタイムを最小限に抑えるための理想的な状態を設定

   - `master-slave` — データベースと Redis のロードバランシングを設定します。

- ![新しいアイコン](../../assets/new.svg) **モジュールの更新** — クラウドコマンドを追加 —`module:refresh` — ビルド時に自動的に行われるのと同様に、無効にされたモジュールや明示的に有効にされていないモジュールを有効にします。<!-- MAGECLOUD-1521 -->

- ![新しいアイコン](../../assets/new.svg) を使用して、サービスの設定を結合または上書きする機能を追加しました。 `_merge` オプション [キャッシュ](../environment/variables-deploy.md#cache_configuration), [SESSION](../environment/variables-deploy.md#session_configuration), [キュー](../environment/variables-deploy.md#queue_configuration)、および [検索](../environment/variables-deploy.md#search_configuration) 設定。<!-- MAGECLOUD-2105 -->

- ![新しいアイコン](../../assets/new.svg) **環境設定サンプルファイル** — を追加しました。 `.magento.env.yaml` 各環境変数の詳細な説明と可能な値を含む ECE-Tools パッケージのサンプルファイル。<!-- MAGECLOUD-1908 -->

   - また、 `.magento.env.yaml` 予期しない値が原因でデプロイメントプロセスの失敗を防ぐ設定です。 エラーが発生した場合、次のような詳細なエラーメッセージが表示されるようになりました。 `Environment configuration is not valid. Please correct .magento.env.yaml file with next suggestions:`<!-- MAGECLOUD-1907 -->

- ![新しいアイコン](../../assets/new.svg) 次の機能が追加されました。 [**環境変数**](../environment/variables-intro.md):

   - 新しい [SCD_MATRIX](../environment/variables-deploy.md#scd_matrix) 環境変数を使用して、デプロイするテーマファイルの量を減らすことができます。<!-- MAGECLOUD-1501 -->

   - 追加された [DATABASE_CONFIGURATION](../environment/variables-deploy.md#database_configuration) 環境変数を使用して、デプロイメント用にデータベース接続をカスタマイズします。<!-- MAGECLOUD-2047 -->

   - 新しい [MIN_LOGGING_LEVEL](../environment/variables-global.md#min_logging_level) 変数は、コードを変更せずに、すべての出力ストリームの最小ログレベルを上書きします。<!-- MAGECLOUD-2129 -->

- ![修正アイコン](../../assets/fix.svg) デプロイとデプロイ後段階の間にダウンタイムが発生する問題を修正しました。 これで、デプロイ後のフェーズが開始されます _即時_ デプロイフェーズが終了した後。

- ![修正アイコン](../../assets/fix.svg) 正常に終了した cron ジョブ ( `status = success`（スケジュールから）<!-- MAGECLOUD-2268 -->

- ![修正アイコン](../../assets/fix.svg) の問題を修正しました。 `post_deploy` フックを使用して、プロジェクトのデプロイ後段階ではなく、デプロイ段階でキャッシュをクリアしました。<!-- MAGECLOUD-2113 -->

- ![修正アイコン](../../assets/fix.svg) SCD を複数のロケールで使用すると同じロケールが生成される問題を修正しました。 `js-translation.json` 各ロケール用のファイル。<!-- MAGECLOUD-2034 -->

- ![修正アイコン](../../assets/fix.svg) 最適化された `db:dump` コマンドを `ece-tools` テーブルのロックを回避し、速度を向上させるためのパッケージ。<!-- MAGECLOUD-2033 -->

## v2002.0.11

>[!NOTE]
>
>ECE-Tools バージョン 2002.0.11 は、2.2.4 との互換性を保つために必要です。

- ![新しいアイコン](../../assets/new.svg) **非マスターノードへの読み取り専用接続の設定** — このリリースでは、読み取り専用トラフィック (  [MariaDB](../environment/variables-deploy.md#mysql_use_slave_connection)) をクリックします。<!--MAGECLOUD-143 -->[レディス](../environment/variables-deploy.md#redis_use_slave_connection) そして <!--MAGECLOUD-143 -->

- ![新しいアイコン](../../assets/new.svg) **設定ウィザード** — 静的コンテンツデプロイメントの設定を検証するためのウィザードが追加されました。 詳しくは、 [スマートウィザード](../deploy/smart-wizards.md).<!--MAGECLOUD-1910 -->

- ![新しいアイコン](../../assets/new.svg) **Symfony コンソールのサポート**—Adobe Commerce 2.3 での Symfony Console 4 のサポートが追加されました。<!-- MAGECLOUD-1966 -->

- ![修正アイコン](../../assets/fix.svg) **Cron スケジュールの最適化** — キュー管理を改善し、cron 関連の問題のデバッグに役立つようにログを強化しました。<!-- MAGECLOUD-1607 -->

- ![修正アイコン](../../assets/fix.svg) デプロイメントの検証が失敗するのは、 `ADMIN_EMAIL` または `ADMIN_USERNAME` の値は既存の管理者アカウントと同じです。<!-- MAGECLOUD-1221 -->

- ![修正アイコン](../../assets/fix.svg) バージョン 2.2.x での SOLR のサポートを削除しました。 2.1.x バージョンでは、SOLR を有効にする機能が維持されます。<!-- MAGECLOUD-1282 -->

- ![修正アイコン](../../assets/fix.svg) PRO プロジェクトのステージング環境と実稼動環境の最初のインストールに、各環境に属するレコードを識別する際に競合が発生するのを防ぐために、Elasticsearch用の異なるインデックスプレフィックスが含まれるようになりました。<!-- MAGECLOUD-1489 -->

- ![修正アイコン](../../assets/fix.svg) 静的コンテンツのデプロイメント中にレガシーアーキテクチャのビルドフェーズが中断される問題を修正しました。<!-- MAGECLOUD-2021 -->

- ![修正アイコン](../../assets/fix.svg) **Cron 固有の改善点**—cron 実装を再度実行しました。<!-- MAGECLOUD-1607 -->

   - cron キューがすぐにいっぱいになる問題を修正しました。 現在は、古い Cron ジョブをより信頼性の高い方法でクリアします。

   - cron ジョブのシーケンスを再編成し、一般グループの前に別々のスレッドのすべてのジョブが起動されるようにしました。

   - cron 問題のデバッグに役立つようにログを改善しました。

   - **注意** — このリリースでは、Cron に関する多くの問題に対処しています。 現在、 _m2-hotfixes_、削除します。

- ![修正アイコン](../../assets/fix.svg) **SCD 固有の改善**—

   - 以下を使用すると、 `VERBOSE_COMMANDS` そして `SCD_COMPRESSION_LEVEL` 両方の環境変数 _ビルド_ プロイを解除する段階 (_P)。<!-- MAGECLOUD-1819 -->

   - の予期しない値が発生した場合に、ランダムエラーでデプロイメントが失敗する問題を修正しました。 `SCD_COMPRESSION_LEVEL` 環境変数。 設定の検証を改善し、意味のある通知を提供しました。 詳しくは、 [`SCD_COMPRESSION_LEVEL`](../environment/variables-build.md#scd_compression_level) を参照してください。<!-- MAGECLOUD-2043 -->

   - の動作を修正しました。 `SCD_COMPRESSION_LEVEL` 環境変数の設定フローで、オーバーライドが期待どおりに動作するようにします。<!-- MAGECLOUD-2044 -->

   - 設定できない問題を修正しました。 `SCD_THREADS` 環境変数を `.magento.env.yaml` ファイル _デプロイ_ ステージ。<!-- MAGECLOUD-2046 -->

## v2002.0.10

- ![新しいアイコン](../../assets/new.svg) **静的コンテンツのデプロイメント (SCD)** — リクエストに応じて（オンデマンドで）静的コンテンツを生成する、新しい代替のデプロイメントプロセスがあります。 これにより、最も重要なアセットを生成することで、ダウンタイムを短縮し、キャッシュ処理を改善します。<!-- MAGECLOUD-1285 -->

   - **新しい環境変数** — が追加されました。 `SCD_ON_DEMAND` 要求されたときに静的コンテンツを生成するグローバル環境変数。<!-- MAGECLOUD-1738 -->

   - **デプロイ後のフック** — が追加されました。 `post_deploy` ～に向かう `.magento.app.yaml` キャッシュをクリアし、キャッシュをプリロード (warms) するファイル _次より後_ コンテナが接続の受け入れを開始します。 これは、 [!DNL Cloud Console] スタータープロジェクト用。 必須ではありませんが、これは `SCD_ON_DEMAND` 環境変数。<!-- MAGECLOUD-1788 -->

- ![新しいアイコン](../../assets/new.svg) **最適化** — 展開時のファイルの移動またはコピーを最適化して、展開速度を向上させ、ファイルシステムの負荷を減らします。<!-- MAGECLOUD-1842 -->

- ![新しいアイコン](../../assets/new.svg) **デプロイメントログ** — デプロイメントプロセス中にログを出力する Syslog および Graylog Extended Log Format(GELF) ハンドラを有効にする機能が追加されました。 詳しくは、 [ログハンドラー](../environment/log-handlers.md).<!-- MAGECLOUD-1751 -->

- ![新しいアイコン](../../assets/new.svg) 次の機能が追加されました。 [**環境変数**](../environment/variables-intro.md):

   - `CRYPT_KEY` — データベースを移動する際に、別の環境に暗号化キーを提供します。<!-- MAGECLOUD-1556 -->

   - `SKIP_HTML_MINIFICATION`—_グローバル_ 内の静的ビューファイルのコピーをスキップする環境変数 `var/view_preprocessed` ディレクトリに格納され、要求された場合は縮小HTMLが生成されます。<!-- MAGECLOUD-1621 and MAGECLOUD-1736-->

   - `SCD_ON_DEMAND`—_グローバル_ 環境変数を使用して、要求されたときに静的コンテンツを生成できます。<!-- MAGECLOUD-1738 -->

   - `WARM_UP_PAGES` — キャッシュのプリロードに使用するページをリストできます。 新しい [デプロイ後の変数](../environment/variables-post-deploy.md).

- ![修正アイコン](../../assets/fix.svg) ローカルに適用したパッチによってインスタンス上のデプロイメントが壊れる問題を修正しました。 これで、ECE-Tools はパッチが適用されたことを検出できます。<!-- MAGECLOUD-982 -->

- ![修正アイコン](../../assets/fix.svg) JavaScript のバンドルと GZIP の機能の間の競合を修正しました。 現在は、これらの機能は正しく連携して動作します。<!-- MAGECLOUD-1735 -->

- ![修正アイコン](../../assets/fix.svg) 以前のバージョンの PHP 7.0.x を使用した場合に、ECE-Tools CLI コマンドが失敗する問題を修正しました。<!-- MAGECLOUD-1744 -->

- ![修正アイコン](../../assets/fix.svg) 複数のスレッドでコンパクトな方法を使用して静的コンテンツをデプロイできない問題を修正しました。<!-- MAGECLOUD-1822 -->

- ![修正アイコン](../../assets/fix.svg) Redis セッションのロックで管理者ログインが遅延する問題を修正しました。 また、この修正は 2.1.x で利用できます。<!-- MAGECLOUD-1853 -->

## v2002.0.9

>[!NOTE]
>
>次の条件を満たす必要があります。 [クラウドインフラストラクチャのAdobe Commerceのメタパッケージのアップグレード](../dev-tools/install-package.md#update-the-metapackage) をクリックして、今後のすべての更新を取得します。

- ![新しいアイコン](../../assets/new.svg) **ece-tools**- `ece-tools` パッケージがAdobe Commerce 2.1.x をサポートするようになりました。<!-- MAGECLOUD-1086 -->

- ![新しいアイコン](../../assets/new.svg) **Redis 設定** — これで、 [Redis の設定](../environment/variables-deploy.md#cache_configuration) 環境変数を使用したページおよびデフォルトのキャッシュと Redis セッションストレージ。<!-- MAGECLOUD-1552 -->

- ![新しいアイコン](../../assets/new.svg) **検索、AMQP、Redis のサービス改善** — すべてのサービスで同じように動作するように、サービス構成フローを統合しました。 手動での `env.php` サービスを設定するファイルはサポートされなくなりました。 環境変数または `.magento.env.yaml` ファイルを使用します。<!-- MAGECLOUD-1437 -->

- ![修正アイコン](../../assets/fix.svg) **環境変数**—

   - の使用 `env:STATIC_CONTENT_THREADS` は非推奨（廃止予定）となり、今後のリリースで削除される予定です。 以下を使用します。 [SCD_THREADS](../environment/variables-deploy.md#scd_threads) 代わりに、<!-- MAGECLOUD-1507 -->

   - The `STATIC_CONTENT_EXCLUDE_THEMES` 環境変数は非推奨でした。 次を使用する必要があります。 `SCD_EXCLUDE_THEMES` 環境変数を使用します。<!-- MAGECLOUD-1640 -->

- ![修正アイコン](../../assets/fix.svg) **ログ** — 組み込みのパッチ適用操作に関するログの作成を簡素化しました。<!-- MAGECLOUD-1674 -->

- ![修正アイコン](../../assets/fix.svg) 削除しました `developer` モードのサポートと `APPLICATION_MODE` 環境変数を設定する必要があります。<!-- MAGECLOUD-1615 -->

- ![修正アイコン](../../assets/fix.svg) Redis に関連する静的コンテンツのデプロイメントエラーが発生する問題を修正しました。 これで、マルチスレッドの静的コンテンツのデプロイメントが設計どおりに実行されます。<!-- MAGECLOUD-1630 -->

- ![修正アイコン](../../assets/fix.svg) ユーザーが管理者の設定フィールドに対する変更を保存できなかった問題を修正しました。このフィールドは、 `app:config:dump` コマンドを使用します。<!-- MAGECLOUD-1175 -->

- ![修正アイコン](../../assets/fix.svg) 以前のバージョンの `symfony/yaml` ：まだ最新バージョンと互換性がない一部のパッケージとの競合を修正するため。<!-- MAGECLOUD-1674 -->

## v2002.0.8

>[!NOTE]
>
>マージ済み `vendor/magento/ece-patches` 次を使用 `vendor/magento/ece-tools` を更新しました。 現在は、 `vendor/magento/ece-patches` 別々にパッケージ化します。

**新機能：**

- **ログの改善**<!-- MAGECLOUD-1253 -MAGECLOUD-1495 -->
   - ビルドまたはデプロイプロセスが環境変数を上書きする際の説明を改善し、ログメッセージを改善しました。
   - インストールとアップグレードの進行状況をリアルタイムで表示できるようになりました。 テールの適用 `install_update.log` ファイルを開き、進行状況を表示します。 以下に例を挙げます。

     ```bash
     tail -f var/log/install_upgrade.log
     ```

- **新しい cron コマンド** — 停止した特定の Cron ジョブを、 [`cron:unlock`](https://support.magento.com/hc/en-us/articles/360033099451) コマンドを使用します。 2.1 では使用できません。<!-- MAGECLOUD-1367 -->

- **統合構成ファイル** — 次のコードを使用して、ビルドステージとデプロイステージを設定できるようになりました： [`.magento.env.yaml`](https://devdocs.magento.com/cloud/project/magento-env-yaml.html) ファイル。<!-- MAGECLOUD-1369 -->

- **バックアップ構成ファイル** — デプロイメントプロセスによって、 `app/etc/env.php` および `app/etc/config.php` デプロイ後の設定ファイル。 また、 [新規 CLI コマンド](https://support.magento.com/hc/en-us/articles/360033182871) これらの構成ファイルをバックアップから復元する場合。<!-- MAGECLOUD-1372 -->

- **検証エラーのトラブルシューティング** — 次の場合に検証エラーを解決するために使用する必要があるコマンドを変更しました： `config.php` には、静的コンテンツをデプロイするのに十分なデータが含まれていません。 以前は、エラーメッセージが `bin/magento app:config:dump`. 今すぐ、を実行する必要があります。 `php ./vendor/bin/ece-tools config:dump`.<!-- MAGECLOUD-1491 -->

- **新しい環境変数** — 環境変数を使用してカスタムを接続できるようになりました [検索](../environment/variables-deploy.md#search_configuration) および [AMQP ベースの](../environment/variables-deploy.md#queue_configuration) サービスをサイトに送信します。<!-- MAGECLOUD-1410 -->

- スマートパッチを実装しました。 現在は、パッケージは、クラウドインフラストラクチャバージョンのAdobe Commerceではなく、パッチが適用されたパッケージバージョンに基づいてパッチを適用します。<!--MAGECLOUD-1090-->

**解決された問題：**

- ビルドエラーを引き起こしていたログの問題を修正しました。<!-- MAGECLOUD-1162 -->

- インタラクティブモードでデプロイメントを実行する際にタイムアウトの例外が発生する問題を修正しました。<!-- MAGECLOUD-1389 -->

- 静的コンテンツの生成にコンパクト戦略を使用するとエラーが発生する問題を修正しました。 2.1 では使用できません。<!-- MAGECLOUD-1446 MAGECLOUD-1485-->

- デプロイメントスクリプトで、ステージング環境と実稼動環境が正しく識別されない問題を修正しました。<!-- MAGECLOUD-1493 -->

- ネットワークの問題によってデータベース接続が中断され、インストールおよびアップグレードプロセス中にエラーが発生する問題を修正しました。<!-- MAGECLOUD-1520 -->

- 次を使用して設定ファイルを書き出せない問題を修正しました： `app:config:dump` 2 回以上。 2.1 では使用できません。<!--  MAGECLOUD-1567  -->

- Redis セッションを修正しました _ロック_ 原因となった問題 _管理者_ ログイン遅延。 2.1 では使用できません。<!--  MAGECLOUD-1582  -->

- 他の Composer ベースのパッチ適用モジュールと競合する原因となっていたバージョン管理に関連する実装の問題を修正しました。<!-- MAGECLOUD-1450 -->

- 読み込み中に PHP メモリの問題が発生する問題を修正しました。<!-- MAGECLOUD-1310 -->

- パッチを削除しました。でバグを修正しました。 `colinmollenhour/credis` v1.6：クラウドインフラストラクチャ 2.2.1 でのAdobe Commerceのサポートを有効にします。2.1 では使用できません。<!-- MAGECLOUD-1033 -->

## v2002.0.7

**解決された問題：**

- 削除しました `var/view_preprocessed` symlinking を使用して、JavaScript の縮小の競合を引き起こしていた問題を修正しました。<!-- MAGECLOUD-1454 -->

## v2002.0.6

**解決された問題：**

- 次の原因となっていた問題を修正しました： `gzip` エラー：ファイル名またはディレクトリ名にスペースが含まれている場合に発生します。<!-- MAGECLOUD-1413 -->

- デプロイメントスクリプトが適切に認識され、モジュールの依存関係を有効にできない問題を修正しました。<!-- MAGECLOUD-1424 -->

## v2002.0.5

**新機能：**

- **環境変数を使用した cron コンシューマーの設定** — 新しい `CRON_CONSUMERS_RUNNER` 環境変数。

- **構成スキャン** — ビルド/デプロイプロセス中に重要なコンポーネントをスキャンし、スキャンが失敗した場合にプロセスを停止し、サイトがメンテナンスモードになっていることによる不要なダウンタイムを防ぐようになりました。

- **ビルド/デプロイの通知** — 次の目的で使用できる設定ファイルを追加しました： [Slack/電子メール通知の設定](../environment/set-up-notifications.md) すべての環境でのビルド/デプロイアクション用。

- **静的コンテンツの圧縮** — 静的コンテンツを次を使用して圧縮するようになりました。 [gzip](https://www.gnu.org/software/gzip/) ビルドおよびデプロイフェーズの間。 この圧縮と Fastly 圧縮を組み合わせると、ストアのサイズを小さくし、デプロイメント速度を向上させることができます。 必要に応じて、 [ビルドオプション](../environment/variables-build.md) または [デプロイ変数](../environment/variables-deploy.md). 詳しくは、次のトピックを参照してください。

   - [アプリケーション環境変数](../application/variables-property.md)

   - [静的コンテンツデプロイメントパフォーマンス](../deploy/static-content.md)

   - [デプロイメントプロセス](https://devdocs.magento.com/cloud/reference/discover-deploy.html)

- **設定の管理** — 自動生成を開始しました。 `app/etc/config.php` ファイルを Git リポジトリに保存します（ビルドフェーズ中にファイルが存在しない場合）。 自動生成されたファイルには、モジュールと拡張子のリストのみが含まれます。 ファイルが既に存在する場合、ビルドフェーズは通常どおり続きます。 次の手順に従う場合 [設定管理](../store/store-settings.md) 後で、コマンドを実行すると、追加の手順を必要とせずにファイルが更新されます。 参照： [デプロイメントプロセス](https://devdocs.magento.com/cloud/reference/discover-deploy.html) を参照してください。

- **データベースダンプ** — を追加しました。 `magento/ece-tools` すべての環境でデータベースダンプを作成する CLI コマンド。 Pro plan Production 環境の場合、このコマンドは 3 つの高可用性ノードの 1 つからのみダンプするので、ダンプ中に別のノードに書き込まれた実稼動データはコピーされません。 実稼動環境でデータベースのダンプを実行する前に、アプリケーションをメンテナンスモードにすることをお勧めします。 詳しくは、 [バックアップ管理](https://devdocs.magento.com/cloud/project/project-webint-snap.html#db-dump) を参照してください。

- **Cron 間隔の制限が解除されました**—us-3、eu-3 および ap-3 地域でプロビジョニングされるすべての環境のデフォルトの Cron 間隔は 1 分です。 その他のすべての地域でのデフォルトの Cron 間隔は、Pro 統合環境の場合は 5 分、Pro ステージング環境および実稼動環境の場合は 1 分です。 既存の Cron ジョブを変更するには、で設定を編集します。 `.magento.app.yaml` または、実稼動/ステージング環境用のサポートチケットを作成します。 参照： [Cron ジョブの設定](../application/crons-property.md#set-up-cron-jobs) を参照してください。

**解決された問題：**

- を呼び出すデプロイプロセスが原因で、デプロイ時間が長くなる問題を修正しました。 `cache-clean` 操作を実行してから、静的コンテンツをデプロイします。<!-- MAGECLOUD-1327 -->

- 実稼動環境でのデプロイメントの静的コンテンツ生成手順でエラーが発生する問題を修正しました。<!-- MAGECLOUD-1322 -->

- 一部の `magento/ece-tools` 出力のログからへのコマンド `stderr`.<!-- MAGECLOUD-1264 -->

- でベース URL 値を取得できない問題を修正しました。 `env.php` 分岐した分岐で更新されないようにします。<!-- MAGECLOUD-1242 -->

- 問題を修正しました。 `magento setup:install` コマンドを使用して、セキュリティで保護されていないプレフィックス (`http://`) を使用してベース URL を保護します。<!-- MAGECLOUD-1171 -->

- パッチエラーがデプロイメントエラーを引き起こさない問題を修正しました。<!-- MAGECLOUD-1170 -->

- を防ぐ問題を修正しました。 `ece-tools` パッチを適用できない場合は、実行の停止と例外のスローから。<!-- MAGECLOUD-1152 -->

- 管理でストアの縮小を有効にした後にストアフロントを読み込む際にエラーが発生するHTMLの問題を修正しました。<!-- MAGECLOUD-1138 -->

## v2002.0.4

**解決された問題：**

- 次の操作を実行できます。 [停止した cron ジョブを手動でリセット](https://support.magento.com/hc/en-us/articles/360033099451) SSH アクセスを介してすべての環境で CLI コマンドを使用する。 デプロイメントプロセスは、cron ジョブを自動的にリセットします。<!-- MAGECLOUD-1355 -->

## v2002.0.3

**解決された問題：**

- Redis が読み取り/書き込みに時間がかかりすぎて、ページがタイムアウトする問題を修正しました。 これで、 `disable_locking` パラメーターを使用して、この問題を回避します。<!-- MAGECLOUD-1311 -->

## v2002.0.2

**解決された問題：**

- The [!DNL RabbitMQ] 設定プロセスで、必要なすべてのパラメーターが自動的に取得されるようになりました。<!-- MAGECLOUD-1246 -->

## v2002.0.1

**新機能：**

- クラウドインフラストラクチャ上のAdobe Commerceで、スコープとスコープがサポートされるようになりました。 [静的コンテンツデプロイメント戦略](https://devdocs.magento.com/guides/v2.3/config-guide/cli/config-cli-subcommands-static-deploy-strategies.html). 次の項目が追加されました： `–s` のデフォルト設定を持つパラメータ `quick` 静的コンテンツデプロイメント戦略用。 環境変数を使用できます [SCD_STRATEGY](../environment/variables-deploy.md) これらの戦略を、ビルドおよびデプロイアクションと共にカスタマイズして使用する場合。 この変数は、オプションをサポートします `standard`, `quick`または `compact`. 次を選択した場合、 `compact`を無効にした場合、 `STATIC_CONTENT_THREADS` 値 `1`（特に実稼動環境では、デプロイメントに時間がかかる場合があります）。 2.1 では使用できません。<!--- MAGECLOUD-1057 -->

- ビルドおよびデプロイアクションをキャプチャしてコンパイルするために、環境にログファイルを作成しました。 The `var/log/cloud.log` ファイルは、ルートアプリケーションディレクトリにあります。<!--- MAGECLOUD-1014 & MAGECLOUD-1023 -->

**解決された問題：**

- リファクタリングした `ece-tools` クラウドインフラストラクチャ 2.2.0 以降のAdobe Commerceとの互換性を確保するためのパッケージ。<!-- MAGECLOUD-919 & MAGECLOUD-1030 -->

- を妨げていた問題を修正しました `ece-tools` パッチを適用できない場合は、実行の停止と例外のスローから。<!-- MAGECLOUD-1186 -->

- ビルド中に依存関係インジェクション (di) コンパイルがスキップされた場合に、例外がスローされる問題を修正しました。<!-- MAGECLOUD-1047 & MAGECLOUD-1049 -->

- デプロイプロセスで、 `env.php` ファイル。<!-- MAGECLOUD-1019 -->

- デフォルトのセキュア管理者が無効になっているので、リダイレクトループが発生していた問題を修正しました。<!-- MAGECLOUD-1020 -->

## v2002.0.0

>[!WARNING]
>
>このパッケージは、クラウドインフラストラクチャ上のAdobe Commerceの他のバージョンとの互換性がなくなり、 **次の値を指定しない** を使用します。

### 初回リリース

の初回リリース `ece-tools` (Adobe Commerce on cloud infrastructure 2.2.0 用 )
