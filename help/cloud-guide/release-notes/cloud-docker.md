---
title: Cloud Docker パッケージ
description: Cloud Docker パッケージの最新の改善点の一覧を参照してください。
feature: Cloud, Docker, Release Notes
recommendations: noDisplay, catalog
last-substantial-update: 2023-07-31T00:00:00Z
exl-id: 907d977f-2e9c-4553-a46b-000bc6a57b28
source-git-commit: 21754f2ee3df586cd03d57210741b36409ad2b36
workflow-type: tm+mt
source-wordcount: '3620'
ht-degree: 0%

---

# Cloud Docker パッケージ

The [`magento/magento-cloud-docker`](https://github.com/magento/magento-cloud-docker) パッケージには、Adobe Commerceをローカルのクラウド環境にデプロイするための機能と Docker イメージが用意されています。 これらのリリースノートでは、このパッケージの最新の改善点を説明します。改善点は、のコンポーネントです。 [Cloud Tools Suite for Commerce](cloud-tools-suite.md).

The `magento/magento-cloud-docker` パッケージでは、次のバージョンシーケンスが使用されます。 `<major>.<minor>.<patch>`

リリースノートには次の内容が含まれます。

- ![新しいアイコン](../../assets/new.svg) 新機能
- ![修正アイコン](../../assets/fix.svg) 修正点および改善点

<!--Add release notes below-->

## v1.3.6 {#latest}

リリース日： 2023 年 7 月 31 日

- ![新しいアイコン](../../assets/new.svg) **新しいサービスバージョンを追加しました**—OpenSearch 2.5 を開きます。
- ![新しいアイコン](../../assets/new.svg) **Composer のキャッシュを有効にする** — これで、Docker 設定を拡張して、Docker コンテナの開始時に Composer のキャッシュをクリアできるようになりました。 詳しくは、 [Docker 設定の拡張](https://developer.adobe.com/commerce/cloud-tools/docker/configure/extend-docker-configuration/) （内） _Cloud Docker for Commerce_ ガイド。

## v1.3.5

リリース日： 2023 年 3 月 11 日

- ![新しいアイコン](../../assets/new.svg) **ionCube**—PHP 8.1 イメージ用に ionCube 拡張を追加しました。
- ![新しいアイコン](../../assets/new.svg) **新しいサービスバージョンを追加しました。**—OpenSearch 2.3 と 2.4、PHP 8.2、Vanrish 7.1.1。
- ![新しいアイコン](../../assets/new.svg) **PHP 8.2 のサポートの強化** — 特定の PHP 8.2.x バージョンで Commerce 2.4.6 をサポートするための互換性の問題を修正しました。
- ![修正アイコン](../../assets/fix.svg) **コンポーザーの問題**—Docker コンテナ内で Composer のバージョンを更新した後に発生していた問題を修正しました。

## v1.3.4

リリース日： 2022 年 10 月 27 日

- ![新しいアイコン](../../assets/new.svg) **新しい Vanish イメージを追加しました。**- Vanish 6.5、7.0 および 7.1 用の画像を追加しました。<!-- MCLOUD-7879 -->

## v1.3.3

リリース日： 2022 年 9 月 14 日

- ![新しいアイコン](../../assets/new.svg) **Apple M1(ARM64) のサポート**— Apple M1(ARM64) アーキテクチャのサポートを有効にするため、Docker イメージに変更が追加されました。<!-- MCLOUD-7989-2 MCLOUD-7989 -->
- ![修正アイコン](../../assets/fix.svg) **Mailhog** — 開発モード中に Mailhog サービスが電子メールを取得しない問題を修正しました。<!-- MCLOUD-8643 -->
- ![修正アイコン](../../assets/fix.svg) **init-docker.sh**— `init-docker.sh` スクリプト。<!-- MCLOUD-8765 -->

## v1.3.2

リリース日： 2022 年 3 月 31 日

- ![新しいアイコン](../../assets/new.svg) **Elasticsearch7.10 の画像を追加しました。**<!-- MCLOUD-8548 -->

## v1.3.1

リリース日： 2022 年 3 月 11 日

- ![新しいアイコン](../../assets/new.svg) **PHP 8.1 をサポート**—PHP 8.1 のサポートを追加しました。
- ![新しいアイコン](../../assets/new.svg) **OpenSearch**—OpenSearch バージョン 1.1 および 1.2 の画像を追加しました。
- ![新しいアイコン](../../assets/new.svg) **Composer 2.1**—PHP 8.x イメージで、デフォルトで composer 2.1.x を設定します。
- ![新しいアイコン](../../assets/new.svg) **PHP イメージの改善**—

   - PHP 8.1 のイメージを追加しました。
   - アップグレードされた xDebug バージョン 3.1.2
   - アップグレードされた xmlrpc 1.0.0RC3

- ![修正アイコン](../../assets/fix.svg) **Elasticsearchと OpenSearch の改善点**-Elasticsearchと OpenSearch Dockerfiles の改善。Elasticsearch5.2 イメージを削除。
- ![修正アイコン](../../assets/fix.svg) **ナトリウム伸展** — を有効にします。 `sodium` デフォルトでは、すべての PHP イメージで拡張されます。
- ![修正アイコン](../../assets/fix.svg) **Composer のキャッシュボリューム**— Composer のキャッシュボリュームが Composer パッケージをキャッシュするためのパスを修正しました。
- ![修正アイコン](../../assets/fix.svg) **nginx でのメモリ制限**- NGINX イメージのメモリ制限を修正しました。

## v1.3.0

リリース日： 2021 年 10 月 26 日

- ![修正アイコン](../../assets/fix.svg) **開発者モードのワークフローを改善** — 以前は、ビルドおよびデプロイ手順でモードを指定する必要がありました。 さて、 `--mode` オプションを `build` step は、後でのモードを決定します。 `deploy` 手順 デプロイメント後にモードを設定する必要はなくなりました。 詳しくは、 [開発者モード](https://devdocs.magento.com/cloud/docker/docker-mode-developer.html).<!-- ACMP-1086 -->
- ![修正アイコン](../../assets/fix.svg) **読み取り専用ファイルシステムの改善**—<!-- ACMP-1106 -->
   - メール設定用の PHP コンテナを起動する際の問題を修正しました。
   - INI ファイルで環境変数を使用できます。
   - PHP のエントリポイントに書き込み権限が必要ないことを確認します。
- ![修正アイコン](../../assets/fix.svg) **ノードを更新** — バンドルされた Node バージョンを更新します。PHP-CLI イメージに Node をインストールする際に、現在の LTS バージョンが使用されるようになりました。<!-- ACMP-1539 -->
- ![修正アイコン](../../assets/fix.svg) **Symfony の更新**—Symfony 設定の依存関係を更新し、Adobe Commerce 2.4.4 との互換性を持たせました。<!-- ACMP-1533 -->

## v1.2.4

リリース日： 2021 年 7 月 30 日

- ![新しいアイコン](../../assets/new.svg) **新規 `Zookeeper` コンテナ** — が追加されました。 [Zookeeper コンテナ](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#zookeeper-container) クラウドインフラストラクチャ上のAdobe Commerceにデプロイされていないプロジェクトのロックプロバイダー設定を管理する。<!--MCLOUD-8000-->

- ![新しいアイコン](../../assets/new.svg) **Composer 2.0 のサポートを追加しました。**— Composer 1.0 からのアップグレードをサポートするために、Composer のバージョン 2.0 が Composer の設定ファイルに追加されました。アップグレードは提供終了に近づいています。<!--MCLOUD-8003-->

## v1.2.3

リリース日： 2021 年 6 月 14 日

- ![新しいアイコン](../../assets/new.svg) **PHP 8.0 を追加しました。**—PHP をバージョン 8.0 に更新し、PHP 8.0 に含まれるすべての新機能と最適化を利用できるようにしました。<!--MCLOUD-7941-->
- ![新しいアイコン](../../assets/new.svg) **Vanish 6.6 およびElasticsearch7.11.2に更新** — 次のリンクは、 [ワニスキャッシュ 6.6](https://varnish-cache.org/releases/rel6.6.0.html#rel6-6-0) Elasticsearch7.11.2。<!--MCLOUD-7921-->
- ![新しいアイコン](../../assets/new.svg) **追加済み `ioncube` PHP 7.4 イメージの拡張**- `ioncube` PHP 7.3 から PHP 7.4 へのアップグレードから除外された後、拡張は PHP 7.4 イメージに再び追加されました。 *[Mattskr によって送信](https://github.com/magento/magento-cloud-docker/pull/314).*<!--PR #314-->
- ![新しいアイコン](../../assets/new.svg) **ファイル同期オプションが追加されました。`manual-native`**- `manual-native` ファイル同期オプションを使用すると、同期を手動で制御でき、macOSおよび Windows 環境に最適なパフォーマンスを提供します。 の使用に関するお読みください `manual-native` オプション [開発者モード](https://devdocs.magento.com/cloud/docker/docker-mode-developer.html) および [Docker 開発者環境でのデータの同期](https://devdocs.magento.com/cloud/docker/docker-syncing-data.html#file-synchronization-options).<!--MCLOUD-7977-->
- ![新しいアイコン](../../assets/new.svg) **ボリュームの削除を削除しました。 `up` および `down` コマンド**- `--volume` オプションが `bin/magento-docker up` および `bin/magento-docker down` コマンド、新しい `bin/magento-docker init` コマンドを使用して、データ損失の警告を表示します。 この変更により、誤ったデータ損失を防ぐことができます。 *[Joeshelton-wagento 提出](https://github.com/magento/magento-cloud-docker/pull/319).*<!--PR #319-->
- ![修正アイコン](../../assets/fix.svg) **更新済み `CN` 生成された証明書の値** — ハードコードされたを削除しました。 `CN` の値を Dockerfile から取得します。 この値により、証明書エラー (`NET::ERR_CERT_INVALID`) が原因で `--host` オプション `ece-docker build:compose` コマンドを無視する必要があります。<!--MCLOUD-7934-->

## v1.2.2

リリース日： 2021 年 4 月 21 日

- ![新しいアイコン](../../assets/new.svg) **更新済み `host.docker.internal` プラットフォームに依存しない**— Ubuntu、Windows、macOS用に同じ Docker Compose スクリプトを作成できるようになりました。 Ubuntu で Xdebug を使用する場合、別の環境変数を使用する必要がなくなりました。 [Igor Vitol が提出した修正](https://github.com/magento/magento-cloud-docker/pull/299).<!--Issue #298-->
- ![新しいアイコン](../../assets/new.svg) **init-docker.sh を更新しました** — が追加されました。 `mounts` オブジェクトを `MAGENTO_CLOUD_APPLICATION` 環境変数。 [Chiranjeevi が提出したフィックス](https://github.com/magento/magento-cloud-docker/pull/299).<!--Issue #299-->
- ![新しいアイコン](../../assets/new.svg) **init-docker.sh を更新しました** — を更新しました。 `init-docker.sh` スクリプトを PHP 7.4 および Cloud Docker 1.2.1 バージョンで使用する場合。 [Adarsh Manickam によって送信された修正](https://github.com/magento/magento-cloud-docker/pull/300).<!--Issue #300-->
- ![新しいアイコン](../../assets/new.svg) **ナトリウムはデフォルトで有効** — を有効にします。 `sodium` PHP Docker イメージ内のデフォルトの PHP 拡張。<!--MCLOUD-7548-->
- ![新しいアイコン](../../assets/new.svg) **`custom-registry`オプション** — が追加されました。 `--custom-registry` 選択肢 `php ./vendor/bin/ece-docker build:compose` コマンドを使用して、独自のイメージレジストリを使用できます。<!--MCLOUD-7476-->

  ```bash
  ./vendor/bin/ece-docker build:compose --custom-registry=my-registry.example.com
  ```

- ![新しいアイコン](../../assets/new.svg) **古いバージョンのElasticsearchを削除**—Elasticsearch画像からElasticsearchバージョン 1.7 および 2.4 を削除しました。<!--MCLOUD-7504-->
- ![新しいアイコン](../../assets/new.svg) **NGINX 証明書の自動生成**- NGINX イメージから既存の証明書を削除しました。 セキュリティを強化するために、新しいデプロイメントのたびに NGINX 証明書が自動生成されるようになりました。<!--MCLOUD-7396-->
- ![修正アイコン](../../assets/fix.svg) **有効`opcache.validate_timestamps`** — を有効にします。 `opcache.validate_timestamps` デベロッパーモードでは、デフォルトで PHP 設定が使用されます。 この設定を有効にすると、Docker でファイルシステムの変更が認識されない問題が修正されました。<!--MCLOUD-7466-->
- ![修正アイコン](../../assets/fix.svg) **固定`build:custom:compose`** — 修正された `build:custom:compose` コマンドを使用して、ビルドプロセス中にファイルを上書きできない場合にエラーをスローします。 エラーをスローすると、 `docker-compose up` 間違ったファイルを使用している可能性があります。<!--MCLOUD-7457-->
- ![修正アイコン](../../assets/fix.svg) **固定 `--sync_engine="native"` オプション** — 実稼動モード (`--mode="production"`)、 `--sync_engine="native"` 」オプションを選択しても、 `docker.composer.yml` ファイル。<!--MCLOUD-7254-->
- ![修正アイコン](../../assets/fix.svg) **サービスバージョンの検証エラーを修正しました** — のサービスバージョンを追加しました。 [!DNL RabbitMQ]、Elasticsearch、その他のサービス `type` プロパティを `MAGENTO_CLOUD_RELATIONSHIP` 変数を使用します。 次のバージョンを `relationships` 変数は、デプロイフェーズで発生した検証エラーを修正しました。<!--MCLOUD-7572-->

## v1.2.1

リリース日： 2020 年 12 月 22 日

- ![新しいアイコン](../../assets/new.svg) **NGINX コマンドオプション**— NGINX の数を変更するためのビルドコマンドオプションを追加しました。 `worker_processes` そして NGINX `worker_connections` TLS および Web サービス用。 The `worker_process` パラメーターは、値を `auto`. 例： <!--MCLOUD-7259-->

  ```terminal
  ./vendor/bin/ece-docker build:compose --nginx-worker-processes=2
  ./vendor/bin/ece-docker build:compose --nginx-worker-connections=2048
  ```

- ![新しいアイコン](../../assets/new.svg) **TLS コマンドオプション**—TLS サービスを使用せずに設定を作成するためのビルドコマンドオプションを追加しました。 例： <!--MCLOUD-7259-->

  ```terminal
  ./vendor/bin/ece-docker build:compose --no-tls
  ```

- ![新しいアイコン](../../assets/new.svg) **NGINX メモリ消費量**- TLS および Web サービスの NGINX プロセスで消費されるメモリを削減しました。<!--MCLOUD-7259-->

- ![新しいアイコン](../../assets/new.svg) **Blackfire**—Cloud Docker イメージでBlackfirePHP 拡張をデフォルトで無効にしました。

- ![修正アイコン](../../assets/fix.svg) **PHP-FPM コンテナ**—PHP-FPM コンテナのヘルスチェックを修正し、 `WEB_PORT` から `80` から `8080`.<!--MCLOUD-7232-->

- ![修正アイコン](../../assets/fix.svg) **無効なボリュームの命名** — デベロッパーモードで無効なボリューム命名が発生するエラーを修正しました。<!--MCLOUD-7442-->

- ![修正アイコン](../../assets/fix.svg) **NGINX アップストリームポート**—Docker NGINX 1.19 イメージを更新し、無限ループを回避するために、ポート 8080 を使用するようにしました。 [Adarsh Manickam によって送信された修正](https://github.com/magento/magento-cloud-docker/pull/296).<!--Issue 295-->

## v1.2.0

リリース日： 2020 年 11 月 10 日

- ![新しいアイコン](../../assets/new.svg) **コンテナの更新 —**

   - ![新しいアイコン](../../assets/new.svg) **PHP-FPM コンテナ**—gnupg PHP 拡張機能のサポートを追加しました。 [Zilker Technology から G Arvind が提出した修正](https://github.com/magento/magento-cloud-docker/pull/210).<!--MCLOUD-5981-->

   - ![修正アイコン](../../assets/fix.svg) **データベースコンテナ** — 必要なデータベースパスワードをヘルスチェックコマンドに追加して、データベースコンテナのヘルスチェックを修正しました。<!--MCLOUD-7122-->

   - ![新しいアイコン](../../assets/new.svg) **Elasticsearchコンテナ**

      - Elasticsearch7.9 のサポートを追加し、今後のAdobe Commerceリリースとの互換性を確保しました。<!--MCLOUD-7190-->

      - **Elasticsearchプラグイン設定**— `services.yaml` 生成するファイル `docker-compose.yaml` Cloud Docker for Commerce 環境用のファイル。 詳しくは、 [Elasticsearchプラグイン](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#elasticsearch-plugins).<!--MCLOUD-2789-->

      - **Elasticsearchプラグインのサポート** — 次のプラグインのサポートがElasticsearchされました。 `analysis-icu`, `analysis-phonetic`, `analysis-stempel`、および `analysis-nori`. The `analysis-icu` および `analysis-phonetic` プラグインはデフォルトでインストールされます。 次の項目を追加または削除できます： `analysis-stempel` および `analysis-nori` 必要に応じてプラグインを使用します。<!--MCLOUD-2789-->

   - ![新しいアイコン](../../assets/new.svg) **CLI コンテナ**

      - **Docker PHP コンテナ内でのコマンドの実行**—Cloud Docker CLI を使用すると、ホストに PHP をインストールしなくても、Docker 環境の PHP コンテナ内でコマンドを実行できます。 例えば、次のコマンドを実行すると、設定が構築されます。  `./bin/magento-docker php 7.3 vendor/bin/ece-docker build:compose`. 詳しくは、 [Cloud Docker CLI](https://devdocs.magento.com/cloud/docker/docker-quick-reference.html#magento-cloud-docker-cli). [Zilker Technology から G Arvind が提出した修正](https://github.com/magento/magento-cloud-docker/pull/209).<!--MCLOUD-5982-->

      - OpenSSH-client を PHP CLI コンテナに追加しました。 現在は、Composer に ssh-agent 転送を使用できます ( `composer.json` ファイルには、Composer コマンドを使用するために ssh クライアントが必要なプライベート git リポジトリが含まれています。<!--MCLOUD-6008-->

   - ![修正アイコン](../../assets/fix.svg) **TLS コンテナ** — さて、 [TLS コンテナ](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#tls-container) は、 `https://hub.docker.com/r/magento/magento-cloud-docker-nginx` Docker 画像を使用することをお勧めします。 この変更により、Cloud Docker 環境のコンテナ間で HTTPS リクエストを送信する際にエラーが発生する問題が修正されました。<!--MCLOUD-6469-->

   - ![新しいアイコン](../../assets/new.svg) **コンテナをテスト** — アプリケーションテスト用のテストコンテナを追加し、 `--with-test` Docker のオプション `build:compose` コマンドを使用して、Docker 環境でテストする場合にのみコンテナを作成できます。 詳しくは、 [アプリケーションテスト](https://devdocs.magento.com/cloud/docker/docker-test-app-mftf.html).<!--MCLOUD-6394-->

   - ![新しいアイコン](../../assets/new.svg) **FPM-XDEBUG コンテナ**

      - ![新しいアイコン](../../assets/new.svg) **Linux での Xdebug の設定** — が追加されました。 `--set-docker-host` オプションを `ece-docker build:compose` コマンドを使用して `host.docker.internal` の値を Xdebug コンテナに含める必要があります。 このオプションは、Linux システムで Xdebug を使用する場合に必要です。 詳しくは、 [Docker 用の Xdebug の設定](https://devdocs.magento.com/cloud/docker/docker-development-debug.html).<!--MCLOUD-6430-->

      - ![修正アイコン](../../assets/fix.svg) 解決する Docker ENTRYPOINT の Xdebug 変数設定を修正しました。 `uninitialized "with_xdebug" variable` エラーがログに表示されます。 [Fix submitted by Florent Olivaud](https://github.com/magento/magento-cloud-docker/pull/218)<!--MCLOUD-6043-->

- ![新しいアイコン](../../assets/new.svg) **Docker 設定の変更**

   - **MailHog 設定** — 次を使用できます。 `ece-docker build:compose` MailHog を無効にし、ポートを指定するコマンドオプション： `--no-mailhog`, `--mailhog-http-port`、および `--mailhog-smtp-port`. 詳しくは、 [電子メールの設定](https://devdocs.magento.com/cloud/docker/docker-config.html#set-up-email).<!--MCLOUD-6898, MCLOUD-6660-->

   - Cloud Docker for Commerce 1.2.0 以降では、Adobeが各パッチバージョンの Docker イメージを提供するようになり、Docker 設定ジェネレーターは、最新を使用する代わりに、指定したパッチバージョンを使用して Docker 設定を作成します。 以前は、Docker 設定ジェネレーターは、最新のパッチバージョンを使用して設定を構築していました。これにより、以前のバージョンを使用して構築された Cloud Docker for Commerce 環境を壊す可能性がありました。<!--MCLOUD-7093-->

   - **カスタム Cloud Docker 設定でのカスタム画像とバージョンの指定** — を更新しました。 `build:custom:compose` コマンドを使用して、カスタム Docker 構成ファイル (`docker-compose.yaml`) をクリックします。 詳しくは、 [カスタム Docker Compose 設定の作成](https://devdocs.magento.com/cloud/docker/docker-config-sources.html#build-a-custom-docker-compose-configuration). <!--MCLOUD-7089-->

   - Docker ホスト設定を更新し、ポート 443 を公開して、Adobe Commerce (`https://magento2.docker`) をすべての CLI コンテナから削除します。 デフォルトのポートは、 `--tls-port` 」オプションを使用します。<!--MCLOUD-6806-->

- ![修正アイコン](../../assets/fix.svg) Cloud Docker for Commerce のビルドが失敗する原因となっていた問題を修正しました。 `app/etc/env.php` ファイルが存在します。<!--MCLOUD-6732-->

- ![修正アイコン](../../assets/fix.svg) ビルド設定が更新され、Cloud Docker for Commerce を Linux または Windows Subsystem for Linux(WSL2) にデプロイする際に問題が発生しないように、名前付きボリュームが通常のボリュームに置き換えられました。<!--MCLOUD-6732-->

- ![修正アイコン](../../assets/fix.svg) Composer 2.0 をサポートするように、Cloud Docker for Commerce 機能テストを更新しました。<!--MCLOUD-7183-->

## v1.1.2

リリース日： 2020 年 9 月 10 日

- ![新しいアイコン](../../assets/new.svg) Elasticsearch7.7 のサポートを追加しました。<!--MCLOUD-6219-->

## v1.1.1

リリース日： 2020 年 8 月 6 日

- ![修正アイコン](../../assets/fix.svg) **更新された電子メール設定**— SendMail を使用する代わりに MailHog サービスをサポートするように、Cloud Docker for Commerce のデフォルト設定を更新しました。 詳しくは、 [電子メールの設定](https://devdocs.magento.com/cloud/docker/docker-config.html#set-up-email).<!--MCLOUD-5624-->

- ![修正アイコン](../../assets/fix.svg) PS ライブラリを Cloud Docker 環境設定に復元し、修正しました。 `ps:  command not found` エラー。<!--MCLOUD-6621-->

- ![修正アイコン](../../assets/fix.svg) Cloud Docker for Commerce のデフォルト設定を更新し、データベースエントリポイントと MariaDB ボリュームの自動マウントを削除して修正しました。 `Cannot create container for service db` Cloud Docker 環境の開始時に発生する可能性のあるエラーです。

  次のオプションを `ece-docker build:compose` コマンド： `--with-entry-point` および `with-mariadb-conf`. 詳しくは、 [サービス設定オプション](https://devdocs.magento.com/cloud/docker/docker-containers.html#service-configuration-options).<!--MCLOUD-6424-->

- ![新しいアイコン](../../assets/new.svg) **CLI コマンドの更新**

| アクション | コマンド |
| ------------------------------------------------------------------------------- | -------------------------------------------------------------- |
| データベースコンテナにエントリポイントを追加して、バックアップからデータベースを復元します | `./vendor/bin/ece-docker build:compose --db --with-entrypoint` |
| MariaDB 設定ボリュームを追加する | `./vendor/bin/ece-docker build:compose --db --mariadb-conf` |

## v1.1.0

リリース日： 2020 年 6 月 26 日

- ![新しいアイコン](../../assets/new.svg) **分割データベースのパフォーマンスソリューションのサポートを追加しました。** — これで、Cloud Docker 環境の分割データベースパフォーマンスソリューションを使用して、ストアを設定およびデプロイできます。<!--MCLOUD-3740-->

- ![新しいアイコン](../../assets/new.svg) **Adobe CommerceとMagento Open Sourceのデプロイメントのサポート**— Cloud Docker for Commerce を使用して、クラウドインフラストラクチャ上でAdobe Commerceにホストされていないプロジェクトのローカル開発環境をデプロイできるようになりました。<!--MCLOUD-5667-->

- ![新しいアイコン](../../assets/new.svg) **Blackfire.io のサポート**— [Blackfire.io 拡張](https://devdocs.magento.com/cloud/docker/docker-config-blackfire-io.html) 自動パフォーマンステスト用。 [Zilker Technology から Adarsh Manickam によって送信された修正](https://github.com/magento/magento-cloud-docker/pull/202)<!--MCLOUD-5857-->

- ![新しいアイコン](../../assets/new.svg) **コンテナの更新**

   - **ワニス**—Vanrish は、サポートされているバージョンの Cloud アプリケーションテンプレートを使用して Cloud Docker 環境にAdobe Commerceをデプロイする場合のデフォルトのキャッシュになりました。 詳しくは、 [ワニス容器](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#varnish-container).<!--MCLOUD-2634-->

   - 追加された `--no-varnish` Cloud Docker 設定ファイルの生成時に Vanish サービスのインストールをスキップするオプション。<!--MCLOUD-2634-->

   - ![新しいアイコン](../../assets/new.svg) **データベース**

      - MySQL データベースのサポートを追加しました。 これで、MariaDB または MySQL を使用して Cloud Docker 環境を設定できます。 詳しくは、 [サービス設定オプション](https://devdocs.magento.com/cloud/docker/docker-containers.html#service-configuration-options).<!--MCLOUD-5691-->

      - Docker 構成ファイルの生成時に、データベースレプリケーションの増分とオフセットの設定をおこなう機能が追加されました。 詳しくは、 [サービスコンテナ](https://devdocs.magento.com/cloud/docker/docker-containers.html#service-containers).<!--MCLOUD-5735-->

   - ![新しいアイコン](../../assets/new.svg) **PHP-FPM**

      - PHP 7.4 のサポートを追加しました。 [Zilker Technology の Mohanela Murugan が提出した Fix](https://github.com/magento/magento-cloud-docker/pull/198)<!--MCLOUD-198-->

      - コピー機能が追加されました。 `php.ini` ファイルを Cloud Docker 環境のルートプロジェクトディレクトリに保存し、PHP-FPM コンテナと CLI コンテナにカスタム PHP 設定を適用します。 詳しくは、 [PHP 設定のカスタマイズ](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#customize-php-settings). [Zilker Technology の Mathew Beane が提出した Fix](https://github.com/magento/magento-cloud-docker/pull/130).<!--MCLOUD-6012-->

      - コンテナのヘルスチェックを追加しました。 [Zilker Technology から Visanth Sampath が提出した Fix](https://github.com/magento/magento-cloud-docker/pull/188).<!--MCLOUD-5752-->

   - ![修正アイコン](../../assets/fix.svg) **Node.js** — セキュリティを強化するため、デフォルトの Node.js バージョンをバージョン 8 からバージョン 10 に更新しました。 Node.js バージョン 8 は非推奨となり、バグ修正やセキュリティパッチは適用されなくなりました。 [Zilker Technology から Mohan Elamurugan が提出した Fix](https://github.com/magento/magento-cloud-docker/pull/183).<!--MCLOUD-5586-->

   - ![新しいアイコン](../../assets/new.svg) **Elasticsearch**

      - Elasticsearch6.8、7.2、7.5 および 7.6 のサポートを追加しました。<!--MCLOUD-4050, MCLOUD-5855,MCLOUD-5860-->

      - をカスタマイズする機能が追加されました。 [Elasticsearchコンテナの設定](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#elasticsearch-container) Docker 作成設定ファイルを生成する際に使用します。<!--MCLOUD-3059-->

      - 追加された `--no-es` オプションを使用して、Docker Compose 設定ファイルを生成するためのサービス設定オプションを指定できます。 このオプションを使用して、Elasticsearchコンテナのインストールをスキップし、代わりに MySQL 検索を使用します。 このオプションは、Adobe Commerceバージョン 2.3.5 以前でのみサポートされます。<!--MCLOUD-3766-->

   - ![新しいアイコン](../../assets/new.svg) **FPM-XDEBUG コンテナ**—Cloud Docker 環境で PHP をデバッグするための Xdebug をインストールして設定するためのサービス設定オプションが追加されました。 詳しくは、 [Xdebug の設定](https://devdocs.magento.com/cloud/docker/docker-development-debug.html).<!--MCLOUD-4098-->

- ![新しいアイコン](../../assets/new.svg) **Docker 設定の変更**

   - PHP-FPM、Redis、Elasticsearch、MySQL Docker の各サービスコンテナのヘルスチェックを追加しました。<!--MCLOUD-3335 and MCLOUD-5856-->

   - デフォルトのファイル同期モードをに変更しました。 `native` を開発者モードで使用します。<!--MCLOUD-3890 -->

   - を生成する際に、汎用の Docker サービスコンテナ画像にバージョン情報を追加しました。 `docker-compose.yml` ファイル。<!--MCLOUD-3878-->

   - アップストリームの PHP-FPM コンテナから大きな応答を処理する機能を改善し、 `fastcgi_buffers` Nginx サーバの値。<!--MCLOUD-5980-->

   - ファイルを同期する 2 番目の同期セッションを `vendor` ディレクトリ。 この変更は、ファイルの同期プロセス中に変異原が動かなくなるのを防ぎます。 [Zilker Technology の Mathew Beane が提出した Fix](https://github.com/magento/magento-cloud-docker/pull/127).<!--MCLOUD-6010-->

   - ![新しいアイコン](../../assets/new.svg) **CLI コマンドの更新**

| アクション | コマンド |
| -------- | --------------- |
| Redis キャッシュをクリア | `bin/magento-docker flush-redis` |
| Vanrish キャッシュをクリア | `bin/magento-docker flush-varnish` |
| デフォルトのワニスのインストールをスキップ | `.vendor/bin/ece-docker build:compose --no-varnish`<!--MCLOUD-2634--> |
| [カスタマイズElasticsearchオプション](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#elasticsearch-container) | `.vendor/bin/ece-docker build:compose --es-env-var`<!--MCLOUD-3059--> |
| [Elasticsearch設定を削除](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#elasticsearch-container) | `.vendor/bin/ece-docker build:compose --no-es`<!--MCLOUD-3766--> |
| MySQL バージョン 5.6 または 5.7 での DB コンテナの設定 | `./vendor/bin/ece-docker build:compose --db <mysql-version-number> --db-image mysql`<!--MCLOUD-5691--> |
| カスタムベース URL を指定 | `./vendor/bin/ece-docker build:compose --host=<hostname> --port=<port-number>`<!--MCLOUD-3063--> |
| [Xdebug 設定のコンテナを追加します。](https://devdocs.magento.com/cloud/docker/docker-development-debug.html) | `.vendor/bin/ece-docker build:compose --mode developer --sync-engine native --with-xdebug`<!--MCLOUD-4098--> |

- ![修正アイコン](../../assets/fix.svg) 変異原が古いセッションを作成するのを防ぐために、変異原ファイルの同期の設定を修正しました。 [Zilker Technology の Mathew Beane が提出した Fix](https://github.com/magento/magento-cloud-docker/pull/127).<!--MCLOUD-6010-->

- ![修正アイコン](../../assets/fix.svg) PHP-FPM コンテナを起動したときに Docker compose ログに構文エラーが発生する設定の問題を修正しました。 [Zilker Technology の Mathew Beane が提出した Fix](https://github.com/magento/magento-cloud-docker/pull/129)<!--MCLOUD-3958-->

- ![修正アイコン](../../assets/fix.svg) 複数の Docker 環境を使用する際に発生することがあったボリュームの競合エラーを修正しました。 [Zilker Technology から G Arvind が提出した修正](https://github.com/magento/magento-cloud-docker/pull/168).

- ![修正アイコン](../../assets/fix.svg) 次の問題を修正しました： `ece-docker build:compose` 設定にBlackfire.io が含まれている場合に失敗するコマンド [Zilker Technology から G Arvind が提出した修正](https://github.com/magento/magento-cloud-docker/pull/199). <!--MCLOUD-5797-->

- ![修正アイコン](../../assets/fix.svg) PHP CLI のイメージ設定を更新し、Cloud Docker for Commerce を使用して複数のパッケージをインストールする際に発生するメモリ不足エラーを防ぎました。 [Zilker Technology から Mohan Elamurugan が提出した Fix](https://github.com/magento/magento-cloud-docker/pull/197).*<!--MCLOUD-5818-->

- ![修正アイコン](../../assets/fix.svg) Cloud Docker 環境での複数の MySQL ユーザーのサポートが追加されました。 以前のリリースでは、 `build:compose` 操作が失敗した場合、 `magento.app.yaml` ファイルが複数のデータベースユーザーを指定しました。 [Zilker Technology から G Arvind が提出した修正](https://github.com/magento/magento-cloud-docker/pull/181).<!--MCLOUD-5670-->

- ![修正アイコン](../../assets/fix.svg) 削除済み `rsyslog` を Cloud Docker for Commerce PHP コンテナから削除して、デプロイ時に警告通知が発生する互換性の問題を解決します。 Cloud Docker は rsyslog ユーティリティを使用しません。<!--MCLOUD-6173-->

## v1.0.0

リリース日： 2020 年 2 月 6 日

- ![新しいアイコン](../../assets/new.svg) **配信用に別のパッケージを作成しました`Cloud Docker for Commerce`**— Cloud Docker for Commerce を `ece-tools` リポジトリを [新規 `magento-cloud-docker` リポジトリ](https://github.com/magento/magento-cloud-docker) コード品質を維持し、独立したリリースを提供する。 新しいパッケージは、ECE-Tools v2002.1.0 以降の依存関係です。

  ece-tools を更新する場合、 `magento/magento-cloud-docker` バージョン 1.0.0 にパッケージ化します。以前の `ece-tools` リリース (2002.0.x) の場合は、 [後方非互換性](backward-incompatible-changes.md) 必要に応じて、スクリプト、コマンド、プロセスとしてプロジェクトを更新します。

- ![新しいアイコン](../../assets/new.svg) **Docker 画像のバージョン管理を追加しました。** — 次に、 `magento/magento-cloud-docker` 更新された画像を取得するには、パッケージを使用します。<!--MAGECLOUD-4737-->

- ![新しいアイコン](../../assets/new.svg) **コンテナの更新**—

   - ![新しいアイコン](../../assets/new.svg) **PHP-FPM コンテナ**—

      - ![新しいアイコン](../../assets/new.svg) **Node.js サポートを追加しました。**—PHP-FPM イメージを更新し、PHP コンテナ内の node、npm、および grunt-cli 機能をサポートしました。<!--MAGECLOUD-3953-->

      - ![新しいアイコン](../../assets/new.svg) **のサポートを追加しました。 [ionCube](https://www.ioncube.com/)** — ローカルの Docker 開発環境で ionCube をサポートするように、デフォルトの Docker 設定を更新しました。<!--MAGECLOUD-4354-->

   - ![新しいアイコン](../../assets/new.svg) **Web コンテナ**—

      - ![新しいアイコン](../../assets/new.svg) **NGINX 設定のカスタマイズ** — カスタムをマウントする機能を追加しました。 `nginx.conf` ファイルを Cloud Docker for Commerce 環境に保存します。 詳しくは、 [Web コンテナ](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#web-container).<!--MAGECLOUD-4204-->

      - ![新しいアイコン](../../assets/new.svg) **自動生成された NGINX 証明書**—Docker 設定ファイルに、Web コンテナ用の NGINX 証明書を自動生成する設定が含まれるようになりました。<!--MAGECLOUD-4258-->

   - ![新しいアイコン](../../assets/new.svg) **新しい Selenium コンテナ** — が追加されました。 [Selenium コンテナ](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#selenium-container) Adobe Commerce Functional Testing Framework (MFTF) を使用したMagentoテストをサポートする。<!--MAGECLOUD-4040-->

   - ![新しいアイコン](../../assets/new.svg) **[!DNL RabbitMQ]バージョンのサポート** — を更新しました。 [!DNL RabbitMQ] サポートするコンテナ設定 [!DNL RabbitMQ] バージョン 3.8.3.4.1.1.2.1.2.2.1..................................................................................................................................................................................<!--MAGECLOUD-4674-->

   - ![修正アイコン](../../assets/fix.svg) **永続的なデータベースコンテナ**- `magento-db: /var/lib/mysql` Docker 設定を停止して削除した後も、データベースボリュームが保持され、Docker 設定を再起動すると復元されるようになりました。 次に、データベースボリュームを手動で削除する必要があります。 詳しくは、 [データベースコンテナ].<!--MAGECLOUD-3978-->

   - ![新しいアイコン](../../assets/new.svg) **TLS コンテナ**—

      - ![新しいアイコン](../../assets/new.svg) **コンテナのベース画像を更新して、公式の画像を使用しました。**- [クラウド TLS コンテナ](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#tls-container) 画像は、現在、 `debian:jessie` Docker 画像 —<!--MAGECLOUD-4163-->

      - ![新しいアイコン](../../assets/new.svg) **のサポートを追加しました。 [ポンド TLS 終了プロキシ]**- [ポンド設定ファイル](https://github.com/magento/magento-cloud-docker/blob/1.0/images/tls/) では、次の ENV 変数が追加されて、TLS コンテナの Docker 設定をカスタマイズします。

         - **`TimeOut`**- 「Time to First Byte(TTFB)」タイムアウト値を設定します。 デフォルト値は 300 秒です。

         - **`RewriteLocation`**— Pound プロキシが、デフォルトで、場所を要求 URL に書き換えるかどうかを指定します。 デフォルトはです。 `0` 書き換えによって外部の SSO サイトなどの外部 web サイトにリダイレクトが壊れるのを防ぐためです。 [Sorin Sugar が提出した Fix](https://github.com/magento/magento-cloud-docker/pull/37)<!--MAGECLOUD-4061-->

      - ![新しいアイコン](../../assets/new.svg) TLS コンテナ設定のタイムアウト値が 15 秒から 300 秒に増加しました。 [Zilker Technology の Mathew Beane が提出した Fix](https://github.com/magento/magento-cloud-docker/pull/78)<!--MAGECLOUD-4460-->

   - ![新しいアイコン](../../assets/new.svg) **ワニス容器**—

      - ![新しいアイコン](../../assets/new.svg) **コンテナのベース画像を更新して、公式の画像を使用しました。**- [雲ワニス容器](https://devdocs.magento.com/cloud/docker/docker-containers-service.html#varnish-container) は現在、 `centos` Docker 画像。<!--MAGECLOUD-4163-->

      - ![新しいアイコン](../../assets/new.svg) **デフォルトのタイムアウト設定を改善しました。** — 追加済み `.first_byte_timeout` および `.between_bytes_timeout` Vanish コンテナの設定 両方のタイムアウト値のデフォルトはです。 `300s` （5 分）。 [Zilker Technology の Mathew Beane が提出した Fix](https://github.com/magento/magento-cloud-docker/pull/78)<!--MAGECLOUD-4460-->

      - ![修正アイコン](../../assets/fix.svg) **Xdebug セッション中にワニスをスキップ**—Vanish コンテナの設定を更新し、 `pass` Xdebug が有効な場合に受け取ったリクエストを受信したとき。 以前のリリースでは、Docker 環境に Varnish が含まれている場合に Xdebug を使用できませんでした。 [Zilker Technology の Mathew Beane が提出した Fix](https://github.com/magento/magento-cloud-docker/pull/111).<!--MAGECLOUD-4873-->

- ![新しいアイコン](../../assets/new.svg) **Docker 設定の変更**—

   - ![新しいアイコン](../../assets/new.svg) **プロジェクトのマウントとボリュームを管理する**—ローカル開発用に Docker 環境を起動する際にマウントとボリュームを管理する機能が追加されました。 詳しくは、 [プロジェクトデータの共有].<!--MAGECLOUD-3248-->

   - ![新しいアイコン](../../assets/new.svg) **ネットワークブリッジモードのサポート** — ローカルネットワークを介した Docker コンテナ間の接続を可能にする、ネットワークブリッジモードのサポートが追加されました。<!--MAGECLOUD-4165-->

   - ![新しいアイコン](../../assets/new.svg) **Cron コンテナはデフォルトで無効になっています** — パフォーマンスを向上させるために、Docker 環境の構築時に、Cron コンテナがデフォルトで設定されなくなりました。 以下を使用すると、 `--with-cron` オプションを使用して、Cron コンテナを環境に追加できます。 詳しくは、 [cron ジョブの管理](https://devdocs.magento.com/cloud/docker/docker-manage-cron-jobs.html).<!--MAGECLOUD-5181-->

   - ![新しいアイコン](../../assets/new.svg) **大きなバックアップファイルの同期を停止**—DB ダンプとアーカイブファイル (ZIP、SQL、GZ、BZ2) を `dist/docker-sync.yml` および `dist/mutagen.sh` ファイル。 大きなファイル（1 GB を超える）を同期すると、非アクティブ状態が発生し、バックアップファイルを再生成できるので、通常は同期が必要なくなります。<!--MAGECLOUD-3979-->

- ![新しいアイコン](../../assets/new.svg) **コマンドの変更**—

   - ![修正アイコン](../../assets/fix.svg) 名前を変更した `./bin/docker` ～にファイルを送る `./bin/magento-docker` を修正して、 `./bin/docker` ファイルは、既存の Docker バイナリファイルを上書きします。 これは、 [後方互換性のない変化](backward-incompatible-changes.md) スクリプトとコマンドの更新が必要です。<!-- MAGECLOUD-4038 -->

   - ![新しいアイコン](../../assets/new.svg) **データベースポートをホストに公開するためのサービス設定オプションを追加しました。**— `--expose-db-port= [Fix submitted by Adarsh Manickam from Zilker Technology](https://github.com/magento/magento-cloud-docker/pull/101).<PORT>` データベース・ポートをホストに公開するオプション ( `docker-compose.yml` ファイル： `bin/ece-docker build:compose --expose-db-port=<PORT>`<!--MAGECLOUD-4454-->

   - ![新しいアイコン](../../assets/new.svg) **新しい post-deploy コマンド** — 以前は、 `.magento.app.yaml` ファイルは、 `cloud-deploy` コマンドを使用します。 次に、別の `cloud-post-deploy` コマンドを使用して、デプロイ後にデプロイ後のフックを実行します。 更新されたローンチの手順については、 [開発者](https://devdocs.magento.com/cloud/docker/docker-mode-developer.html) および [実稼動](https://devdocs.magento.com/cloud/docker/docker-mode-production.html) モード。<!--MAGECLOUD-3996-->

   - ![新しいアイコン](../../assets/new.svg) 追加された `--rm` 選択肢 `./bin/magento-docker` コンテナのビルドおよびデプロイ用のコマンド。 これにより、タスクの完了後にコンテナが削除されます。<!--MAGECLOUD-4205-->

   - ![新しいアイコン](../../assets/new.svg) **の更新 `build:compose` command**—

      - ![新しいアイコン](../../assets/new.svg) 追加された `--sync-engine="native"` オプションを `docker-build` 」コマンドを使用して、開発者モードで Docker Compose 設定ファイルを生成する際にファイルの同期を無効にします。 ローカルの Docker 開発用にファイル同期を必要としない Linux システムで開発する場合は、このオプションを使用します。 詳しくは、 [Docker 環境でのデータの同期](https://devdocs.magento.com/cloud/docker/docker-syncing-data.html).<!--MCLOUD-3231, MCLOUD-3890-->

   - ![新しいアイコン](../../assets/new.svg) デフォルトのファイル同期設定を `docker-sync` から `native`. [Zilker Technology の Mathew Beane が提出した Fix](https://github.com/magento/magento-cloud-docker/pull/124).<!--MAGECLOUD-5066-->

- ![新しいアイコン](../../assets/new.svg) **検証の改善点**—

   - ![新しいアイコン](../../assets/new.svg) ローカル Docker 開発環境のデプロイメントプロセスに検証機能を追加し、Cloud 環境設定にデータベースの復号化に必要な暗号化キーが含まれていることを検証しました。 これで、環境設定で暗号化キーの値が指定されていない場合に、ログにエラーメッセージが表示されます。<!--MAGECLOUD-4423-->

   - ![新しいアイコン](../../assets/new.svg) ビルドおよびデプロイ処理を続行する前に、Elasticsearchサービスの準備ができていることを確認するために、コンテナのヘルスチェックをビルドサービスに追加しました。 ヘルスチェックがエラーを返した場合、コンテナは自動的に再起動します。<!--MAGECLOUD-4456-->
