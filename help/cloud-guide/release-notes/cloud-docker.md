---
title: Cloud Docker パッケージ
description: Cloud Docker パッケージの最新の改善点のリストを確認します。
feature: Cloud, Docker, Release Notes
recommendations: noDisplay, catalog
last-substantial-update: 2024-04-08T00:00:00Z
exl-id: 907d977f-2e9c-4553-a46b-000bc6a57b28
source-git-commit: bc76cba0219f16fd055c20289811b51c35c9b026
workflow-type: tm+mt
source-wordcount: '3662'
ht-degree: 0%

---

# Cloud Docker パッケージ

この [`magento/magento-cloud-docker`](https://github.com/magento/magento-cloud-docker) Adobe Commerceをローカルクラウド環境にデプロイする機能と Docker イメージを提供するパッケージです。 これらのリリースノートでは、のコンポーネントであるこのパッケージの最新の改善点について説明します [Commerce用 Cloud Tools スイート](cloud-tools-suite.md).

この `magento/magento-cloud-docker` パッケージでは、次のバージョンシーケンスを使用します。 `<major>.<minor>.<patch>`

リリースノートには次のものが含まれます。

- ![新規アイコン](../../assets/new.svg) 新機能
- ![修正アイコン](../../assets/fix.svg) 修正点および改善点

<!--Add release notes below-->

## v1.3.7 {#latest}

リリース日：2024 年 4 月 8 日（PT）

- ![新規アイコン](../../assets/new.svg) **PHP** — PHP 8.3 および PHP 8.3 のイメージのサポートを追加しました。
- ![新規アイコン](../../assets/new.svg) **Nginx**  – 画像 nginx v. 1.24 を追加しました。
- ![新規アイコン](../../assets/new.svg) **Opensearch**  – 画像 OpenSearch v. 2.12、1.3 を追加しました。
- ![新規アイコン](../../assets/new.svg) **コンポーザー** - Composer バージョンを 2.2.23 に更新しました。

## v1.3.6

リリース日：2023 年 7 月 31 日（PT）

- ![新規アイコン](../../assets/new.svg) **新しいサービスバージョンを追加しました**—OpenSearch 2.5 を使用します。
- ![新規アイコン](../../assets/new.svg) **Composer キャッシュの有効化**— Docker コンテナの開始時に Composer のキャッシュのクリアを有効にするように Docker 設定を拡張できるようになりました。 参照： [Docker 設定の拡張](https://developer.adobe.com/commerce/cloud-tools/docker/configure/extend-docker-configuration/) が含まれる _Cloud Docker for Commerce_ ガイド。

## v1.3.5

リリース日：2023 年 3 月 10 日（PT）

- ![新規アイコン](../../assets/new.svg) **ionCube**— PHP 8.1 のイメージに ionCube 拡張モジュールを追加しました。
- ![新規アイコン](../../assets/new.svg) **新しいサービスバージョンを追加しました**—OpenSearch 2.3 と 2.4、PHP 8.2、Varnish 7.1.1。
- ![新規アイコン](../../assets/new.svg) **PHP 8.2 のサポートの強化**—Commerce 2.4.6 をサポートするために、特定の PHP 8.2.x バージョンの互換性の問題を修正しました。
- ![修正アイコン](../../assets/fix.svg) **Composer の問題**- Docker コンテナ内の Composer バージョンを更新した後に発生していた問題を修正しました。

## v1.3.4

リリース日：2022 年 10 月 27 日（PT）

- ![新規アイコン](../../assets/new.svg) **新しいワニス画像を追加しました**- ワニス 6.5、7.0、7.1 の画像を追加しました。<!-- MCLOUD-7879 -->

## v1.3.3

リリース日：2022 年 9 月 13 日（PT）

- ![新規アイコン](../../assets/new.svg) **（Apple M1 （ARM64）のサポート）**- Apple M1 （ARM64）アーキテクチャのサポートを有効にするために、Docker イメージに変更を追加しました。<!-- MCLOUD-7989-2 MCLOUD-7989 -->
- ![修正アイコン](../../assets/fix.svg) **メールホッグ** – 開発者モード中にメールホッグサービスがメールを取得しない問題を修正しました。<!-- MCLOUD-8643 -->
- ![修正アイコン](../../assets/fix.svg) **init-docker.sh** – のサービスバージョンバリデーターを修正した。 `init-docker.sh` スクリプト。<!-- MCLOUD-8765 -->

## v1.3.2

リリース日：2022 年 3 月 31 日（PT）

- ![新規アイコン](../../assets/new.svg) **Elasticsearch 7.10 の画像を追加しました**<!-- MCLOUD-8548 -->

## v1.3.1

リリース日：2022 年 3 月 10 日（PT）

- ![新規アイコン](../../assets/new.svg) **PHP 8.1 のサポート**—PHP 8.1 のサポートを追加しました。
- ![新規アイコン](../../assets/new.svg) **OpenSearch**- OpenSearch バージョン 1.1 および 1.2 の画像を追加しました。
- ![新規アイコン](../../assets/new.svg) **Composer 2.1**— PHP 8.x イメージで composer 2.1.x をデフォルトで設定します。
- ![新規アイコン](../../assets/new.svg) **PHP 画像の改善**—

   - PHP 8.1 イメージを追加しました。
   - xDebug バージョン 3.1.2 のアップグレード
   - Xmlrpc 1.0.0RC3 をアップグレードしました

- ![修正アイコン](../../assets/fix.svg) **Elasticsearchと OpenSearch の改善**- Elasticsearchと OpenSearch Dockerfiles の改善。Elasticsearch 5.2 のイメージを削除。
- ![修正アイコン](../../assets/fix.svg) **ナトリウム延長** – が有効になった `sodium` すべての PHP イメージでデフォルトで拡張モジュールを使用します。
- ![修正アイコン](../../assets/fix.svg) **Composer キャッシュ ボリューム**- キャッシュされた Composer パッケージを持つ Composer キャッシュ ボリュームの固定パス。
- ![修正アイコン](../../assets/fix.svg) **Nginx のメモリ制限**- NGINX イメージのメモリ制限を修正しました。

## v1.3.0

リリース日：2021 年 10 月 25 日（PT）

- ![修正アイコン](../../assets/fix.svg) **開発者モードワークフローの改善** – 以前は、ビルド手順とデプロイ手順でモードを指定する必要がありました。 さて、 `--mode` のオプション `build` 手順によって、後でモードが決定されます `deploy` ステップ。 デプロイメント後のモードの設定は不要になりました。 参照： [開発者モード](https://devdocs.magento.com/cloud/docker/docker-mode-developer.html).<!-- ACMP-1086 -->
- ![修正アイコン](../../assets/fix.svg) **読み取り専用ファイルシステムの改善**—<!-- ACMP-1106 -->
   - メール設定用の PHP コンテナを起動する際の問題を修正しました。
   - INI ファイルで環境変数を使用できます。
   - PHP エントリポイントに書き込み権限が必要ないことを確認します。
- ![修正アイコン](../../assets/fix.svg) **ノードの更新** – バンドルされた Node のバージョンを更新します。PHP-CLI イメージに Node をインストールする際に、現在の LTS バージョンが使用されるようになりました。<!-- ACMP-1539 -->
- ![修正アイコン](../../assets/fix.svg) **署名を更新**— Adobe Commerce 2.4.4 と互換性を持たせるために、Symfony config の依存関係を更新しました。<!-- ACMP-1533 -->

## v1.2.4

リリース日：2021 年 7 月 29 日（PT）

- ![新規アイコン](../../assets/new.svg) **新規 `Zookeeper` コンテナ** – が追加されました [Zookeeper コンテナ](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#zookeeper-container) クラウドインフラストラクチャー上のAdobe Commerceにデプロイされていないプロジェクトのロックプロバイダー設定を管理します。<!--MCLOUD-8000-->

- ![新規アイコン](../../assets/new.svg) **Composer 2.0 がサポートされるようになりました。**—Composer バージョン 2.0 が Composer 設定ファイルに追加され、提供終了が近づいている Composer 1.0 からのアップグレードがサポートされるようになりました。<!--MCLOUD-8003-->

## v1.2.3

リリース日：2021 年 6 月 14 日

- ![新規アイコン](../../assets/new.svg) **PHP 8.0 を追加しました。**—PHP をバージョン 8.0 に更新し、PHP 8.0 に含まれるすべての新機能と最適化を利用できるようにしました。<!--MCLOUD-7941-->
- ![新規アイコン](../../assets/new.svg) **Varnish 6.6 およびElasticsearch 7.11.2 に更新されました。** – 次のリンクは、に関するリリース情報を提供します [Varnish Cache 6.6](https://varnish-cache.org/releases/rel6.6.0.html#rel6-6-0) およびElasticsearch 7.11.2。<!--MCLOUD-7921-->
- ![新規アイコン](../../assets/new.svg) **追加済み `ioncube` php 7.4 の拡張機能画像** – この `ioncube` 拡張モジュールは、PHP 7.3 から PHP 7.4 へのアップグレードから最初に除外された後、PHP 7.4 のイメージに再度追加されました。 *[Mattskr によって送信されました](https://github.com/magento/magento-cloud-docker/pull/314).*<!--PR #314-->
- ![新規アイコン](../../assets/new.svg) **ファイル同期オプションを追加しました。`manual-native`** – この `manual-native` ファイル同期オプションを使用すると、同期を手動で制御でき、macOSと Windows 環境で最高のパフォーマンスが得られます。 の使用について `manual-native` オプション： [開発者モード](https://devdocs.magento.com/cloud/docker/docker-mode-developer.html) および [Docker デベロッパー環境でのデータの同期](https://devdocs.magento.com/cloud/docker/docker-syncing-data.html#file-synchronization-options).<!--MCLOUD-7977-->
- ![新規アイコン](../../assets/new.svg) **からボリューム削除を削除しました `up` および `down` コマンド** – この `--volume` オプションがから削除されました `bin/magento-docker up` および `bin/magento-docker down` 新しいに置き換えられたコマンド `bin/magento-docker init` データ損失警告を伴うコマンド。 この変更により、誤ってデータが失われるのを防ぐことができます。 *[Joeshelton-wagento より提出](https://github.com/magento/magento-cloud-docker/pull/319).*<!--PR #319-->
- ![修正アイコン](../../assets/fix.svg) **更新日 `CN` 生成された証明書の値**— ハードコードを削除しました。 `CN` dockerfile からの値。 この値で証明書エラー（`NET::ERR_CERT_INVALID`）に含める必要があります `--host` オプション： `ece-docker build:compose` 無視するコマンド。<!--MCLOUD-7934-->

## v1.2.2

リリース日：2021 年 4 月 20 日（PT）

- ![新規アイコン](../../assets/new.svg) **更新日 `host.docker.internal` プラットホームに依存しない**—Ubuntu、Windows、macOSに同じ Docker Compose スクリプトを作成できるようになりました。 Ubuntu で Xdebug を使用する際に、個別の環境変数が必要なくなりました。 [Igor Vitol によって修正が送信されました](https://github.com/magento/magento-cloud-docker/pull/299).<!--Issue #298-->
- ![新規アイコン](../../assets/new.svg) **init-docker.sh を更新** – が追加されました `mounts` ～に反対する `MAGENTO_CLOUD_APPLICATION` 環境変数。 [Chiranjeevi によって送信された修正](https://github.com/magento/magento-cloud-docker/pull/299).<!--Issue #299-->
- ![新規アイコン](../../assets/new.svg) **init-docker.sh を更新** – さんが、 `init-docker.sh` php 7.4 および Cloud Docker 1.2.1 バージョンのスクリプト。 [Adarsh Manickam によって修正が送信されました](https://github.com/magento/magento-cloud-docker/pull/300).<!--Issue #300-->
- ![新規アイコン](../../assets/new.svg) **ナトリウムはデフォルトで有効になっています** – が有効になった `sodium` PHP Docker イメージ内のデフォルトの PHP 拡張機能。<!--MCLOUD-7548-->
- ![新規アイコン](../../assets/new.svg) **`custom-registry`オプション** – が追加されました `--custom-registry` 対するオプション `php ./vendor/bin/ece-docker build:compose` 独自の画像レジストリを使用するためのコマンド。<!--MCLOUD-7476-->

  ```bash
  ./vendor/bin/ece-docker build:compose --custom-registry=my-registry.example.com
  ```

- ![新規アイコン](../../assets/new.svg) **古いElasticsearchバージョンを削除しました**- Elasticsearchのバージョン 1.7 および 2.4 をElasticsearchイメージから削除しました。<!--MCLOUD-7504-->
- ![新規アイコン](../../assets/new.svg) **NGINX 証明書の自動生成**- NGINX イメージから既存の証明書を削除しました。 NGINX 証明書は、セキュリティを向上させるために、新しいデプロイメントのたびに自動生成されるようになりました。<!--MCLOUD-7396-->
- ![修正アイコン](../../assets/fix.svg) **Enabled`opcache.validate_timestamps`** – が有効になった `opcache.validate_timestamps` 開発者モードのデフォルトの PHP 設定。 この設定を有効にすると、ファイルシステムに対する変更が Docker で認識されない問題が修正されました。<!--MCLOUD-7466-->
- ![修正アイコン](../../assets/fix.svg) **固定`build:custom:compose`** – を修正した。 `build:custom:compose` ビルド処理中にファイルを上書きできない場合にエラーをスローするコマンド。 エラーをスローすると、次のような状況を防ぐことができます `docker-compose up` 間違ったファイルを使用している可能性があります。<!--MCLOUD-7457-->
- ![修正アイコン](../../assets/fix.svg) **固定 `--sync_engine="native"` オプション** – 実稼動モードの場合の問題を修正しました（`--mode="production"`）、 `--sync_engine="native"` オプションを選択すると、内のローカルフォルダーのエントリはに作成されません `docker.composer.yml` ファイル。<!--MCLOUD-7254-->
- ![修正アイコン](../../assets/fix.svg) **修正されたサービス バージョン検証エラー** – のサービスバージョンを追加しました。 [!DNL RabbitMQ]、Elasticsearchおよびその他ののサービス `type` のプロパティ `MAGENTO_CLOUD_RELATIONSHIP` 変数。 これらのバージョンのへの追加 `relationships` 変数では、デプロイフェーズ中に発生した検証エラーを修正しました。<!--MCLOUD-7572-->

## v1.2.1

リリース日：2020 年 12 月 21 日（PT）

- ![新規アイコン](../../assets/new.svg) **NGINX コマンド オプション**—NGINX の数を変更するためのビルド コマンド オプションを追加しました。 `worker_processes` と NGINX `worker_connections` （TLS および web サービスの場合）。 この `worker_process` パラメーターは、値をに設定する機能を保持します `auto`. 例： <!--MCLOUD-7259-->

  ```terminal
  ./vendor/bin/ece-docker build:compose --nginx-worker-processes=2
  ./vendor/bin/ece-docker build:compose --nginx-worker-connections=2048
  ```

- ![新規アイコン](../../assets/new.svg) **TLS コマンドオプション**— TLS サービスを使用せずに設定を作成するためのビルドコマンドオプションを追加しました。 例： <!--MCLOUD-7259-->

  ```terminal
  ./vendor/bin/ece-docker build:compose --no-tls
  ```

- ![新規アイコン](../../assets/new.svg) **NGINX メモリ消費量**- TLS および Web サービスに対する NGINX プロセスによって消費されるメモリを削減します。<!--MCLOUD-7259-->

- ![新規アイコン](../../assets/new.svg) **Blackfire**- Cloud Docker イメージでBlackfire PHP 拡張をデフォルトで無効にします。

- ![修正アイコン](../../assets/fix.svg) **PHP-FPM コンテナ** – 変更により PHP-FPM コンテナのヘルスチェックを修正しました。 `WEB_PORT` から `80` 対象： `8080`.<!--MCLOUD-7232-->

- ![修正アイコン](../../assets/fix.svg) **無効なボリュームの命名** – 開発者モードで無効なボリューム名が発生するエラーを修正しました。<!--MCLOUD-7442-->

- ![修正アイコン](../../assets/fix.svg) **NGINX アップストリーム ポート** – 無限ループを避けるために、ポート 8080 を使用するように Docker NGINX 1.19 イメージを更新しました。 [Adarsh Manickam によって修正が送信されました](https://github.com/magento/magento-cloud-docker/pull/296).<!--Issue 295-->

## v1.2.0

リリース日：2020 年 11 月 9 日（PT）

- ![新規アイコン](../../assets/new.svg) **コンテナの更新 –**

   - ![新規アイコン](../../assets/new.svg) **PHP-FPM コンテナ**— gnupg PHP 拡張モジュールのサポートを追加しました。 [Zilker Technology の G Arvind による修正](https://github.com/magento/magento-cloud-docker/pull/210).<!--MCLOUD-5981-->

   - ![修正アイコン](../../assets/fix.svg) **データベースコンテナ** – ヘルスチェックコマンドに必要なデータベースパスワードを追加することで、データベースコンテナのヘルスチェックを修正しました。<!--MCLOUD-7122-->

   - ![新規アイコン](../../assets/new.svg) **Elasticsearchコンテナ**

      - 今後のAdobe Commerce リリースとの互換性のために、Elasticsearch 7.9 がサポートされるようになりました。<!--MCLOUD-7190-->

      - **Elasticsearchプラグインの設定** – からElasticsearchプラグイン設定情報を使用できるようになりました `services.yaml` 生成するファイル `docker-compose.yaml` cloud Docker for Commerce環境用のファイル。 参照： [Elasticsearchプラグイン](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#elasticsearch-plugins).<!--MCLOUD-2789-->

      - **Elasticsearchプラグインのサポート** – 次のElasticsearchプラグインのサポートが追加されました。 `analysis-icu`, `analysis-phonetic`, `analysis-stempel`、および `analysis-nori`. この `analysis-icu` および `analysis-phonetic` プラグインはデフォルトでインストールされます。 を追加または削除できます `analysis-stempel` および `analysis-nori` 必要に応じてプラグインを使用します。<!--MCLOUD-2789-->

   - ![新規アイコン](../../assets/new.svg) **CLI コンテナ**

      - **Docker PHP コンテナ内でコマンドを実行する** – これで、Cloud Docker CLI を使用して、ホストに PHP をインストールすることなく、Docker 環境の PHP コンテナ内でコマンドを実行できます。 例えば、次のコマンドは、設定をビルドします。  `./bin/magento-docker php 7.3 vendor/bin/ece-docker build:compose`. 参照： [Cloud Docker CLI](https://devdocs.magento.com/cloud/docker/docker-quick-reference.html#magento-cloud-docker-cli). [Zilker Technology の G Arvind による修正](https://github.com/magento/magento-cloud-docker/pull/209).<!--MCLOUD-5982-->

      - OpenSSH-client を PHP CLI コンテナに追加しました。 これで、次のような場合に、Composer で ssh エージェント転送を使用できます `composer.json` ファイルには、Composer コマンドを使用するために ssh クライアントを必要とするプライベート Git リポジトリが含まれています。<!--MCLOUD-6008-->

   - ![修正アイコン](../../assets/fix.svg) **TLS コンテナ** – さて、 [TLS コンテナ](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#tls-container) は、に基づいています `https://hub.docker.com/r/magento/magento-cloud-docker-nginx` CentOS イメージではなく Docker イメージ。 この変更により、Cloud Docker 環境のコンテナ間で HTTPS リクエストを送信する際にエラーが発生していた問題が修正されました。<!--MCLOUD-6469-->

   - ![新規アイコン](../../assets/new.svg) **コンテナをテスト**- アプリケーションテスト用のテストコンテナを追加し、 `--with-test` Docker のオプション `build:compose` Docker 環境でのテスト時にのみコンテナを作成するコマンド。 参照： [アプリケーションテスト](https://devdocs.magento.com/cloud/docker/docker-test-app-mftf.html).<!--MCLOUD-6394-->

   - ![新規アイコン](../../assets/new.svg) **FPM-XDEBUG コンテナ**

      - ![新規アイコン](../../assets/new.svg) **Linux で Xdebug を設定する** – が追加されました `--set-docker-host` のオプション `ece-docker build:compose` を設定するコマンド `host.docker.internal` xdebug コンテナの値。 このオプションは、Linux システムで Xdebug を使用する場合に必要です。 参照： [Docker 用の Xdebug の設定](https://devdocs.magento.com/cloud/docker/docker-development-debug.html).<!--MCLOUD-6430-->

      - ![修正アイコン](../../assets/fix.svg) 解決する Docker エントリポイントの Xdebug 変数設定を修正しました `uninitialized "with_xdebug" variable` ログのエラー。 [Florent Olivoud による修正の送信](https://github.com/magento/magento-cloud-docker/pull/218)<!--MCLOUD-6043-->

- ![新規アイコン](../../assets/new.svg) **Docker 設定の変更**

   - **MailHog 設定** – 以下を使用できるようになりました。 `ece-docker build:compose` mailhog を無効にしてポートを指定するコマンド オプション： `--no-mailhog`, `--mailhog-http-port`、および `--mailhog-smtp-port`. 参照： [メールの設定](https://devdocs.magento.com/cloud/docker/docker-config.html#set-up-email).<!--MCLOUD-6898, MCLOUD-6660-->

   - Cloud Docker for Commerce 1.2.0 以降では、Adobeで各パッチバージョンの Docker イメージが提供されるようになり、Docker configuration generator は、最新のバージョンを使用する代わりに、指定されたパッチバージョンで Docker 設定を作成します。 以前は、Docker Configuration Generator は最新のパッチバージョンを使用して設定をビルドしていましたが、以前のバージョンを使用してビルドされたCommerce環境用の Cloud Docker に違反する可能性がありました。<!--MCLOUD-7093-->

   - **カスタム Cloud Docker 設定でのカスタム画像およびバージョンの指定** – さんが、 `build:custom:compose` カスタム Docker 作成設定ファイル（`docker-compose.yaml`）に設定します。 参照： [カスタム Docker Compose 設定の作成](https://devdocs.magento.com/cloud/docker/docker-config-sources.html#build-a-custom-docker-compose-configuration). <!--MCLOUD-7089-->

   - ポート 443 を公開するように Docker ホスト設定を更新して、Adobe Commerce（`https://magento2.docker`）を選択します。 デフォルトポートは、 `--tls-port` Docker 設定ファイルを生成する際のオプションです。<!--MCLOUD-6806-->

- ![修正アイコン](../../assets/fix.svg) 次の場合に、Cloud Docker for Commerceのビルドが失敗する問題を修正しました `app/etc/env.php` ファイルが存在します。<!--MCLOUD-6732-->

- ![修正アイコン](../../assets/fix.svg) Linux または Windows Subsystem for Linux （WSL2）にCommerce用 Cloud Docker をデプロイする際の問題を防ぐために、指定したボリュームを通常のボリュームに置き換えるようにビルド設定を更新しました。<!--MCLOUD-6732-->

- ![修正アイコン](../../assets/fix.svg) Composer 2.0 をサポートするように Cloud Docker for Commerce機能テストを更新しました。<!--MCLOUD-7183-->

## v1.1.2

リリース日：2020 年 9 月 9 日（PT）

- ![新規アイコン](../../assets/new.svg) Elasticsearch 7.7 がサポートされるようになりました。<!--MCLOUD-6219-->

## v1.1.1

リリース日：2020 年 8 月 5 日（PT）

- ![修正アイコン](../../assets/fix.svg) **更新されたメール設定**- SendMail を使用する代わりに MailHog サービスをサポートするように、デフォルトのCommerce用 Cloud Docker 設定を更新しました。 参照： [メールの設定](https://devdocs.magento.com/cloud/docker/docker-config.html#set-up-email).<!--MCLOUD-5624-->

- ![修正アイコン](../../assets/fix.svg) 修正のため、PS ライブラリを Cloud Docker 環境設定に復元しました `ps:  command not found` エラー。<!--MCLOUD-6621-->

- ![修正アイコン](../../assets/fix.svg) デフォルトの Cloud Docker for Commerce設定を更新し、修正するデータベースエントリポイントと MariaDB ボリュームの自動マウントを削除しました `Cannot create container for service db` cloud Docker 環境の開始時に発生する可能性があるエラー。

  これで、次のオプションをに追加することで、データベースディレクトリをマウントするように Cloud Docker 環境を設定できるようになりました `ece-docker build:compose` コマンド： `--with-entry-point` および `with-mariadb-conf`. 参照： [サービス設定オプション](https://devdocs.magento.com/cloud/docker/docker-containers.html#service-configuration-options).<!--MCLOUD-6424-->

- ![新規アイコン](../../assets/new.svg) **CLI コマンドの更新**

| アクション | コマンド |
| ------------------------------------------------------------------------------- | -------------------------------------------------------------- |
| データベース コンテナーにエントリ ポイントを追加して、バックアップからデータベースを復元します | `./vendor/bin/ece-docker build:compose --db --with-entrypoint` |
| MariaDB 構成ボリュームの追加 | `./vendor/bin/ece-docker build:compose --db --mariadb-conf` |

## v1.1.0

リリース日：2020 年 6 月 25 日（PT）

- ![新規アイコン](../../assets/new.svg) **スプリット・データベース・パフォーマンス・ソリューションのサポートを追加** – これで、Cloud Docker 環境の分割データベースパフォーマンスソリューションを使用して、ストアを設定およびデプロイできます。<!--MCLOUD-3740-->

- ![新規アイコン](../../assets/new.svg) **Adobe CommerceとMagento Open Sourceデプロイメントのサポート**— Cloud Docker for Commerceを使用して、クラウドインフラストラクチャ上のAdobe Commerceでホストされていないプロジェクトのローカル開発環境をデプロイできるようになりました。<!--MCLOUD-5667-->

- ![新規アイコン](../../assets/new.svg) **Blackfire.io サポート** – の使用をサポートするようになりました。 [Blackfire.io 拡張機能](https://devdocs.magento.com/cloud/docker/docker-config-blackfire-io.html) 自動化されたパフォーマンステスト用。 [Zilker Technology の Adarsh Manickam によって修正が送信されました](https://github.com/magento/magento-cloud-docker/pull/202)<!--MCLOUD-5857-->

- ![新規アイコン](../../assets/new.svg) **コンテナの更新**

   - **ワニス**- サポートされているバージョンのクラウドアプリケーションテンプレートを使用して Cloud Docker 環境にAdobe Commerceをデプロイする場合、Varnish がデフォルトのキャッシュになりました。 参照： [ワニス容器](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#varnish-container).<!--MCLOUD-2634-->

   - さんがを追加しました `--no-varnish` cloud Docker 設定ファイルを生成する際に、Varnish サービスのインストールをスキップするオプション。<!--MCLOUD-2634-->

   - ![新規アイコン](../../assets/new.svg) **データベース**

      - MySQL データベースのサポートを追加しました。 これで、MariaDB または MySQL を使用して Cloud Docker 環境を設定できます。 参照： [サービス設定オプション](https://devdocs.magento.com/cloud/docker/docker-containers.html#service-configuration-options).<!--MCLOUD-5691-->

      - Docker コンポーズファイルを生成する際に、データベースレプリケーションの増分とオフセットの設定を指定する機能が追加されました。 参照： [サービスコンテナ](https://devdocs.magento.com/cloud/docker/docker-containers.html#service-containers).<!--MCLOUD-5735-->

   - ![新規アイコン](../../assets/new.svg) **PHP-FPM**

      - PHP 7.4 のサポートを追加。 [Zilker Technology の Mohanela Murugan による修正](https://github.com/magento/magento-cloud-docker/pull/198)<!--MCLOUD-198-->

      - をコピーできるようになりました `php.ini` ルートプロジェクトディレクトリのファイルを Cloud Docker 環境に追加し、カスタム PHP 設定を PHP-FPM および CLI コンテナに適用します。 参照： [PHP 設定のカスタマイズ](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#customize-php-settings). [Mathew Beane により Zilker Technology から送信された修正](https://github.com/magento/magento-cloud-docker/pull/130).<!--MCLOUD-6012-->

      - コンテナヘルスチェックを追加しました。 [Zilker Technology の Visanth Sampath によって送信された修正](https://github.com/magento/magento-cloud-docker/pull/188).<!--MCLOUD-5752-->

   - ![修正アイコン](../../assets/fix.svg) **Node.js**— セキュリティを向上させるために、デフォルトの Node.js バージョンをバージョン 8 からバージョン 10 に更新しました。 Node.js バージョン 8 は非推奨で、バグ修正やセキュリティパッチによる更新はなくなりました。 [Zilker Technology の Mohan Elamurugan による修正](https://github.com/magento/magento-cloud-docker/pull/183).<!--MCLOUD-5586-->

   - ![新規アイコン](../../assets/new.svg) **Elasticsearch**

      - Elasticsearch 6.8、7.2、7.5、7.6 がサポートされるようになりました。<!--MCLOUD-4050, MCLOUD-5855,MCLOUD-5860-->

      - をカスタマイズできるようになりました [Elasticsearchコンテナの設定](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#elasticsearch-container) docker 構成コンフィギュレーションファイルを生成するとき。<!--MCLOUD-3059-->

      - さんがを追加しました `--no-es` docker Compose 設定ファイルを生成するためのサービス設定オプションのオプションです。 Elasticsearchコンテナのインストールをスキップし、代わりに MySQL 検索を使用する場合は、このオプションを使用します。 このオプションは、Adobe Commerce バージョン 2.3.5 以前でのみサポートされています。<!--MCLOUD-3766-->

   - ![新規アイコン](../../assets/new.svg) **FPM-XDEBUG コンテナ**— Cloud Docker 環境で PHP をデバッグするために Xdebug をインストールおよび設定するサービス設定オプションを追加しました。 参照： [Xdebug の設定](https://devdocs.magento.com/cloud/docker/docker-development-debug.html).<!--MCLOUD-4098-->

- ![新規アイコン](../../assets/new.svg) **Docker 設定の変更**

   - PHP-FPM、Redis、Elasticsearch、および MySQL Docker サービスコンテナのヘルスチェックを追加しました。<!--MCLOUD-3335 and MCLOUD-5856-->

   - デフォルトのファイル同期モードをに変更しました。 `native` 開発者モード。<!--MCLOUD-3890 -->

   - の生成時に汎用 Docker サービスコンテナ画像にバージョン情報を追加しました。 `docker-compose.yml` ファイル。<!--MCLOUD-3878-->

   - アップストリームの PHP-FPM コンテナからの大きな応答を処理する機能を改善しました。 `fastcgi_buffers` nginx サーバーの値。<!--MCLOUD-5980-->

   - にファイルを同期するための 2 つ目の同期セッションを追加することで、変更ファイルの同期パフォーマンスが向上しました。 `vendor` ディレクトリ。 この変更により、ファイルの同期処理中に変更が停止するのを防ぎます。 [Mathew Beane により Zilker Technology から送信された修正](https://github.com/magento/magento-cloud-docker/pull/127).<!--MCLOUD-6010-->

   - ![新規アイコン](../../assets/new.svg) **CLI コマンドの更新**

| アクション | コマンド |
| -------- | --------------- |
| Redis キャッシュのクリア | `bin/magento-docker flush-redis` |
| Varnish キャッシュのクリア | `bin/magento-docker flush-varnish` |
| 既定の Varnish インストールをスキップする | `.vendor/bin/ece-docker build:compose --no-varnish`<!--MCLOUD-2634--> |
| [Elasticsearchオプションのカスタマイズ](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#elasticsearch-container) | `.vendor/bin/ece-docker build:compose --es-env-var`<!--MCLOUD-3059--> |
| [Elasticsearch設定を削除](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#elasticsearch-container) | `.vendor/bin/ece-docker build:compose --no-es`<!--MCLOUD-3766--> |
| MySQL バージョン 5.6 または 5.7 で DB コンテナを設定する | `./vendor/bin/ece-docker build:compose --db <mysql-version-number> --db-image mysql`<!--MCLOUD-5691--> |
| カスタムベース URL を指定 | `./vendor/bin/ece-docker build:compose --host=<hostname> --port=<port-number>`<!--MCLOUD-3063--> |
| [Xdebug 設定用のコンテナの追加](https://devdocs.magento.com/cloud/docker/docker-development-debug.html) | `.vendor/bin/ece-docker build:compose --mode developer --sync-engine native --with-xdebug`<!--MCLOUD-4098--> |

- ![修正アイコン](../../assets/fix.svg) mutagen ファイル同期の設定を修正して、mutagen が古いセッションを作成するのを防ぎました。 [Mathew Beane により Zilker Technology から送信された修正](https://github.com/magento/magento-cloud-docker/pull/127).<!--MCLOUD-6010-->

- ![修正アイコン](../../assets/fix.svg) PHP-FPM コンテナの起動時に Docker の作成ログに構文エラーが記録される設定の問題を修正しました。 [Mathew Beane により Zilker Technology から送信された修正](https://github.com/magento/magento-cloud-docker/pull/129)<!--MCLOUD-3958-->

- ![修正アイコン](../../assets/fix.svg) 複数の Docker 環境を使用する際に発生することがあったボリューム競合エラーを修正しました。 [Zilker Technology の G Arvind による修正](https://github.com/magento/magento-cloud-docker/pull/168).

- ![修正アイコン](../../assets/fix.svg) の原因となった問題を修正しました `ece-docker build:compose` Blackfireに configuration.io が含まれている場合に失敗するコマンド。 [Zilker Technology の G Arvind による修正](https://github.com/magento/magento-cloud-docker/pull/199). <!--MCLOUD-5797-->

- ![修正アイコン](../../assets/fix.svg) Cloud Docker for Commerceを使用して複数のパッケージをインストールする際に発生するメモリ不足エラーを防ぐために、PHP CLI イメージ設定を更新しました。 [Zilker Technology の Mohan Elamurugan による修正](https://github.com/magento/magento-cloud-docker/pull/197).*<!--MCLOUD-5818-->

- ![修正アイコン](../../assets/fix.svg) Cloud Docker 環境で複数の MySQL ユーザーをサポートするようになりました。 以前のリリースでは、 `build:compose` 次の場合、操作は失敗しました： `magento.app.yaml` ファイルは複数のデータベース ユーザーを指定しました。 [Zilker Technology の G Arvind による修正](https://github.com/magento/magento-cloud-docker/pull/181).<!--MCLOUD-5670-->

- ![修正アイコン](../../assets/fix.svg) 削除済み `rsyslog` Cloud Docker for Commerceの PHP コンテナから、デプロイ中に警告が表示される原因となった互換性の問題を解決します。 Cloud Docker は rsyslog ユーティリティを使用しません。<!--MCLOUD-6173-->

## v1.0.0

リリース日：2020 年 2 月 5 日（PT）

- ![新規アイコン](../../assets/new.svg) **配信用に別のパッケージを作成しました`Cloud Docker for Commerce`**—Commerce用の Cloud Docker を配信するソースコードを `ece-tools` リポジトリ [新規 `magento-cloud-docker` リポジトリ](https://github.com/magento/magento-cloud-docker) コード品質を維持し、独立したリリースを提供する。 新しいパッケージは ECE-Tools v2002.1.0 以降の依存関係です。

  ece-tools を更新すると、 `magento/magento-cloud-docker` パッケージをバージョン 1.0.0 に追加します。以前と共にCommerce用に Cloud Docker を使用した場合 `ece-tools` リリース（2002.0.x）、 [後方非互換性](backward-incompatible-changes.md) 必要に応じて、スクリプト、コマンド、プロセスとしてプロジェクトを更新します。

- ![新規アイコン](../../assets/new.svg) **Docker イメージへのバージョン管理の追加** – ここで、を更新する必要があります `magento/magento-cloud-docker` 更新された画像を取得するためのパッケージ。<!--MAGECLOUD-4737-->

- ![新規アイコン](../../assets/new.svg) **コンテナの更新**—

   - ![新規アイコン](../../assets/new.svg) **PHP-FPM コンテナ**—

      - ![新規アイコン](../../assets/new.svg) **Node.js のサポートを追加**—PHP コンテナ内の node、npm、grunt-cli 機能をサポートするように PHP-FPM イメージを更新しました。<!--MAGECLOUD-3953-->

      - ![新規アイコン](../../assets/new.svg) **をサポートするようになりました [ionCube](https://www.ioncube.com/)**- ローカル Docker 開発環境で ionCube をサポートするように、デフォルトの Docker 設定を更新しました。<!--MAGECLOUD-4354-->

   - ![新規アイコン](../../assets/new.svg) **Web コンテナ**—

      - ![新規アイコン](../../assets/new.svg) **NGINX 設定のカスタマイズ**— カスタムをマウントする機能を追加しました。 `nginx.conf` ファイルを Cloud Docker for Commerce環境に送信します。 参照： [Web コンテナ](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#web-container).<!--MAGECLOUD-4204-->

      - ![新規アイコン](../../assets/new.svg) **自動生成された NGINX 証明書**- Docker 設定ファイルに、Web コンテナ用の NGINX 証明書を自動生成するための設定が含まれるようになりました。<!--MAGECLOUD-4258-->

   - ![新規アイコン](../../assets/new.svg) **新規 Selenium コンテナ** – が追加されました [Selenium コンテナ](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#selenium-container) Magento機能テストフレームワーク（MFTF）を使用したAdobe Commerce アプリケーションテストをサポートします。<!--MAGECLOUD-4040-->

   - ![新規アイコン](../../assets/new.svg) **[!DNL RabbitMQ]バージョンのサポート** – さんが、 [!DNL RabbitMQ] サポートするコンテナ設定 [!DNL RabbitMQ] バージョン 3.8。<!--MAGECLOUD-4674-->

   - ![修正アイコン](../../assets/fix.svg) **永続的なデータベースコンテナ** – この `magento-db: /var/lib/mysql` docker 設定を停止して削除した後、データベースのボリュームが保持されるようになり、Docker 設定を再起動すると復元されるようになりました。 ここで、データベース・ボリュームを手動で削除する必要があります。 参照： [データベースコンテナ].<!--MAGECLOUD-3978-->

   - ![新規アイコン](../../assets/new.svg) **TLS コンテナ**—

      - ![新規アイコン](../../assets/new.svg) **公式画像を使用するようにコンテナベース画像を更新しました** – この [クラウド TLS コンテナ](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#tls-container) 画像は現在、公式に基づいています `debian:jessie` Docker イメージ。—<!--MAGECLOUD-4163-->

      - ![新規アイコン](../../assets/new.svg) **をサポートするようになりました [ポンド TLS 終端プロキシ]** – この [ポンド設定ファイル](https://github.com/magento/magento-cloud-docker/blob/1.0/images/tls/) 以下の環境変数を追加して、TLS コンテナ用の Docker 設定をカスタマイズします。

         - **`TimeOut`**—Time to First Byte （TTFB） タイムアウト値を設定する。 デフォルト値は 300 秒です。

         - **`RewriteLocation`**- Pound プロキシがデフォルトで場所をリクエスト URL に書き換えるかどうかを決定します。 デフォルトは `0` 書き換えが外部の SSO サイトなどの外部 web サイトへのリダイレクトを中断するのを防ぐ。 [Sorin Sugar によって提出された修正](https://github.com/magento/magento-cloud-docker/pull/37)<!--MAGECLOUD-4061-->

      - ![新規アイコン](../../assets/new.svg) TLS コンテナ設定のタイムアウト値を 15 秒から 300 秒に増加しました。 [Mathew Beane により Zilker Technology から送信された修正](https://github.com/magento/magento-cloud-docker/pull/78)<!--MAGECLOUD-4460-->

   - ![新規アイコン](../../assets/new.svg) **ワニス容器**—

      - ![新規アイコン](../../assets/new.svg) **公式画像を使用するようにコンテナベース画像を更新しました** – この [Cloud Varnish コンテナ](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#varnish-container) は現在、公式に基づいています `centos` Docker イメージ。<!--MAGECLOUD-4163-->

      - ![新規アイコン](../../assets/new.svg) **デフォルトのタイムアウト設定の改善** – 追加 `.first_byte_timeout` および `.between_bytes_timeout` ニス コンテナへの設定。 両方のタイムアウト値のデフォルトはです。 `300s` （5 分）。 [Mathew Beane により Zilker Technology から送信された修正](https://github.com/magento/magento-cloud-docker/pull/78)<!--MAGECLOUD-4460-->

      - ![修正アイコン](../../assets/fix.svg) **Xdebug セッション中にワニスをスキップする** – 返されるように Varnish コンテナ設定を更新しました。 `pass` Xdebug が有効化されたときに受信したリクエストに対して実行されます。 以前のリリースでは、Docker 環境に Varnish が含まれている場合、Xdebug を使用できませんでした。 [Mathew Beane により Zilker Technology から送信された修正](https://github.com/magento/magento-cloud-docker/pull/111).<!--MAGECLOUD-4873-->

- ![新規アイコン](../../assets/new.svg) **Docker 設定の変更**—

   - ![新規アイコン](../../assets/new.svg) **プロジェクトのマウントとボリュームの管理**- Docker 環境を起動してローカル開発する際に、マウントとボリュームを管理する機能が追加されました。 参照： [プロジェクトデータの共有].<!--MAGECLOUD-3248-->

   - ![新規アイコン](../../assets/new.svg) **ネットワークブリッジモードのサポート**- ローカルネットワーク上の Docker コンテナ間の接続を有効にするネットワークブリッジモードのサポートを追加しました。<!--MAGECLOUD-4165-->

   - ![新規アイコン](../../assets/new.svg) **Cron コンテナはデフォルトで無効**- パフォーマンスを向上させるために、Docker 環境のビルド時に、Cron コンテナはデフォルトでは設定されなくなりました。 を使用できます `--with-cron` Docker ビルドコマンドの「」オプションを使用して、Cron コンテナを環境に追加する。 参照： [Cron ジョブの管理](https://devdocs.magento.com/cloud/docker/docker-manage-cron-jobs.html).<!--MAGECLOUD-5181-->

   - ![新規アイコン](../../assets/new.svg) **大きなバックアップ ファイルの同期を停止する**- DB ダンプとアーカイブファイル（ZIP、SQL、GZ、BZ2）をに追加しました。 `dist/docker-sync.yml` および `dist/mutagen.sh` ファイル。 大きなファイル（1 GB 超）を同期すると、無操作状態が続く可能性があり、バックアップファイルは再生成できるので、通常は同期を必要としません。<!--MAGECLOUD-3979-->

- ![新規アイコン](../../assets/new.svg) **コマンドの変更**—

   - ![修正アイコン](../../assets/fix.svg) の名前を変更 `./bin/docker` ファイル先 `./bin/magento-docker` 以下の理由で、一部の Docker 環境が壊れる問題を修正します `./bin/docker` ファイルを選択すると、既存の Docker バイナリファイルが上書きされます。 これは [後方互換性のない変化](backward-incompatible-changes.md) それには、スクリプトとコマンドの更新が必要です。<!-- MAGECLOUD-4038 -->

   - ![新規アイコン](../../assets/new.svg) **データベースポートをホストに公開するためのサービス設定オプションが追加されました** – を使用する `--expose-db-port= [Fix submitted by Adarsh Manickam from Zilker Technology](https://github.com/magento/magento-cloud-docker/pull/101).<PORT>` を構築する際にデータベースポートをホストに公開するオプション `docker-compose.yml` ファイル： `bin/ece-docker build:compose --expose-db-port=<PORT>`<!--MAGECLOUD-4454-->

   - ![新規アイコン](../../assets/new.svg) **新しいポストデプロイコマンド** – 以前は、で定義されたデプロイ後のフック `.magento.app.yaml` ファイルは、を使用してAdobe Commerceを Cloud Docker コンテナにデプロイした後、自動的に実行されました `cloud-deploy` コマンド。 今、あなたは別のものを発行する必要があります `cloud-post-deploy` デプロイ後にデプロイ後のフックを実行するコマンド。 の更新されたローンチ手順を参照してください [開発者](https://devdocs.magento.com/cloud/docker/docker-mode-developer.html) および [実稼動](https://devdocs.magento.com/cloud/docker/docker-mode-production.html) モード。<!--MAGECLOUD-3996-->

   - ![新規アイコン](../../assets/new.svg) さんがを追加しました `--rm` 対するオプション `./bin/magento-docker` ビルドおよびデプロイ コンテナ用のコマンド。 これにより、タスクが完了した後にコンテナが削除されます。<!--MAGECLOUD-4205-->

   - ![新規アイコン](../../assets/new.svg) **更新先 `build:compose` コマンド**—

      - ![新規アイコン](../../assets/new.svg) さんがを追加しました `--sync-engine="native"` のオプション `docker-build` 開発者モードで Docker Compose 設定ファイルを生成する際に、ファイル同期を無効にするコマンド。 このオプションは、ローカル Docker 開発用のファイル同期を必要としない Linux システムで開発する場合に使用します。 参照： [Docker 環境でのデータの同期](https://devdocs.magento.com/cloud/docker/docker-syncing-data.html).<!--MCLOUD-3231, MCLOUD-3890-->

   - ![新規アイコン](../../assets/new.svg) デフォルトのファイル同期設定をから変更しました `docker-sync` 対象： `native`. [Mathew Beane により Zilker Technology から送信された修正](https://github.com/magento/magento-cloud-docker/pull/124).<!--MAGECLOUD-5066-->

- ![新規アイコン](../../assets/new.svg) **検証の改善**—

   - ![新規アイコン](../../assets/new.svg) ローカル Docker 開発環境のデプロイメントプロセスに、クラウド環境設定にデータベースの復号化に必要な暗号化キーが含まれていることを検証する検証を追加しました。 これで、環境設定で暗号化キーの値が指定されていない場合、ログにエラーメッセージが表示されるようになりました。<!--MAGECLOUD-4423-->

   - ![新規アイコン](../../assets/new.svg) ビルドおよびデプロイ処理を続行する前にサービスの準備が整っていることを確認するためのコンテナヘルスチェックがElasticsearchサービスに追加されました。 ヘルスチェックがエラーを返した場合、コンテナは自動的に再起動します。<!--MAGECLOUD-4456-->
