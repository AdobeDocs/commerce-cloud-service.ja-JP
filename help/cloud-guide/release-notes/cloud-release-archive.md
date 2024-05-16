---
title: ece-tools のリリースノートアーカイブ
description: ece ツールのアーカイブされた機能強化について説明します。
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
>これらのリリースノートは、の情報と更新を提供します `ece-tools` v2002.0.22 以降。 参照： [Cloud Tools Suite のリリースノート](cloud-tools-suite.md) の最新のアップデートを取得するには `ece-tools` およびその他のクラウドパッケージ。

## v2002.0.22

この `ece-tools` 2002.0.22 リリースでは、の構造が変更されました `ece-tools` のリリースを切り離すためのパッケージ `Adobe Commerce on cloud infrastructure` ece-Tools リリースのパッチ。 このリリース以降、パッチおよび重要な修正は、 [`magento/magento-cloud-patches`](https://github.com/magento/magento-cloud-patches) パッケージ（ `ece-tools` パッケージ。 リリースアップデートのスケジュール設定やコミュニティの貢献度の操作に関する複雑さを軽減するために、これらの変更を行いました。

- ![新規アイコン](../../assets/new.svg) **ECE-Tools パッケージの変更**

   - ![新規アイコン](../../assets/new.svg) Adobe Commerce パッチをから移動しました。 `ece-tools` 新規にパッケージ化 [`magento/magento-cloud-patches`](https://github.com/magento/magento-cloud-patches) composer パッケージ。

   - ![新規アイコン](../../assets/new.svg) が更新されました `composer.json` ファイル： `ece-tools` の依存関係を追加するパッケージ `magento/magento-cloud-patches` v1.0.0 パッケージ。

   - ![修正アイコン](../../assets/fix.svg) の原因となった問題を修正しました `ece-tools` バージョン 2.3.2-p2 以降のセキュリティのみのリリースにパッチセットを適用すると、パッチ適用プロセスが中断する。 この問題は、で採用された新しいバージョン管理スキームによって導入されました [セキュリティ専用パッチ](https://devdocs.magento.com/guides/v2.3/release-notes/bk-release-notes.html#security-only-patches).<!--MAGECLOUD-4661-->

- ![修正アイコン](../../assets/fix.svg) **パッチおよび重要な修正** – を使用したクラウド環境の更新 `ece-tools` バージョン 2002.0.22：次のパッチおよび重要な修正を適用します。 これらのパッチは、 `magento/magento-cloud-patches` v1.0.0 パッケージ。

   - ![修正アイコン](../../assets/fix.svg) **2.3.1.x および 2.3.2.x リリース用 Page Builder セキュリティパッチ**- ページビルダープレビューで、未認証ユーザーが一部のテンプレートメソッドにアクセスして、ネットワーク（RCE）経由の任意のコード実行をトリガーするために使用でき、グローバルな情報リークが発生する問題を修正しました。 この問題は、Adobe Commerce バージョン 2.3.1 および 2.3.2 で、サポートされていないバージョンのページビルダーを使用している場合に発生する可能性があります。<!--MAGECLOUD-4649-->

   - ![修正アイコン](../../assets/fix.svg) **MSI パッチ** – 在庫の管理にデフォルトの在庫設定を使用する際に、インデックス作成エラーやパフォーマンスの問題が発生する問題を修正します。<!--MAGECLOUD-4428-->

   - ![修正アイコン](../../assets/fix.svg) **新しいメールインターフェイスの後方互換性** – に起因する後方非互換性の問題を修正します。 `Magento\Framework\Mail\EmailMessageInterface` Adobe Commerce v2.3.3 で導入された PHP インターフェイス。このパッチの範囲では、 `EmailMessageInterface` 古いのを継承 `MessageInterface`、およびAdobe Commerceコアモジュールは、依存するに戻ります `MessageInterface`.<!--MAGECLOUD-4422-->

   - ![修正アイコン](../../assets/fix.svg) **カタログのページネーションがElasticsearch 6.x で機能しない**-Elasticsearch 6.x をカタログ検索エンジンとして使用する場合に発生する、検索結果のページネーションに関する重大な問題を修正しました。<!--MAGECLOUD-4448-->

## v2002.0.21

- ![新規アイコン](../../assets/new.svg) **Docker の更新**—

   - ![新規アイコン](../../assets/new.svg) **新しい Docker イメージ**— バージョン 2.3.3 以降でサポート<!-- MAGECLOUD-3345 -->

      - PHP バージョン 7.3.<!-- MAGECLOUD-4017 -->

      - Varnish Cache 6.2.0<!-- MAGECLOUD-4017 -->

   - ![新規アイコン](../../assets/new.svg) で指定されたカスタムフック設定を適用できるようになりました `.magento.app.yaml` Docker 環境で作成します。 以前は、Docker 環境はデフォルトのフック設定のみをサポートしていました。<!-- MAGECLOUD-3505-->

   - ![新規アイコン](../../assets/new.svg) Docker のビルド中に Docker ENV ファイルが生成されなくなりました。 `docker:config:convert` コマンドは非推奨（廃止予定）です。 対応するデータがに保存されます。 `docker-compose.yml` ファイル。<!-- MAGECLOUD-3816-->

   - ![新規アイコン](../../assets/new.svg) **更新された PHP イメージ**-node、npm、grunt-cli の機能をサポートするために、Node.js を PHP Docker イメージに追加しました。<!-- MAGECLOUD-3953 -->

- ![新規アイコン](../../assets/new.svg) **環境変数の更新**-

   - ![新規アイコン](../../assets/new.svg) さんがを追加しました **LOCK_PROVIDER** 変数をデプロイして、重複する cron ジョブや cron グループの起動を防ぐロックプロバイダーを設定します。 の変数の説明を参照してください。 [変数のデプロイ](../environment/variables-deploy.md#lock_provider) トピック。<!-- MAGECLOUD-4052 -->

   - ![新規アイコン](../../assets/new.svg) さんがを追加しました **CONSUMERS_WAIT_FOR_MAX_MESSAGES** 環境変数：を使用する際に、コンシューマーがメッセージキューからメッセージを処理する方法を設定します。 `CRON_CONSUMERS_RUNNER` cron ジョブを管理する環境変数。 の変数の説明を参照してください。 [変数のデプロイ](../environment/variables-deploy.md#consumers_wait_for_max_messages) トピック。<!-- MAGECLOUD-4071 -->

   - ![修正アイコン](../../assets/fix.svg) 次の場合にデータベースのデッドロックエラーが発生する可能性がある問題を修正しました `consumers_runner` cron ジョブが、同じコンシューマーの複数のインスタンスを異なるノードで開始する。 ここで、 [**CRON_CONSUMERS_RUNNER**](../environment/variables-deploy.md#cron_consumers_runner) 環境に変数をデプロイすると、 `consumers_runner` ジョブはを使用します `single-thread` 各コンシューマーの 1 つのインスタンスを 1 つのノードでのみ起動するオプション。<!-- MAGECLOUD-3913 -->

   - ![修正アイコン](../../assets/fix.svg) に影響する問題を修正しました [**WARM_UP_PAGES**](../environment/variables-post-deploy.md#warm_up_pages) デフォルトストア URL を使用する機能。 ここで、 `config:show:default-url` コマンドでベース URL を取得できない場合は、MAGENTO_CLOUD_ROUTES 変数の URL が使用されます。<!-- MAGECLOUD-3866 -->

- ![新規アイコン](../../assets/new.svg) が返すログ情報を更新しました `module:refresh` コマンド。 有効なモジュールの詳細なリストが `cloud.log` ファイル。<!-- MAGECLOUD-2514 -->

- ![新規アイコン](../../assets/new.svg) Adobe Commerceのバージョンとインストールされているサービスの互換性に関する問題（Elasticsearchなど）について、バージョン互換性の検証と警告通知を改善しました。 [!DNL RabbitMQ]、Redis および DB です。<!-- MAGECLOUD-3535 -->

- ![新規アイコン](../../assets/new.svg) RabitMQ バージョン 3.8 のサポートを追加。<!-- MAGECLOUD-4674-->

- ![新規アイコン](../../assets/new.svg) 新しいAdobe Commerce 2.3.3 および 2.2.10 リリースでサポートされているバージョンを反映するように、サービス互換性のインタラクティブな検証を更新しました。 参照： [必要システム構成](https://devdocs.magento.com/guides/v2.4/install-gde/system-requirements.html) が含まれる _インストールガイド_ （推奨バージョン用）。<!-- MAGECLOUD-4018 -->

- ![修正アイコン](../../assets/fix.svg) デプロイフェーズの cron ジョブ管理プロセスが、この問題がエラーではないことを明確にするために、既に完了した cron ジョブを停止しようとすると返されるログメッセージを改善しました。 がログレベルをから変更しました `INFO` 対象： `DEBUG`.<!-- MAGECLOUD-3653-->

- ![修正アイコン](../../assets/fix.svg) を実行する際の問題を修正しました `setup:upgrade` デプロイ中にエラーが発生したときにデプロイメントプロセスを中断しなかったコマンド `app:config:import` タスク。<!-- MAGECLOUD-3806 -->

- ![新規アイコン](../../assets/new.svg) ファイルハンドラーのデフォルトログレベルをに変更しました `debug` に表示されるログの詳細度を下げるには [!DNL Cloud Console]を選択します（デバッグ用の詳細情報は提供されます）。<!-- MAGECLOUD-3871 -->

- ![修正アイコン](../../assets/fix.svg) ビルド中に静的コンテンツのデプロイメントでエラーが発生する問題を修正しました。 インストール後、および `ece-tools` 設定ダンプ。で管理者ユーザー用のロケールが指定されていない場合にエラーが発生しました `config.php` ファイル。 これで、管理者ユーザーのデフォルトのロケールが `config.php` ファイル。<!-- MAGECLOUD-3957 -->

- ![修正アイコン](../../assets/fix.svg) を修正 `Undefined index error` 次の場合に発生 `magento-cloud` セキュア URL （https）が設定されていない環境で CLI コマンドが失敗する。 現在、ECE-Tools パッケージは、セキュア URL が使用できない場合、ベース URL （http）を使用します。<!-- MAGECLOUD-4009 -->

## v2002.0.20

- ![新規アイコン](../../assets/new.svg) **Docker の更新**—

   - ![新規アイコン](../../assets/new.svg) を使用して機能テストを実行できるようになりました `ece-tools` docker 環境のパッケージ。 参照： [アプリケーションテスト](https://devdocs.magento.com/cloud/docker/docker-test-magecloud-pkg-code.html).<!-- MAGECLOUD-3129/3684 -->

   - ![新規アイコン](../../assets/new.svg) を使用した PHP モジュールの設定をサポートするようになりました。 `.magento.app.yaml` ファイル。 任意 [で指定された PHP 拡張モジュール `.magento.app.yaml` ファイル](https://devdocs.magento.com/cloud/project/magento-app-php-application.html#php-extensions) docker PHP コンテナで使用できるようになります。<!-- MAGECLOUD-3357 -->

   - ![新規アイコン](../../assets/new.svg) Docker コマンドラインのエクスペリエンスを向上させるために使用できる新しいコマンドがあります。 を参照してください。 [`bin/magento-docker` Docker リファレンスの節](https://devdocs.magento.com/cloud/docker/docker-quick-reference.html#magento-cloud-docker-cli).<!-- MAGECLOUD-3569 -->

   - ![新規アイコン](../../assets/new.svg) Mutagen.io を使用して、ローカルホストと Docker 間の開発中にファイルを同期する機能を追加しました。<!-- MAGECLOUD-3559 -->

   - ![修正アイコン](../../assets/fix.svg) Docker 環境を使用する際のデフォルトパスを修正しました。 SSH を使用して Docker コンテナにログインすると、内のプロジェクトルートに移動します。 `/app` ディレクトリ（期待どおり）。<!-- MAGECLOUD-3582 -->

   - ![修正アイコン](../../assets/fix.svg) Sodium ライブラリをバージョン 1.0.11 からバージョン 1.0.18 に更新し、Sodium PHP 拡張モジュールを更新しました。<!-- MAGECLOUD-3832 -->

     >[!WARNING]
     >
     >クラウドインフラストラクチャー上のAdobe Commerceのお客様には、次が必要です [Adobe Commerce サポートチケットを送信](https://support.magento.com/hc/en-us/articles/360000913794#submit-ticket) Adobe Commerce 2.3.2 にアップグレードする前に、Pro 実稼動環境とステージング環境で libsodium パッケージをアップグレードする場合。現在、スターター環境をAdobe Commerce 2.3.2 にアップグレードすることはできません。

   - ![修正アイコン](../../assets/fix.svg) さんがを追加しました `analysis-icu` および `analysis-phonetic` すべての Docker イメージへのプラグインのElasticsearch。<!-- MAGECLOUD-3446 -->

   - ![修正アイコン](../../assets/fix.svg) 検証機能の向上：のオプションを使用する場合 `docker:build` コマンド。オプションを使用する場合は、値を指定する必要があります。 また、を使用する場合のノード バージョンの検証を追加しました。 `docker:build run` コマンド。<!-- MAGECLOUD-3486 & MAGECLOUD-3678 -->

- ![新規アイコン](../../assets/new.svg) **環境変数の更新**—

   - ![新規アイコン](../../assets/new.svg) を使用したデータベーステーブル接頭辞のサポートを追加しました [DATABASE_CONFIGURATION 環境変数](../environment/variables-deploy.md#database_configuration).<!-- MAGECLOUD-2901 -->

   - ![新規アイコン](../../assets/new.svg) さんがを追加しました **FORCE_UPDATE_URLS** 変数をデプロイして、Pro およびスターターの実稼動環境とステージング環境にデプロイする際にベース URL を更新します。 の定義を参照してください。 [変数のデプロイ](../environment/variables-deploy.md#force_update_urls) コンテンツ。<!-- MAGECLOUD-3602 -->

   - ![新規アイコン](../../assets/new.svg) さんがを追加しました **TTFB_TESTED_PAGES** 設定するデプロイ後変数 _最初のバイトまでの時間_ ページテスト：クラウドインフラストラクチャにデプロイされたサイトのアプリケーションパフォーマンスを確認します。 の変数の説明を参照してください。 [デプロイ後変数](../environment/variables-post-deploy.md).<!-- MAGECLOUD-3643 -->

   - ![修正アイコン](../../assets/fix.svg) マルチスレッド SCD で、静的コンテンツのデプロイメントでランダムなエラーが発生する問題を修正しました。 この回避策では、 **SCD_THREADS** 変数から `1`. 必要に応じて、カウントを増やすことができるようになりました。 の定義を参照してください。 [変数のデプロイ](../environment/variables-deploy.md#scd_threads) および [ビルド変数](../environment/variables-build.md#scd_threads).<!-- MAGECLOUD-3611 -->

   - ![修正アイコン](../../assets/fix.svg) を設定できます **WARM_UP_PAGES** 単一ページ、複数ドメイン、複数ページをキャッシュする環境変数。 で展開された定義を参照してください。 [デプロイ後変数](../environment/variables-post-deploy.md#warm_up_pages) コンテンツ。<!-- MAGECLOUD-3258 -->

- ![修正アイコン](../../assets/fix.svg) さんがを追加しました `pub/static/.htaccess` ファイルを除外リストに追加します。 [修正は PHOENIX MEDIA GmbH の Björn Kraus 氏によって提出されました](https://github.com/magento/ece-tools/pull/455).<!-- MAGECLOUD-3545/Github#455 -->

- ![修正アイコン](../../assets/fix.svg) すべての検証メッセージがとして表示されていた場合のエラーを修正しました `Critical` 少なくとも 1 つの重要レベルバリデーターがエラーを返した場合。<!-- MAGECLOUD-3178 -->

- ![修正アイコン](../../assets/fix.svg) ベース URL がデータベースに存在しない場合に、デプロイメントが失敗する問題を修正しました。<!-- MAGECLOUD-3075 -->

- ![新規アイコン](../../assets/new.svg) 新しいを追加しました **`env:config:show`コマンド** に `ece-tools` 環境サービス、ルート、変数を表示するパッケージ。 参照： [サービス、ルート、変数](https://devdocs.magento.com/cloud/reference/ece-tools-reference.html#services-routes-and-variables). [Vladimir Kerkhoff が提供する機能](https://github.com/magento/ece-tools/pull/486).<!-- MAGECLOUD-3451 -->

- ![修正アイコン](../../assets/fix.svg) Adobe Commerce 2.2.6 以前をでインストールしようとすると重大なエラーが発生する問題を修正しました `ece-tools` シェルリファクタリングの後に開発されます。<!-- MAGECLOUD-3665 -->

- ![修正アイコン](../../assets/fix.svg) Adobe Commerce 2.1.x および 2.2.x のインストールが失敗し、非推奨（廃止予定）の Carbon バージョンの使用に関する警告が表示される問題を修正しました。<!-- MAGECLOUD-3704 -->

- ![修正アイコン](../../assets/fix.svg) 減じた `cloud.log` からのシェル出力のログレベル `info` 対象： `debug`.<!-- MAGECLOUD-3277 -->

- ![修正アイコン](../../assets/fix.svg) さんがを追加しました `--remove-definers (-d)` のオプション `ece-tools db-dump` ダンプ ファイルから定義を削除するコマンド。<!-- MAGECLOUD-3510 -->

## v2002.0.19

- ![修正アイコン](../../assets/fix.svg) を上書きする問題を修正しました `env.php` デプロイ中にファイルを保存すると、カスタム設定が失われる。 このアップデートにより、クラウドインフラストラクチャ上のAdobe Commerceで `env.php` カスタム設定を維持しながら、デプロイメントごとにファイルを取り込みます。<!-- MAGECLOUD-3668 -->

## v2002.0.18

- ![新規アイコン](../../assets/new.svg) **Docker の更新**—

   - ![新規アイコン](../../assets/new.svg) これで、Docker 環境は、 [.magento.app.yaml ファイルの cron プロパティ](https://devdocs.magento.com/cloud/project/magento-app-properties.html#crons).<!-- MAGECLOUD-3150 -->

   - ![新規アイコン](../../assets/new.svg) **新規 Docker コンテナ** – が追加されました [TLS 終了プロキシコンテナ](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#varnish-container) https 経由の Varnish SSL 終了を容易にする。<!-- MAGECLOUD-2890 -->

   - ![新規アイコン](../../assets/new.svg) **新しい Docker イメージ**- Gulp および Jasmine JS 単体テストなどのその他の機能をサポートするために Node.js 画像を追加しました。<!-- MAGECLOUD-3345 -->

   - ![新規アイコン](../../assets/new.svg) **Docker ビルドモード** – で Docker 環境を起動するように選択できるようになりました。 [実稼動モードまたは開発者モード](https://devdocs.magento.com/cloud/docker/docker-launch.html#set-the-launch-mode). 開発者モードは、完全な書き込み可能なファイルシステム権限を持つアクティブ開発をサポートします。<!-- MAGECLOUD-3152/3511 -->

   - ![修正アイコン](../../assets/fix.svg) Docker のデプロイに失敗する問題を `Name or service not known` キャッシュが使用できないサービス用に設定されている場合のエラー。 これで、からサービスを削除できます [`.magento/services.yaml` ファイル](https://devdocs.magento.com/cloud/project/services.html). Docker 設定ジェネレーターがのサービスを更新します `docker/config.php.dist` ファイルを自動的に。<!-- MAGECLOUD-3369 -->

   - ![新規アイコン](../../assets/new.svg) サービスの互換性のインタラクティブな検証を追加しました。 ここで、リクエストされたサービスがAdobe Commerceのバージョンや他のサービスと互換性がない場合、 _対話モード_ では、メッセージと続行する選択をユーザーに促します。 を参照してください。 [サービスのバージョン](https://devdocs.magento.com/cloud/docker/docker-containers.html#service-containers) docker で使用可能です。 の使用 `-n` CICD の目的でインタラクティビティをスキップするオプション。<!-- MAGECLOUD-3251 -->

   - ![修正アイコン](../../assets/fix.svg) Docker Compose の問題を修正しました `db-dump` 既存のダンプを消去したコマンド。<!-- MAGECLOUD-3366 -->

   - ![修正アイコン](../../assets/fix.svg) Redis が割り当てられた問題を修正しました `session`, `default`、および `page_cache` ストレージを同じデータベース ID にキャッシュします。<!-- MAGECLOUD-3172 -->

- ![新規アイコン](../../assets/new.svg) **環境変数の更新**—

   - ![新規アイコン](../../assets/new.svg) 新しい **ELASTICSUITE\_CONFIGURATION** 環境変数は、デプロイメントとデプロイメントの間、カスタマイズされたサービス設定を保持します。 の定義を参照してください。 [変数のデプロイ](../environment/variables-deploy.md#elasticsuite_configuration) コンテンツ。<!-- MAGECLOUD-3205 -->

   - ![新規アイコン](../../assets/new.svg) さんがを追加しました **SCD_MAX_EXECUTION_TIMEOUT** 環境変数を使用すると、 `.magento.env.yaml` ファイル。 の定義を参照してください。 [変数のデプロイ](../environment/variables-deploy.md#scd_max_execution_time), [ビルド変数](../environment/variables-build.md#scd_max_execution_time)、および [グローバル変数](../environment/variables-global.md#scd_max_execution_time).<!-- MAGECLOUD-2822 -->

      - ![新規アイコン](../../assets/new.svg) さんがを追加しました **MAGENTO_CLOUD_LOCKS_DIR** 環境変数：クラウドインフラストラクチャ上のロックプロバイダーのマウントポイントへのパスを設定します。 ロックプロバイダーは、重複した cron ジョブや cron グループの起動を防ぎます。 この変数はAdobe Commerce バージョン 2.2.5 以降でサポートされ、自動的に設定されます。 の定義を参照してください [クラウド変数](../environment/variables-cloud.md).<!-- MAGECLOUD-3135 -->

      - ![修正アイコン](../../assets/fix.svg) がを変更しました **SCD_THREADS** 環境変数のデフォルト値：検出された CPU スレッド数に基づいて最適な値を自動的に決定します。 で更新された定義を参照してください。 [変数のデプロイ](../environment/variables-deploy.md#scd_threads) および [ビルド変数](../environment/variables-build.md#scd_threads).<!-- MAGECLOUD-3382 -->

- ![修正アイコン](../../assets/fix.svg) DB 分離メカニズムのパッチが、クラウドインフラストラクチャバージョン 2002.0.16 上のAdobe Commerceにアップグレードする際にエラーが発生する問題を修正しました。<!-- MAGECLOUD-3383 -->

- ![修正アイコン](../../assets/fix.svg) に代わるパッチを追加しました。 _Google画像グラフ_ （を使用） _Image-Chart_. DevBlog の記事を参照 [Google画像グラフの M1 の廃止と更新](https://community.magento.com/t5/Magento-DevBlog/Google-Image-Charts-deprecation-and-update-for-M1/ba-p/125006).<!-- MAGECLOUD-3456 -->

- ![修正アイコン](../../assets/fix.svg) の検証を追加しました [SEARCH_CONFIGURATION 変数](../environment/variables-deploy.md#search_configuration). 「エンジン」オプションが設定されていない場合、デプロイは失敗し、 `_merge` は必須ではありません。<!-- MAGECLOUD-3470 -->

- ![修正アイコン](../../assets/fix.svg) 例外が発生した後に機密データが公開される問題を修正しました。 これで、機密情報が適切にマスクされます。<!-- MAGECLOUD-3525 -->

- ![修正アイコン](../../assets/fix.svg) Magento Open Sourceパッケージのフォールトトレラント設定を改善しました。 Adobe Commerceが Redis からデータを読み取れない場合 `slave` 例えば、Redis から読み取りが行われます `master` インスタンス。 参照： [REDIS_USE_SLAVE_CONNECTION](../environment/variables-deploy.md#redis_use_slave_connection).<!-- MAGECLOUD-2899 -->

## v2002.0.17

>[!NOTE]
>
>この `ece-tools` バージョン 2002.0.17 には、重要なセキュリティパッチが含まれています。 参照： [テクニカルリソース：Magento Open Sourceパッチ](https://magento.com/tech-resources/download#download2288).

- ![新規アイコン](../../assets/new.svg) **サービスの更新**—Adobe Commerce バージョン 2.2.8 以降、2.2.x、2.3.1 以降、2.3.x でサポート

   - Elasticsearchバージョン 6.x がサポートされるようになりました。<!-- MAGECLOUD-3196 -->

   - Redis バージョン 5.0 のサポートを追加しました。

- ![新規アイコン](../../assets/new.svg) **新しい Docker イメージ** – 次のサービスを Docker ビルドに追加しました。

   - Elasticsearch 6.5<!-- MAGECLOUD-3196 -->

   - Redis 5.0<!-- MAGECLOUD-3223 -->

- ![新規アイコン](../../assets/new.svg) **新しい環境変数** – 以前は、SCD 圧縮のタイムアウトがハードコードされていました。 次に、を使用して SCD 圧縮タイムアウトを設定できます **SCD_COMPRESSION_TIMEOUT** 環境変数。 の定義を参照してください。 [ビルド変数](../environment/variables-build.md#scd_compression_timeout) および [変数のデプロイ](../environment/variables-deploy.md#scd_compression_timeout) コンテンツ。<!-- MAGECLOUD-2870 -->

- ![修正アイコン](../../assets/fix.svg) さんがを追加しました `--use-rewrites` セキュリティとカスタマーエクスペリエンスを向上させるために、ストアフロントおよび管理者アクセスで生成されたリンクの web サーバー書き換えを使用するように、インストールコマンドのオプションを設定します。<!-- MAGECLOUD-3246 -->

- ![修正アイコン](../../assets/fix.svg) にタイムスタンプを追加しました `var/log/install_upgrade.log` インストールおよびアップグレードイベントの日付を表示するファイル。<!-- MAGECLOUD-2895 -->

## v2002.0.16

- ![新規アイコン](../../assets/new.svg) **Docker の更新**—

   - Docker 環境で生成されたデフォルトのサービス設定は、クラウドテンプレートのデフォルト設定と同じになりました。<!-- MAGECLOUD-3025 -->

   - Docker 環境からメールを送信するには、 `sendmail` サービス。<!-- MAGECLOUD-2907 -->

   - にを追加しました。 [xdebug の設定](https://devdocs.magento.com/cloud/docker/docker-development-debug.html) を Cloud Docker 環境でデバッグします。<!-- MAGECLOUD-2891 -->

   - を生成する際の web サービス権限の問題を修正しました `docker-compose.yml` ファイル。<!-- MAGECLOUD-2883 -->

- ![新規アイコン](../../assets/new.svg) **アップグレードの改善** – を確認する検証を追加しました `autoload` のプロパティ `composer.json` Adobe Commerce v2.3 にアップグレードする前に、必要な設定の変更がファイルに含まれています。参照： [アップグレードバージョン](https://devdocs.magento.com/cloud/project/project-upgrade.html).<!-- MAGECLOUD-2392 -->

- ![新規アイコン](../../assets/new.svg) 静的なコンテンツをデプロイする際の圧縮プロセスには、ネイティブで生成またはカスタマイズされたすべてのアセットが含まれるようになり、 [`build:transfer` セクション](https://devdocs.magento.com/cloud/project/magento-app-properties.html#hooks). 以前は、圧縮プロセスは、静的アセットのカスタム圧縮とバンドルを適用する前に行われていました。 [Tryzens Limited から Rafael Garcia Lepper によって送信された修正](https://github.com/magento/ece-tools/pull/413).<!-- MAGECLOUD-3104 -->

- ![修正アイコン](../../assets/fix.svg) 追加のデータベースとサービスの関係を設定した直後に、デプロイメント中に発生したデータベース接続エラーを修正しました。 また、この修正は、Starter のCommerce レポートの設定プロセス中に発生した問題に対処します。 スターターの場合、このアップグレードは、Commerce レポートを使用する際に「必須」です。<!-- MAGECLOUD-3035 -->

- ![修正アイコン](../../assets/fix.svg) データベース設定の検証の問題で、デプロイプロセスが失敗する原因を修正しました。<!-- MAGECLOUD-3003 -->

- ![修正アイコン](../../assets/fix.svg) 制約を適切なバージョンに更新しました `symfony/yaml` で使用するパッケージ [PHP 定数](https://devdocs.magento.com/cloud/project/magento-env-yaml.html#php-constants). を使用している場合、定数解析が機能しない。 `symfony/yaml` 3.2 より前のパッケージバージョン。 [Vladimir Kerkhoff による修正の送信](https://github.com/magento/ece-tools/pull/404).<!-- MAGECLOUD-2956 -->

- ![新規アイコン](../../assets/new.svg) **環境設定の確認**—PHP のバージョンをチェックし、最新の推奨バージョンを使用していない場合に警告する検証を追加しました。<!--MAGECLOUD-2903-->

- ![修正アイコン](../../assets/fix.svg) 不正な形式の JSON 変数を処理する問題を修正しました。 これで、JSON 変数が構文エラーを引き起こした場合、 `cloud.log` ファイルとデプロイメントは、デフォルトの変数を使用して続行されます。<!-- MAGECLOUD-2851 -->

- ![修正アイコン](../../assets/fix.svg) Redis サービスを無効にした直後にデプロイ中に発生していた接続エラーを修正しました。<!-- MAGECLOUD-2747 -->

- ![新規アイコン](../../assets/new.svg) **変更の記録** – さんが、 [ログレベル](../environment/log-handlers.md#log-levels) から `Info` 対象： `Notice` 次のビルドおよびデプロイプロセスイベントの場合：<!--MAGECLOUD-2925-->

   - にインストールされたモジュールの紐付けプロセスの開始と終了 `composer.json` で共有設定を使用します。 `app/etc/config.php` ファイル

   - 設定検証プロセスの開始と終了

   - の開始と終了 `setup:di:compile` クラスを生成するプロセス

- ![新規アイコン](../../assets/new.svg) **新しい環境変数**—

   - **[RESOURCE_CONFIGURATION デプロイ変数](../environment/variables-deploy.md#resource_configuration)** – この変数を使用して、リソース名をデータベース接続にマップします。<!-- MAGECLOUD-3026 & MAGECLOUD-2963-->

   - **[X_FRAME_CONFIGURATION グローバル変数](../environment/variables-global.md#x_frame_configuration)** – この変数を使用して、 `X-Frame-Options` でAdobe Commerce ページをレンダリングするためのヘッダー設定 `<frame>`, `<iframe>`、または `<object>`.<!-- MAGECLOUD-3048 -->

- ![修正アイコン](../../assets/fix.svg) **環境変数の更新** – 次の環境変数を変更しました。

   - **[WARM_UP_PAGES](../environment/variables-post-deploy.md)**- Adobe Commerce ストア用に定義されたすべてのドメインで、指定されたページのキャッシュをプリロードできるようになりました。 以前は、サイトが複数のドメインで設定されている場合、デプロイ後のプロセスが、デフォルト以外のドメイン上の指定されたページのキャッシュをプリロードできず、デプロイ後のログで次のエラーが返されていました。 `ERROR: Warming up failed: <uri>`<!-- MAGECLOUD-2466 -->

   - **SCD_COMPRESSION_LEVEL**- ドキュメントとサンプルを更新しました `.magento.env.yaml` scd 圧縮レベルの適切なデフォルト値を持つファイル。 の定義を参照してください。 [ビルド変数](../environment/variables-build.md#scd_compression_level) および [変数のデプロイ](../environment/variables-deploy.md#scd_compression_level) コンテンツ。<!-- MAGECLOUD-2823 -->

   - **SCD_EXCLUDE_THEMES** – この環境変数は非推奨です。 の使用 [SCD_MATRIX](../environment/variables-build.md#scd_matrix) テーマの設定を制御します。<!--MAGECLOUD-2882-->

   - **SCD\_MATRIX**- SCD_MATRIX が異なる文字ケースを含むテーマ値を無視した場合に発生する問題を防ぐために、検証プロセスを修正しました。 の定義を参照してください。 [ビルド変数](../environment/variables-build.md#scd_matrix) および [変数のデプロイ](../environment/variables-deploy.md#scd_matrix) コンテンツ。<!-- MAGECLOUD-2904 -->

   - **管理変数**—<!-- MAGECLOUD-2573/MAGECLOUD-2848 -->

      - 環境変数を使用して管理者ユーザーの資格情報を管理する際のセキュリティが向上しました。 ADMIN_EMAIL、ADMIN_USERNAME および ADMIN_PASSWORD 環境変数を、アップグレード中に管理者の資格情報を上書きするために使用できなくなりました。 管理パネルにアクセスできない場合は、 _パスワードを忘れる_ 機能または `admin:user:create` 新しい管理者ユーザーを作成する CLI コマンド。 参照： [管理パネルへのアクセス](https://devdocs.magento.com/cloud/onboarding/onboarding-tasks.html#admin).

      - パッチのアップグレードまたは適用時に、ADMIN_EMAIL は不要になりました。

## v2002.0.15

- ![新規アイコン](../../assets/new.svg) **Docker の更新**—

   - これで、Docker Generator はで指定されたサービスを使用するようになりました `.magento.app.yaml` および `.magento/services.yaml` 設定ファイルの条件 [docker 環境の構築](https://devdocs.magento.com/cloud/docker/docker-config.html). ビルドパラメーターを使用して、別のサービスバージョンを選択できます。<!-- MAGECLOUD-2888 -->

   - PHP 7.2 のイメージを追加しました – Cloud Docker で PHP 7.2 のサポートを追加しました。 [Docker 設定の起動](https://devdocs.magento.com/cloud/docker/docker-config.html) を含めるには `docker:build --php` 使用しているAdobe Commerceと互換性のある PHP のバージョンを指定します。<!-- MAGECLOUD-2799 -->

   - がを追加しました [Cron コンテナ](https://devdocs.magento.com/cloud/docker/docker-containers-cli.html#cron-container) php-CLI イメージに基づいています。<!-- MAGECLOUD-2565 -->

   - Docker ビルドに次のサービスを追加しました。

      - [!DNL RabbitMQ] 3.5 および 3.7<!-- MAGECLOUD-2567 & 2889-->

      - Elasticsearch 1.7、2.4、5.2<!-- MAGECLOUD-2569 & 2887 -->

      - Redis 3.2 と 4.0<!-- MAGECLOUD-2886 -->

- ![新規アイコン](../../assets/new.svg) **PHP 定数を使用してを設定する** – のサポートを追加 [PHP 定数](https://devdocs.magento.com/cloud/project/magento-env-yaml.html#php-constants) が含まれる `.magento.env.yaml` 設定ファイル。<!-- MAGECLOUD- 2575 -->

- ![新規アイコン](../../assets/new.svg) **新しい環境変数** – デフォルトでは、実稼動環境でのみGoogle Analyticsが有効になっています。 を使用して、ステージング環境と統合環境でGoogle Analyticsを有効にできます  [ENABLE_GOOGLE_ANALYTICS 環境変数](../environment/variables-deploy.md#enable_google_analytics).<!--MAGECLOUD-2879-->

- ![修正アイコン](../../assets/fix.svg) カスタマイズした cron 設定をから削除する問題を修正しました `env.php` 再デプロイメント後のファイル。 現在は、カスタム cron 設定は安全に `env.php` ファイル。<!-- MAGECLOUD-2923 -->

- ![修正アイコン](../../assets/fix.svg) メッセージの不整合を修正し、 [ログレベル](../environment/log-handlers.md#log-levels) ビルド、デプロイ、デプロイ後のフェーズ用。 開始および終了ログメッセージレベルの向上： **情報** 対象： **通知** すべてのフェーズとサブフェーズで使用します。 必要に応じて、開始および終了ログメッセージを追加しました。<!-- MAGECLOUD-2919 -->

- ![修正アイコン](../../assets/fix.svg) 設定の際に、デプロイ後フェーズを開始できない cron プロセスに関する問題を修正しました。 これで、デプロイ後のフックを有効にした場合、デプロイ後のフェーズの開始時に、cron プロセスが再び有効になります。<!-- MAGECLOUD-2862 -->

- ![修正アイコン](../../assets/fix.svg) カスタムデータベース設定を指定した場合に、Adobe Commerceを正常にインストールできない問題を修正しました。 以前は、インストールプロセスは、 [MAGENTO_CLOUD_RELATIONSHIP 変数](../environment/variables-cloud.md) でカスタマイズされた接続情報を指定した場合でも、 [DATABASE_CONFIGURATION 環境変数](../environment/variables-deploy.md#database_configuration).<!--MAGECLOUD-2736-->

- ![修正アイコン](../../assets/fix.svg) を修正しました `config:dump` コマンドを使用して、 `system` の節 `config.php` ファイル。<!--MAGECLOUD-2740-->

- ![修正アイコン](../../assets/fix.svg) に起因する問題を修正しました _ウォームアップ_ ソースベース URL の参照を修正することで、デプロイ後のフェーズでエラーが発生しました。<!--MAGECLOUD-2797-->

- ![修正アイコン](../../assets/fix.svg) の間にファイルが正しく生成されなかった問題を修正しました `setup:di:compile` Amazon Pay モジュールに影響を与えたプロセス。<!--MAGECLOUD-2850-->

## v2002.0.14

- ![新規アイコン](../../assets/new.svg) **理想的な状態の確認** – この `ideal-state` ウィザードでは、各デプロイメントで現在の構成を検証し、構成を更新してダウンタイムのない迅速なデプロイメントを実現するための明確な手順を提供するようになりました。<!--MAGECLOUD-2372-->

- ![修正アイコン](../../assets/fix.svg) **PCI コンプライアンス**- サードパーティのメッセージングサービスと接続する際に、クラウドインフラストラクチャ上のAdobe Commerceのメッセージングプロトコルを更新して、Transport Layer Security （TLS）バージョン 1.2 を必要とするようにしました。 TLS バージョン 1.2 をサポートしていないメッセージサービスを使用している場合は、サービスをアップグレードする必要があります。 そうでない場合は、Adobe Commerce アプリケーションがメッセージサーバーに接続してメールを送信しようとすると、次のエラーメッセージが表示されます。 `Unable to connect via TLS`.<!--MAGECLOUD-2521-->

- ![新規アイコン](../../assets/new.svg) **デプロイメントの改善** – ステージング環境または実稼動環境に次の問題がある場合に顧客に警告する検証を追加しました `dev`, `debug`、または `debug_logging` オプションを有効にして、過度のログアクティビティによって発生するパフォーマンスの問題を防ぎます。<!--MAGECLOUD-2517-->

- ![修正アイコン](../../assets/fix.svg) **デプロイメントの修正**—

   - これで、メンテナンスモードがデプロイフェーズの開始時に有効になり、最後に無効になります。 配置に失敗した場合、配置の問題が解決されるまで、サイトはメンテナンス モードのままになります。 以前は、デプロイメントに失敗した場合でも、サイトは実稼動モードに戻っていました。<!--MAGECLOUD-2603-->

      - デプロイフェーズの検証チェックを修正し、次のデプロイメントの問題のエラーレベルを `CRITICAL` 対象： `WARNING` デプロイメントが完了するように設定します。 以前は、これらの問題が原因でデプロイメントが失敗していました。

      - 環境設定に、デプロイまたはクラウド変数の誤った値が含まれています。

   - クラウドインフラストラクチャー上のElasticsearchのバージョンは、クラウドインフラストラクチャー上のAdobe Commerceでサポートされている elasticsearch/elasticsearch モジュールのバージョンと互換性がありません。 を参照してください。 [Elasticsearchのトラブルシューティング記事](https://support.magento.com/hc/en-us/articles/360015758471-Deployment-fails-or-interrupts-with-cloud-log-error-Elasticsearch-version-is-not-compatible-with-current-version-of-magento) （Adobe Commerce サポートのナレッジベース）<!--MAGECLOUD-2600-->

   - の共有設定の問題を修正しました `app/etc/config.php` 原因となったファイル `recursion detected` デプロイ中にエラーが発生しました。<!--MAGECLOUD-2173-->

- ![修正アイコン](../../assets/fix.svg) **Cron 関連の修正**—

   - デフォルト（1 分）以外の cron 頻度を指定した場合にジョブが実行されない cron スケジュールの問題を修正しました。<!--MAGECLOUD-2602-->

   - デプロイフェーズで、デプロイメント中に cron ジョブを引き続き実行できた問題を修正しました。この問題により、データベースのロックやその他の重要な問題が発生する可能性があります。 これで、デプロイフェーズが開始される前にすべての cron ジョブが停止し、デプロイメントの完了後に再開されます。&lt;!—MAGECLOUD—2537—>

   - バージョン 2.2.x の cron ジョブワークフローを修正し、フリーズされた cron ジョブのロックを解除して、デプロイメント開始前に停止できるようにしました。 以前は、フリーズした cron ジョブが原因でデプロイメントが停止していました。<!--MAGECLOUD-2501-->

- ![修正アイコン](../../assets/fix.svg) の形式を変更しました `config.php` によって生成されたファイル `vendor/bin/ece-tools config:dump` Adobe Commerceのコーディング標準に準拠するために、短い配列構文と 4 文字のインデントを使用するコマンド。<!--MAGECLOUD-2527-->

- ![修正アイコン](../../assets/fix.svg) の場合に発生するデプロイメントエラーを修正しました `.magento.env.yaml` 次を含む `{{ base_url }}` および `{{ unsecure_base_url }}` クラウドインフラストラクチャプロジェクト上のAdobe Commerceのデフォルト URL 設定の代わりに使用される、web 設定のプレースホルダー。/<!--MAGECLOUD-2607-->

## v2002.0.13

- ![新規アイコン](../../assets/new.svg) **ダウンタイムなしのデプロイメントを有効にする** – 現在は、クラウドインフラストラクチャー上のAdobe Commerceがデプロイメント時に必要なデータベース変更を含むリクエストをキューに入れ、デプロイメントが完了するとすぐに変更を適用します。 セッションが失われないように、リクエストを最大 5 分間保持できます。 参照： [クラウドでのデプロイメントのダウンタイムを短縮する静的コンテンツデプロイメントオプション](https://support.magento.com/hc/en-us/articles/360004861194-Static-content-deployment-options-to-reduce-deployment-downtime-on-Cloud).<!--MAGECLOUD-2169-->

- ![新規アイコン](../../assets/new.svg) **Docker Compose for Cloud** – に対して次の改善を行いました。 [Docker のセットアップと設定](https://devdocs.magento.com/cloud/docker/docker-config.html) プロセス：

   - コマンドを追加しました。`docker:config:convert` php 設定ファイルを Docker ENV 形式に変換して、環境設定を簡素化する。 次に、PHP 設定ファイルを Docker ディレクトリにコピーして、Docker ENV ファイルに変換します。 参照： [Docker の起動](https://devdocs.magento.com/cloud/docker/docker-config.html).<!--MAGECLOUD-2359-->

   - クラウドインフラストラクチャー上のAdobe Commerceのインストールプロセスで、読み取り専用ファイルシステムと読み取り/書き込み可能ファイルシステムの両方へのデプロイがサポートされるようになりました。これにより、クラウドファイルシステムをより詳細にエミュレートできます。 参照： [Docker の設定](https://devdocs.magento.com/cloud/docker/docker-config.html).&lt;!—MAGECLOUD—2357—>

   - **Redis サービスサポート**- Redis イメージが追加されました。これは Docker コンテナにデプロイされ、Docker インストールと連携するように自動的に設定されます。&lt;!—MAGECLOUD—2442—>

   - これで、Cloud Docker を使用する際の DB ダンプ機能が利用できるようになりました [データベースコンテナ](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#database-container). また、次のことができます [ファイルを共有](https://devdocs.magento.com/cloud/docker/docker-containers.html#sharing-data-between-host-machine-and-container) を使用してホストマシンとコンテナの間で `docker/mnt` ディレクトリ。<!-- MAGECLOUD-2577 -->

   - **Varnish サービスのサポート**— Docker コンテナに自動的にデプロイされる Varnish イメージを追加しました。 デプロイメント後、Adobe Commerceのベストプラクティスに従って、Varnish を手動で設定できます。 参照： [ワニスの設定と使用](https://devdocs.magento.com/guides/v2.3/config-guide/varnish/config-varnish.html).&lt;!—MAGECLOUD—2358—>

   - 安全なサイトアクセス - Adobe Commerce ストアと管理パネルにアクセスするための SSL サポートが追加されました。&lt;!—MAGECLOUD—2360—>

- ![修正アイコン](../../assets/fix.svg) **クラウドインフラストラクチャー上のAdobe Commerce拡張機能のサポートを改善しました** – クラウドインフラストラクチャー上のAdobe Commerceの guzzlehttp/guzzle パッケージの最小バージョン要件をダウングレードしました [composer.json ファイル](https://devdocs.magento.com/cloud/reference/cloud-composer.html) をバージョン 6.2 にアップグレードして、 `ece-tools` パッケージはより多くの拡張機能と互換性があります。<!--MAGECLOUD-2205-->

- ![新規アイコン](../../assets/new.svg) **ビルドフェーズでのAdobe Commerce アプリケーションへのカスタム変更の適用** – ビルドフェーズを 2 つの個別のプロセスに分割して、フックを使用して、アプリケーションをデプロイメント用にパッケージ化する前に、生成された静的コンテンツにカスタムの変更を適用できるようにします。 この _ビルド：生成_ process は、コードを生成し、パッチを適用し、静的コンテンツを生成します。 この _ビルド：転送_ プロセスは、生成されたコードと静的コンテンツを最終的な宛先に転送します。 参照： [アプリケーションフック](https://devdocs.magento.com/cloud/project/magento-app-properties.html#hooks).<!--MAGECLOUD-2363-->

- ![修正アイコン](../../assets/fix.svg) **環境設定チェック**- Adobe Commerceをクラウドインフラストラクチャにビルドおよびデプロイする前に、バージョンの非互換性と設定エラーに関する警告を顧客に表示するために、環境設定の検証を改善しました。

   - サポートされていない、または非推奨（廃止予定）の環境変数と値を識別するための、バージョン固有の検証を追加しました。<!--MAGECLOUD-2183-->

   - Elasticsearch設定の問題に関してユーザーに警告するためのElasticsearch互換性チェックが追加されました。 現在は、サーバー上のElasticsearchサービスのバージョンがAdobe Commerceと互換性がない場合、デプロイは失敗します。 以前は、Elasticsearchバージョンに互換性がない場合でもデプロイメントが成功し、サイトデプロイメント後に製品カタログの問題が発生していました。<!--MAGECLOUD-2389-->

     非互換性は、次の方法で解決できます。 [サポートチケットの送信](https://devdocs.magento.com/cloud/trouble/trouble.html) Elasticsearchを互換性のあるバージョンにアップグレードするか、Adobe Commerceの設定を変更してElasticsearch PHP クライアントの互換性のあるバージョンを指定します。

      - Adobe Commerce バージョン 2.1.x から 2.2.2 の場合は、Elasticsearchをバージョン 2.4 にアップグレードします。

      - Adobe Commerce バージョン 2.2.3 以降では、Elasticsearchをバージョン 5.2 にアップグレードします。

      - Elasticsearch 1.x または 2.x があり、アップグレードしない場合は、composer.json でAdobe Commerce Elasticsearchの PHP クライアントのバージョン要件を次のように更新します。 `"elasticsearch/elasticsearch": "~2.0"`.

   - ビルド、デプロイ、デプロイ後のフェーズで競合を引き起こす可能性のある設定を特定するために、環境変数の検証を改善しました。 例えば、静的コンテンツのデプロイメントのグローバル設定がビルドまたはデプロイフェーズの設定と競合する場合、インストールおよびアップグレードプロセス中に警告メッセージが表示されます。<!--MAGECLOUD-2156-->

- ![修正アイコン](../../assets/fix.svg) **環境変数の更新** – 次の環境変数を変更しました。

   - **[SKIP_VARIATION_MINIFICATION グローバルHTML](../environment/variables-global.md#skip_html_minification)**— デフォルト値をに変更しました。 `true` ステージング環境および実稼動環境にデプロイする際のダウンタイムを最小限に抑える、オンデマンドのHTMLコンテンツ縮小を有効にします。 この設定は、ダウンタイムなしのデプロイメントに必要です。<!--MAGECLOUD-2435-->

   - **[CLEAN_STATIC_FILES デプロイ変数](../environment/variables-deploy.md#clean_static_files)**- CLEAN_STATIC_FILES 環境変数の設定に基づいて、ビルドフェーズで生成された静的コンテンツのクリーンな静的ファイル処理を管理する機能を追加しました。 以前は、ビルドフェーズで生成された静的コンテンツファイルは、常にクリーンアップされていました。<!--MAGECLOUD-1506-->

- ![修正アイコン](../../assets/fix.svg) **ログ** – ログメッセージを改善し、ログサイズを小さくするために、次の変更を行いました。

   - 環境設定でデバッグレベルのログが指定されていない場合でも、デプロイメントの失敗ログエントリに、エラーの原因となった操作からのコマンド出力が含まれるようになりました。 参照： [`MIN_LOGGING_LEVEL`](../environment/variables-global.md#min_logging_level).<!--MAGECLOUD-2489-->

   - ファイルシステムが読み取り専用状態にあるので、一部の拡張機能で必要な生成ファクトリが正しく生成されない場合に発生するデプロイメントエラーのログを追加しました。<!--MAGECLOUD-2209-->

   - インタラクティブなプログレスバーを使用するセットアップコマンドによって発生する、デプロイログサイズの縮小とフォーマットの問題の修正。<!--MAGECLOUD-2402-->

   - 不要な冗長をなくし、一部のログステートメントの優先度レベルを更新しました。<!--MAGECLOUD-2227-->

- ![修正アイコン](../../assets/fix.svg) **Cron 固有の修正**—

   - 履歴有効期間のデフォルトの cron ジョブ設定を 3d （4320 分）から 1 時間（60 分）に変更して、cron キューの量が多すぎると発生するパフォーマンスの問題とデプロイメントエラーを防ぎました。<!--MAGECLOUD-2427-->

   - デプロイフェーズにおける cron ジョブ管理プロセスを改善し、データベースのロックやその他の重大な問題を防ぎました。 これで、すべての cron ジョブはデプロイフェーズで停止し、デプロイメントが完了した後で再起動されます。<!--MAGECLOUD-2445-->

   - Adobe Commerce バージョン 2.2.0 以降の cron ジョブで起動されたコンシューマーをスケジュールするためのロックメカニズムで、cron ジョブで重複したコンシューマーが起動されない問題を修正しました。<!--MAGECLOUD-2464-->

- ![修正アイコン](../../assets/fix.svg) の問題を修正しました [静的コンテンツ圧縮プロセス](../environment/variables-intro.md) （`gzip`を引き起こした `not overwritten` および `no such file or directory` 展開プロセス中に圧縮ファイルを参照する際にエラーが発生しました。<!-- MAGECLOUD-2182-->

- ![修正アイコン](../../assets/fix.svg) を実行できない問題を修正しました `php ./vendor/bin/ece-tools config:dump` コマンドで冗長セクションを `config.php` ストアのロケールが指定されていない場合、ダンププロセス中にファイルが生成されます。 これで、設定ファイルを環境間で簡単に移動できます。 をに更新した後 `ece-tools` v2002.0.13、古いバージョンの再生成 `config.php` を改善したファイル `config:dump` コマンド。 参照： [ストア設定の設定管理](https://devdocs.magento.com/cloud/live/sens-data-over.html).<!--MAGECLOUD-2444-->

- ![修正アイコン](../../assets/fix.svg) のルート設定の場合に、デプロイフェーズ中にエラーが発生する問題を修正しました。 `.magento/routes.yaml` ファイルはからリダイレクトされる [頂点](https://blog.cloudflare.com/zone-apex-naked-domain-root-domain-cname-supp/) ドメインからへ `www` ドメイン。<!--MAGECLOUD-2556-->

- ![修正アイコン](../../assets/fix.svg) の問題を修正しました `_merge` オプション： [`SEARCH_CONFIGURATION`](../environment/variables-deploy.md#search_configuration) を含めない場合に、誤った結合結果を引き起こす変数 `engine` 更新されたのパラメーター `.magento.env.yaml` 設定ファイル。 現在は、結合操作により、更新されたで指定した値のみが正しく上書きされます `.magento.env.yaml` を設定する必要はありません `engine` パラメーター。<!--MAGECLOUD-2520-->

- ![修正アイコン](../../assets/fix.svg) クラウドインフラストラクチャバージョン 2.2.1 以降で、Adobe Commerceのセッションロックが誤って有効になり、パフォーマンスの低下やタイムアウトの原因となる可能性がある Redis 設定の問題を修正しました。 現在は、セッションロックはデフォルトで無効になっています。 この問題は、のデフォルト動作の変更が原因で発生しました `disable_locking` redis セッションハンドラーパッケージの v1.3.4 で導入されたパラメーター。 参照： [colinmollenhour/php-redis-session-abstract パッケージ](https://github.com/colinmollenhour/php-redis-session-abstract).<!-- MAGECLOUD-2515-->

## v2002.0.12

- ![新規アイコン](../../assets/new.svg) **Docker Compose for Cloud** – コマンドを追加 – `docker:build` – 生成する [Docker Compose](https://devdocs.magento.com/cloud/docker/docker-config.html) クラウドからの設定 `ece-tools` リポジトリ。<!-- MAGECLOUD-2250 -->

- ![新規アイコン](../../assets/new.svg) **ロケールの変更** – これで、設定プロセスのエクスポートとインポートを行わずに、ストアのロケールを変更できるようになりました。 アプリケーションが実稼動環境にあり、SCD_ON_DEMAND が有効になっている間は、格納および管理ロケールオプションを使用できます。<!-- MAGECLOUD-2019 -->

- ![新規アイコン](../../assets/new.svg) <!-- MAGECLOU-1998 -->**サイトマップとロボット** – が作成されました [ワークフロー](../store/robots-sitemap.md) を追加 `robots.txt` のファイルを作成して生成 `sitemap.xml` インフラストラクチャに変更を加える必要のない、単一ドメイン設定用のファイル。

- ![新規アイコン](../../assets/new.svg) **ウィザード**— 2 つを追加しました [ウィザード](../deploy/smart-wizards.md) クラウド設定の支援：<!-- MAGECLOUD-1910 -->

   - `ideal-state` – 導入のダウンタイムを最小限に抑えるための理想的な状態を構成する

   - `master-slave` – データベースと Redis のロード・バランシングを構成します。

- ![新規アイコン](../../assets/new.svg) **モジュールの更新** – クラウドコマンドを追加 – `module:refresh` – 無効または明示的に有効になっていないモジュールを有効にします。これは、ビルド中に自動的に行われる方法と同様です。<!-- MAGECLOUD-1521 -->

- ![新規アイコン](../../assets/new.svg) を使用してサービスの設定を結合または上書きすることを選択する機能を追加しました `_merge` オプション： [キャッシュ](../environment/variables-deploy.md#cache_configuration), [SESSION](../environment/variables-deploy.md#session_configuration), [キュー](../environment/variables-deploy.md#queue_configuration)、および [検索](../environment/variables-deploy.md#search_configuration) 設定。<!-- MAGECLOUD-2105 -->

- ![新規アイコン](../../assets/new.svg) **環境設定サンプルファイル** – を追加しました。 `.magento.env.yaml` ece-Tools パッケージに追加するサンプルファイル。各環境変数の詳細な説明と指定可能な値が記載されています。<!-- MAGECLOUD-1908 -->

   - また、の詳細な検証も追加しました `.magento.env.yaml` 予期しない値によってデプロイメントプロセスでエラーが発生するのを防ぐ設定。 エラーが発生すると、次の内容で始まる詳細なエラーメッセージが表示されるようになりました。 `Environment configuration is not valid. Please correct .magento.env.yaml file with next suggestions:`<!-- MAGECLOUD-1907 -->

- ![新規アイコン](../../assets/new.svg) 以下を追加しました [**環境変数**](../environment/variables-intro.md):

   - 新しいを使用して、テーマごとに複数のロケールを定義できるようになりました [SCD_MATRIX](../environment/variables-deploy.md#scd_matrix) デプロイするテーマファイルの量を減らす環境変数。<!-- MAGECLOUD-1501 -->

   - さんがを追加しました [DATABASE_CONFIGURATION](../environment/variables-deploy.md#database_configuration) デプロイメント用にデータベース接続をカスタマイズするための環境変数。<!-- MAGECLOUD-2047 -->

   - 新しい [MIN_LOGGING_LEVEL](../environment/variables-global.md#min_logging_level) 変数は、コードを変更せずにすべての出力ストリームの最小ログレベルを上書きします。<!-- MAGECLOUD-2129 -->

- ![修正アイコン](../../assets/fix.svg) デプロイフェーズとデプロイ後フェーズの間でダウンタイムが発生する問題を修正しました。 これで、デプロイ後フェーズが開始されます _即時_ デプロイフェーズ終了後。

- ![修正アイコン](../../assets/fix.svg) で成功した cron ジョブをクリーンアップしない問題を修正しました `status = success`、スケジュールから。<!-- MAGECLOUD-2268 -->

- ![修正アイコン](../../assets/fix.svg) の問題を修正しました `post_deploy` プロジェクトのデプロイ後フェーズではなくデプロイフェーズのキャッシュをクリアしたフック。<!-- MAGECLOUD-2113 -->

- ![修正アイコン](../../assets/fix.svg) 複数のロケールで SCD を使用すると、同じ生成が行われる問題を修正しました `js-translation.json` 各ロケール用のファイル。<!-- MAGECLOUD-2034 -->

- ![修正アイコン](../../assets/fix.svg) 最適化： `db:dump` のコマンド `ece-tools` テーブルのロックを回避し、速度を上げるためにパッケージ化します。<!-- MAGECLOUD-2033 -->

## v2002.0.11

>[!NOTE]
>
>ECE-Tools バージョン 2002.0.11 は、2.2.4 の互換性のために必要です。

- ![新規アイコン](../../assets/new.svg) **マスター以外のノードへの読み取り専用接続の設定** – このリリースでは、読み取り専用トラフィックを受信するように、非マスターノードへの読み取り専用接続を設定する機能が追加されました（  [MariaDB](../environment/variables-deploy.md#mysql_use_slave_connection)）に設定します。<!--MAGECLOUD-143 -->[Redis](../environment/variables-deploy.md#redis_use_slave_connection) および <!--MAGECLOUD-143 -->

- ![新規アイコン](../../assets/new.svg) **設定ウィザード** – 静的なコンテンツのデプロイメントの設定を検証するのに役立つウィザードが追加されました。 参照： [スマート・ウィザード](../deploy/smart-wizards.md).<!--MAGECLOUD-1910 -->

- ![新規アイコン](../../assets/new.svg) **Symfony Console のサポート**—Adobe Commerce 2.3 で Symfony Console 4 がサポートされるようになりました。<!-- MAGECLOUD-1966 -->

- ![修正アイコン](../../assets/fix.svg) **Cron スケジュールの最適化**- cron 関連の問題のデバッグに役立つように、キュー管理と拡張ログを改善しました。<!-- MAGECLOUD-1607 -->

- ![修正アイコン](../../assets/fix.svg) デプロイメントの検証が失敗するのは、 `ADMIN_EMAIL` または `ADMIN_USERNAME` 値は既存の管理者アカウントと同じです。<!-- MAGECLOUD-1221 -->

- ![修正アイコン](../../assets/fix.svg) 2.2.x バージョンの SOLR のサポートを削除。 2.1.x バージョンでは、SOLR を有効にする機能が保持されています。<!-- MAGECLOUD-1282 -->

- ![修正アイコン](../../assets/fix.svg) ステージング環境と実稼動環境に最初にインストールされる PRO プロジェクトには、Elasticsearch用に異なるインデックスプレフィックスが含まれるようになりました。これにより、各環境に属するレコードを特定しながら、競合の可能性を防ぎます。<!-- MAGECLOUD-1489 -->

- ![修正アイコン](../../assets/fix.svg) 静的コンテンツのデプロイメント中にレガシーアーキテクチャのビルドフェーズが中断される問題を修正しました。<!-- MAGECLOUD-2021 -->

- ![修正アイコン](../../assets/fix.svg) **Cron 固有の改善点**— cron の実装を修正しました。<!-- MAGECLOUD-1607 -->

   - Cron キューがすぐにいっぱいになる問題を修正しました。 これで、古い cron ジョブがより信頼性の高い方法でクリアされます。

   - Cron ジョブのシーケンスを再編成して、個別のスレッド内のすべてのジョブが一般グループの前に起動するようにしました。

   - cron に関する問題のデバッグをより適切に支援するために、ログ機能を改善しました。

   - **メモ** – このリリースでは、cron 関連の多くの問題に対処しています。 現在、で cron 関連のパッチを使用している場合 _m2 – ホットフィックス_、それらを削除します。

- ![修正アイコン](../../assets/fix.svg) **SCD 固有の改善点**—

   - を使用できます `VERBOSE_COMMANDS` および `SCD_COMPRESSION_LEVEL` 両方の期間の環境変数 _ビルド_ およびデプロイフェーズ。<!-- MAGECLOUD-1819 -->

   - で予期しない値が発生した場合、デプロイメントがランダムエラーで失敗する問題を修正しました `SCD_COMPRESSION_LEVEL` 環境変数。 設定の検証を改善して、意味のある通知を提供するようになりました。 参照： [`SCD_COMPRESSION_LEVEL`](../environment/variables-build.md#scd_compression_level) （使用可能な値）。<!-- MAGECLOUD-2043 -->

   - の動作を修正 `SCD_COMPRESSION_LEVEL` 環境変数の設定フローにより、オーバーライドが期待どおりに動作します。<!-- MAGECLOUD-2044 -->

   - を設定できない問題を修正しました `SCD_THREADS` の環境変数 `.magento.env.yaml` ファイル _deploy_ ステージ。<!-- MAGECLOUD-2046 -->

## v2002.0.10

- ![新規アイコン](../../assets/new.svg) **静的コンテンツデプロイメント（SCD）** – 要求に応じて（オンデマンドで）静的コンテンツを生成する、新しい代替デプロイメントプロセスがあります。 これにより、最も重要なアセットを生成できるので、ダウンタイムが短縮され、キャッシュ処理が改善されます。<!-- MAGECLOUD-1285 -->

   - **新しい環境変数** – が追加されました `SCD_ON_DEMAND` 静的コンテンツをリクエスト時に生成するためのグローバル環境変数。<!-- MAGECLOUD-1738 -->

   - **デプロイ後フック** – が追加されました `post_deploy` フック用 `.magento.app.yaml` キャッシュをクリアし、キャッシュをプリロード（warms）するファイル _後_ コンテナが接続の受け入れを開始します。 これは、にステージング環境と実稼動環境が含まれている Pro プロジェクトでのみ使用できます [!DNL Cloud Console] スタータープロジェクトの場合はです。 必須ではありませんが、と並行して動作します `SCD_ON_DEMAND` 環境変数。<!-- MAGECLOUD-1788 -->

- ![新規アイコン](../../assets/new.svg) **最適化** – 導入時にファイルの移動またはコピーを最適化し、導入スピードを向上させ、ファイル・システムへの負荷を軽減します。<!-- MAGECLOUD-1842 -->

- ![新規アイコン](../../assets/new.svg) **デプロイメントログ**- デプロイメントプロセス中にログを出力するための Syslog および Graylog Extended Log Format （GELF） ハンドラを有効にする機能が追加されました。 参照： [ログハンドラー](../environment/log-handlers.md).<!-- MAGECLOUD-1751 -->

- ![新規アイコン](../../assets/new.svg) 以下を追加しました [**環境変数**](../environment/variables-intro.md):

   - `CRYPT_KEY` – データベースを移動するときに、暗号化キーを別の環境に提供します。<!-- MAGECLOUD-1556 -->

   - `SKIP_HTML_MINIFICATION`—_グローバル_ 静的ビューファイルのコピーをスキップする環境変数 `var/view_preprocessed` ディレクトリを指定し、必要に応じて縮小HTMLを生成します。<!-- MAGECLOUD-1621 and MAGECLOUD-1736-->

   - `SCD_ON_DEMAND`—_グローバル_ 静的コンテンツをリクエスト時に生成するための環境変数。<!-- MAGECLOUD-1738 -->

   - `WARM_UP_PAGES`- キャッシュのプリロードに使用するページをリストできます。 新しいで使用可能 [デプロイ後変数](../environment/variables-post-deploy.md).

- ![修正アイコン](../../assets/fix.svg) インスタンス上のデプロイメントを中断する、ローカルに適用されたパッチが関与する問題を修正しました。 これで、ECE-Tools はパッチが適用されたことを検出できます。<!-- MAGECLOUD-982 -->

- ![修正アイコン](../../assets/fix.svg) JavaScript バンドルと GZIP 機能の間の競合を修正しました。 これで、これらの機能は正しく連携して動作します。<!-- MAGECLOUD-1735 -->

- ![修正アイコン](../../assets/fix.svg) 以前のバージョンの PHP 7.0.x を使用すると ECE-Tools CLI コマンドが失敗する問題を修正しました。<!-- MAGECLOUD-1744 -->

- ![修正アイコン](../../assets/fix.svg) 複数スレッドでのコンパクトな戦略で静的コンテンツのデプロイができない問題を修正しました。<!-- MAGECLOUD-1822 -->

- ![修正アイコン](../../assets/fix.svg) 管理者ログインの遅延を引き起こす Redis セッションロックの問題を修正しました。 また、2.1.x では修正も利用できます。<!-- MAGECLOUD-1853 -->

## v2002.0.9

>[!NOTE]
>
>あなたは必要があります [クラウドインフラストラクチャメタパッケージ上のAdobe Commerceのアップグレード](../dev-tools/install-package.md#update-the-metapackage) これで、今後のすべての更新を取得できます。

- ![新規アイコン](../../assets/new.svg) **ece-tools** – この `ece-tools` パッケージでAdobe Commerce 2.1.x がサポートされるようになりました。<!-- MAGECLOUD-1086 -->

- ![新規アイコン](../../assets/new.svg) **Redis 設定** – これで、以下を実行できます。 [redis の設定](../environment/variables-deploy.md#cache_configuration) 環境変数を使用したページとデフォルトのキャッシュおよび Redis セッションストレージ<!-- MAGECLOUD-1552 -->

- ![新規アイコン](../../assets/new.svg) **検索、AMQP、Redis サービスの改善** – サービス設定フローを統合して、すべてのサービスで同じように動作するようになりました。 を手動で編集する `env.php` サービスを設定するためのファイルはサポートされなくなりました。 環境変数または `.magento.env.yaml` 代わりに、ファイルを参照してください。<!-- MAGECLOUD-1437 -->

- ![修正アイコン](../../assets/fix.svg) **環境変数**—

   - の使用 `env:STATIC_CONTENT_THREADS` は非推奨（廃止予定）で、今後のリリースで削除される予定です。 の使用 [SCD_THREADS](../environment/variables-deploy.md#scd_threads) その代わり。<!-- MAGECLOUD-1507 -->

   - この `STATIC_CONTENT_EXCLUDE_THEMES` 環境変数は非推奨（廃止予定）となりました。 を使用する必要があります `SCD_EXCLUDE_THEMES` 代わりに環境変数を使用します。<!-- MAGECLOUD-1640 -->

- ![修正アイコン](../../assets/fix.svg) **ログ** – 組み込みのパッチ適用オペレーションに関するログ作成を合理化しました。<!-- MAGECLOUD-1674 -->

- ![修正アイコン](../../assets/fix.svg) 削除しました `developer` モードサポートと `APPLICATION_MODE` 予期しない動作を引き起こしていたため、環境変数に影響を及ぼします。<!-- MAGECLOUD-1615 -->

- ![修正アイコン](../../assets/fix.svg) Redis に関連する静的コンテンツのデプロイメントの失敗を引き起こす問題を修正しました。 現在は、設計どおりにマルチスレッド静的コンテンツのデプロイメントが実行されます。<!-- MAGECLOUD-1630 -->

- ![修正アイコン](../../assets/fix.svg) を実行した後に機密としてマークされる管理者の設定フィールドにユーザーが変更を保存できなかった問題を修正しました `app:config:dump` コマンド。<!-- MAGECLOUD-1175 -->

- ![修正アイコン](../../assets/fix.svg) の以前のバージョンへのサポートを追加しました `symfony/yaml` 最新バージョンとまだ互換性がない一部のパッケージとの競合を修正します。<!-- MAGECLOUD-1674 -->

## v2002.0.8

>[!NOTE]
>
>合併しました `vendor/magento/ece-patches` （を使用） `vendor/magento/ece-tools` このリリースではです。 を更新する必要はなくなりました `vendor/magento/ece-patches` 別々にパッケージ化します。

**新機能：**

- **ログの改善**<!-- MAGECLOUD-1253 -MAGECLOUD-1495 -->
   - ビルドまたはデプロイプロセスで環境変数が上書きされる際の説明を改善するために、ログメッセージを改善しました。
   - インストールとアップグレードの進行状況をリアルタイムで確認できるようになりました。 を追跡する `install_update.log` 進行状況を表示するファイル。 以下に例を挙げます。

     ```bash
     tail -f var/log/install_upgrade.log
     ```

- **新しい cron コマンド** – では、スタックした特定の cron ジョブをすべて停止して再起動するのではなく、それらのジョブをロック解除できるようになりました。 [`cron:unlock`](https://support.magento.com/hc/en-us/articles/360033099451) コマンド。 2.1 では利用できません。<!-- MAGECLOUD-1367 -->

- **統合設定ファイル** – を使用して、ビルドとデプロイのステージを設定できるようになりました [`.magento.env.yaml`](https://devdocs.magento.com/cloud/project/magento-env-yaml.html) ファイル。<!-- MAGECLOUD-1369 -->

- **設定ファイルのバックアップ** – 展開プロセスでは、のバックアップが自動的に作成されるようになりました `app/etc/env.php` および `app/etc/config.php` デプロイ後の設定ファイル。 以下も追加しました [新しい CLI コマンド](https://support.magento.com/hc/en-us/articles/360033182871) バックアップからこれらの構成ファイルを復元します。<!-- MAGECLOUD-1372 -->

- **検証エラーのトラブルシューティング** – 検証エラーを解決するために使用する必要があるコマンドを変更しました。 `config.php` には、静的コンテンツのデプロイメントに十分なデータが含まれていません。 以前は、エラーメッセージからを実行するように指示されていました `bin/magento app:config:dump`. さあ、走らなくちゃ `php ./vendor/bin/ece-tools config:dump`.<!-- MAGECLOUD-1491 -->

- **新しい環境変数** – 環境変数を使用してカスタムを接続できるようになりました [検索](../environment/variables-deploy.md#search_configuration) および [AMQP ベース](../environment/variables-deploy.md#queue_configuration) サイトへのサービス。<!-- MAGECLOUD-1410 -->

- 私たちはスマートパッチを実装しました。 これで、クラウドインフラストラクチャー上のAdobe Commerceのバージョンではなく、パッチが適用されたパッケージバージョンに基づいて、パッケージでパッチが適用されます。<!--MAGECLOUD-1090-->

**解決された問題：**

- ビルドエラーの原因となっていたログの問題を修正しました。<!-- MAGECLOUD-1162 -->

- インタラクティブモードでデプロイメントを実行するとタイムアウト例外が発生する問題を修正しました。<!-- MAGECLOUD-1389 -->

- 静的コンテンツ生成にコンパクト戦略を使用するとエラーが発生する問題を修正しました。 2.1 では利用できません。<!-- MAGECLOUD-1446 MAGECLOUD-1485-->

- デプロイメントスクリプトがステージング環境と実稼動環境を適切に識別しない問題を修正しました。<!-- MAGECLOUD-1493 -->

- ネットワークの問題によってデータベース接続が中断されたり、インストールおよびアップグレードプロセス中にエラーが発生したりする問題を修正しました。<!-- MAGECLOUD-1520 -->

- を使用して設定ファイルをエクスポートできない問題を修正しました `app:config:dump` 2 回以上。 2.1 では利用できません。<!--  MAGECLOUD-1567  -->

- Redis セッションを修正しました _ロック_ を引き起こした問題 _Admin_ ログインの遅延。 2.1 では利用できません。<!--  MAGECLOUD-1582  -->

- 他の Composer ベースのパッチ適用モジュールとの競合を引き起こしていたバージョン管理に関連する実装の問題を修正しました。<!-- MAGECLOUD-1450 -->

- インポート時に PHP のメモリに関する問題を修正しました。<!-- MAGECLOUD-1310 -->

- パッチを削除しました。のバグを修正しました。 `colinmollenhour/credis` v1.6:cloud infrastructure 2.2.1 でAdobe Commerceのサポートを有効にします。2.1 では利用できません。<!-- MAGECLOUD-1033 -->

## v2002.0.7

**解決された問題：**

- 削除しました `var/view_preprocessed` javascript の縮小の競合を引き起こしている問題を修正するためのシンボリックリンク。<!-- MAGECLOUD-1454 -->

## v2002.0.6

**解決された問題：**

- が原因で発生していた問題を修正しました `gzip` ファイル名またはディレクトリ名にスペースが含まれている場合にエラーが発生します。<!-- MAGECLOUD-1413 -->

- デプロイメントスクリプトでモジュールの依存関係を適切に認識して有効にできなかった問題を修正しました。<!-- MAGECLOUD-1424 -->

## v2002.0.5

**新機能：**

- **Cron コンシューマーと環境変数の設定** – 新しいを使用して cron コンシューマーを設定できるようになりました `CRON_CONSUMERS_RUNNER` 環境変数。

- **設定のスキャン** – 現在は、ビルド/デプロイ・プロセス中に重要なコンポーネントをスキャンし、スキャンが失敗した場合はプロセスを停止します。これにより、サイトがメンテナンス・モードになっていることが原因で発生する不要なダウンタイムを回避できます。

- **通知のビルド/デプロイ** – に使用できる設定ファイルを追加しました。 [Slackやメール通知の設定](../environment/set-up-notifications.md) すべての環境でビルド/デプロイアクションを実行する場合。

- **静的コンテンツ圧縮** – 静的コンテンツを次を使用して圧縮するようになりました [gzip](https://www.gnu.org/software/gzip/) ビルドおよびデプロイフェーズ中に行います。 この圧縮と Fastly 圧縮を組み合わせると、ストアのサイズを縮小し、デプロイメント速度を向上させることができます。 必要に応じて、 [ビルドオプション](../environment/variables-build.md) または [変数をデプロイ](../environment/variables-deploy.md). 詳しくは、次のトピックを参照してください。

   - [アプリケーション環境変数](../application/variables-property.md)

   - [静的コンテンツのデプロイメントパフォーマンス](../deploy/static-content.md)

   - [デプロイメントプロセス](https://devdocs.magento.com/cloud/reference/discover-deploy.html)

- **設定管理** – 以下を自動生成します。 `app/etc/config.php` ファイルが Git リポジトリに存在しない場合は、ビルドフェーズ中にファイルを保存します。 自動生成されたファイルには、モジュールと拡張子のリストのみが含まれます。 ファイルが既に存在する場合、ビルドフェーズは通常どおり続行されます。 フォローしている場合 [設定の管理](../store/store-settings.md) 後で、コマンドを使用すると、追加の手順を必要とせずにファイルが更新されます。 こちらを参照してください [デプロイメントプロセス](https://devdocs.magento.com/cloud/reference/discover-deploy.html) を参照してください。

- **データベースダンプ** – を追加しました。 `magento/ece-tools` すべての環境でデータベースダンプを作成する CLI コマンド。 Pro Plan の実稼動環境の場合、このコマンドは 3 つの高可用性ノードのうちの 1 つからのみダンプするので、ダンプ中に別のノードに書き込まれた実稼動データはコピーされません。 実稼動環境でデータベースダンプを実行する前に、アプリケーションをメンテナンスモードにすることをお勧めします。 参照： [バックアップ管理](https://devdocs.magento.com/cloud/project/project-webint-snap.html#db-dump) を参照してください。

- **Cron 間隔の制限を解除**—us-3、eu-3、ap-3 の各地域でプロビジョニングされたすべての環境のデフォルトの cron 間隔は 1 分です。 その他のすべての地域のデフォルトの cron 間隔は、Pro 統合環境の場合は 5 分、Pro ステージング環境と実稼動環境の場合は 1 分です。 既存の cron ジョブを変更するには、で設定を編集します。 `.magento.app.yaml` または、実稼働/ステージング環境用のサポートチケットを作成します。 こちらを参照してください [cron ジョブの設定](../application/crons-property.md#set-up-cron-jobs) を参照してください。

**解決された問題：**

- を呼び出すデプロイプロセスが原因で、デプロイに時間がかかる問題を修正しました `cache-clean` 静的コンテンツのデプロイメントの前に操作します。<!-- MAGECLOUD-1327 -->

- 実稼動環境へのデプロイメントの静的コンテンツ生成手順でエラーが発生する問題を修正しました。<!-- MAGECLOUD-1322 -->

- 一部ののエラーを修正しました `magento/ece-tools` 出力をにログ記録するコマンド `stderr`.<!-- MAGECLOUD-1264 -->

- でベース URL 値を使用できない問題を修正しました `env.php` 分岐した分岐で更新されることから。<!-- MAGECLOUD-1242 -->

- の原因となっている問題を修正しました `magento setup:install` セキュアでないプレフィックスを追加するコマンド （`http://`）を使用してベース URL を保護します。<!-- MAGECLOUD-1171 -->

- パッチエラーがデプロイメントの失敗を引き起こさない問題を修正しました。<!-- MAGECLOUD-1170 -->

- を防ぐ問題を修正しました `ece-tools` パッチを適用できない場合に実行を停止して例外をスローする。<!-- MAGECLOUD-1152 -->

- 管理者でHTMLの縮小を有効にした後、ストアフロントを読み込む際にエラーが発生する問題を修正しました。<!-- MAGECLOUD-1138 -->

## v2002.0.4

**解決された問題：**

- これで、 [スタックした cron ジョブを手動でリセット](https://support.magento.com/hc/en-us/articles/360033099451) ssh アクセス経由ですべての環境で CLI コマンドを使用する。 デプロイメントプロセスによって、cron ジョブが自動的にリセットされます。<!-- MAGECLOUD-1355 -->

## v2002.0.3

**解決された問題：**

- Redis が読み取り/書き込みに時間がかかりすぎているので、ページがタイムアウトする問題を修正しました。 これで、を使用できます `disable_locking` この問題を防ぐには、Redis 設定のパラメーターを使用します。<!-- MAGECLOUD-1311 -->

## v2002.0.2

**解決された問題：**

- この [!DNL RabbitMQ] 設定プロセスは、必要なすべてのパラメーターを自動的に取得するようになりました。<!-- MAGECLOUD-1246 -->

## v2002.0.1

**新機能：**

- クラウドインフラストラクチャー上のAdobe Commerceでスコープおよびがサポートされるようになりました。 [静的コンテンツのデプロイメント戦略](https://devdocs.magento.com/guides/v2.3/config-guide/cli/config-cli-subcommands-static-deploy-strategies.html). が追加されました `–s` デフォルト設定がのパラメーター `quick` （静的コンテンツのデプロイメント戦略の場合）。 環境変数を使用できます [SCD_STRATEGY](../environment/variables-deploy.md) これらの戦略をカスタマイズし、ビルドアクションやデプロイアクションで使用するには、 この変数は、オプションをサポートします `standard`, `quick`、または `compact`. を選択する場合 `compact`を上書きします。 `STATIC_CONTENT_THREADS` 次を使用して値 `1`（特に実稼動環境では、デプロイメントの速度が低下する可能性があります）。 2.1 では利用できません。<!--- MAGECLOUD-1057 -->

- 環境に関するログファイルを作成して、ビルドアクションとデプロイアクションをキャプチャおよびコンパイルしました。 この `var/log/cloud.log` ファイルは、ルートアプリケーションディレクトリにあります。<!--- MAGECLOUD-1014 & MAGECLOUD-1023 -->

**解決された問題：**

- をリファクタリング `ece-tools` cloud infrastructure 2.2.0 以降のAdobe Commerceと互換性を持たせるためのパッケージ。<!-- MAGECLOUD-919 & MAGECLOUD-1030 -->

- 妨げとなっていた問題を修正しました `ece-tools` パッチを適用できない場合に実行を停止して例外をスローする。<!-- MAGECLOUD-1186 -->

- ビルド中に依存関係の挿入（di）コンパイルがスキップされると例外がスローされる問題を修正しました。<!-- MAGECLOUD-1047 & MAGECLOUD-1049 -->

- デプロイプロセスによってのカスタム Redis 設定が上書きされる問題を修正しました。 `env.php` ファイル。<!-- MAGECLOUD-1019 -->

- デフォルトのセキュア管理者で無効になっていることが原因で、リダイレクトループが発生していた問題を修正しました。<!-- MAGECLOUD-1020 -->

## v2002.0.0

>[!WARNING]
>
>このパッケージは、クラウドインフラストラクチャー上の他のバージョンのAdobe Commerceとは互換性がなくなりました。 **してはならない** を使用します。

### 初回リリース

の初回リリース `ece-tools` （クラウドインフラストラクチャー 2.2.0 上のAdobe Commerceの場合）
