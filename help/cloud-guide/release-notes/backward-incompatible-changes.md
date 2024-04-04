---
title: 後方互換性のない変更
description: 既存の Cloud プロジェクトをアップグレードする際の下位互換性について説明します。
feature: Cloud, Release Notes
recommendations: noDisplay, catalog
exl-id: 9847c565-6d59-429a-9593-c2eba5cf2da4
source-git-commit: f9edcc85c14354a2eddacbc5219557e107a367cb
workflow-type: tm+mt
source-wordcount: '791'
ht-degree: 0%

---

# 後方互換性のない変更

後方互換性のない変更では、最新リリースの `ece-tools` パッケージまたはその他のコマース用クラウドツールスイートパッケージ。

## 変更点 `ece-tools` パッケージ

以前は、 `ece-tools` パッケージが別個のパッケージで提供されるようになりました。 これらのパッケージは、に対するコンポーザーの依存関係です。 `ece-tools`ece-tools をインストールまたは更新すると、自動的にインストールおよび更新されます。

新しいアーキテクチャは、インストールまたは更新プロセスに影響を与えません。 ただし、クラウドインフラストラクチャプロジェクト上でAdobe Commerceを操作する際に、いくつかのコマンド構文やプロセスを変更する必要が生じる場合があります。 詳しくは、以下の後方互換性のない変更情報と、 [Cloud Tools Suite リリースノート](cloud-tools-suite.md).

### サービスバージョン要件の変更

PHP の最小バージョン要件を 7.0.x から 7.1.x に変更し、 `ece-tools` v2002.1.0 以降。 環境設定で PHP 7.0 が指定されている場合は、 [php の設定](../application/php-settings.md) （内） `.magento.app.yaml` ファイル。

>[!WARNING]
>
>PHP のバージョン要件が変更されたため、 `ece-tools` 2002.1.0 は、Adobe Commerce 2.1.15 以降を実行するクラウドインフラストラクチャプロジェクトでは、Adobe Commerceのみをサポートします。 プロジェクトで以前のリリースを使用している場合は、 [アップグレード](../development/commerce-version.md) 更新する前に `ece-tools` 2002.1.0.

### 環境設定の変更

次の表に、で削除または廃止された環境変数およびその他の環境設定ファイルに関する情報を示します。 `ece-tools` v2002.1.0

| 項目 | 代替手段 |
| -------- | ----------- |
| `SCD_EXCLUDE_THEMES` 変数 | [`SCD_MATRIX`](../environment/variables-build.md#scd_matrix) |
| `STATIC_CONTENT_THREADS` 変数 | [`SCD_THREADS`](../environment/variables-build.md#scd_threads) |
| `DO_DEPLOY_STATIC_CONTENT` 変数 | [`SKIP_SCD`](../environment/variables-build.md#skip_scd) |
| `STATIC_CONTENT_SYMLINK` 変数 | なし。 これで、ビルドでは常に静的コンテンツディレクトリへのシンボリックリンクが作成されます。 `pub/static`. |
| `build_options.ini` ファイル | 以下を使用します。 [`.magento.env.yaml`](../application/configure-app-yaml.md) ファイルを設定して、すべての環境にわたってビルドとデプロイのアクションを管理するように環境変数を設定します。<p>を含むクラウド環境を構築する場合、 `build_options.ini` ファイルが生成されない場合、ビルドは失敗します。 |

### CLI コマンドの変更

次の表に、ECE-Tools v2002.1.0 での CLI コマンドの変更の概要を示します。コマンドまたはスクリプトの更新が必要になる場合があります。

| コマンド | 代替手段 |
|-------- | ----------- |
| `m2-ece-build` | `vendor/bin/ece-tools build` |
| `m2-ece-deploy` | `vendor/bin/ece-tools deploy` |
| `m2-ece-scd-dump` | `vendor/bin/ece-tools config:dump` |
| `vendor/bin/ece-tools patch` | `vendor/bin/ece-patches apply` |
| `vendor/bin/ece-tools docker:build` | `vendor/bin/ece-docker build:compose` |
| `vendor/bin/ece-tools docker:config:convert` | `vendor/bin/ece-docker  image:generate:php` |

以前の ECE-Tools リリースでは、 `m2-ece-build` および `m2-ece-deploy` に配置フックを設定するコマンド `.magento.app.yaml` ファイル。 v2002.1.0 に更新する場合は、 `hooks` 設定 `.magento.app.yaml` ファイルを使用して古いコマンドを入力し、必要に応じて置き換えます。

## クラウドパッチの変更

- **ダウンロードしたパッチを削除する**- `magento/magento-cloud-patches` パッケージは、次の場所から利用可能なすべてのパッチをバンドルします。 [ソフトウェアダウンロード](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/prerequisites/commerce.html) ページに追加され、クラウドにデプロイする際に自動的に適用されます。 ECE-Tools 2002.1.0 以降にアップグレードした後にパッチの競合を防ぐには、ダウンロードしてプロジェクトに手動で追加したAdobe指定のパッチを削除します。

- **apply patches コマンドの更新** — パッチを適用するコマンドを `vendor/bin/ece-tools` ディレクトリの `vendor/bin/ece-patches` ディレクトリ。 このコマンドを使用してパッチを手動で適用する場合は、新しいパスを使用します。

  > 手動でパッチを適用する

  ```bash
  php ./vendor/bin/ece-patches apply
  ```

## Cloud Docker の変更点

- **最小の PHP バージョンの要件は、PHP 7.1 になりました。**-Cloud Docker for Commerce ホストが以前のバージョンを実行している場合は、PHP v7.1 以降にアップグレードします。

- **Cloud Docker for Commerce コマンドの変更**-

   - **Docker ビルド操作用のコマースコマンド用の Cloud Docker を更新しています** — コマース用の Cloud Docker コマンドを、 `vendor/bin/ece-tools` ディレクトリの `vendor/bin/ece-docker` ディレクトリ。 新しいパスを使用するようにスクリプトとコマンドを更新します。

     へのアップグレード後 `ece-tools` 2002.1.0 では、次のコマンドを使用して使用可能な `ece-docker` コマンド。

     ```bash
     php ./vendor/bin/ece-docker list
     ```

   - **Cloud Docker-compose コマンドの更新** — 次のコマンドファイルへのパスの名前を変更しました： `./bin/docker` から `./bin/magento-docker`. 新しいパスを使用するようにスクリプトとコマンドを更新します。

   - **Cron コンテナはデフォルトの Docker 設定に含まれなくなりました。** — ここで、 `--with-cron` オプションを `ece-docker build:compose` コマンドを使用して、Cron コンテナを Docker 環境設定に含めます。 詳しくは、 [cron ジョブの管理](https://developer.adobe.com/commerce/cloud-tools/docker/configure/manage-cron-jobs/) （内） _Cloud Docker for Commerce_ ガイド。

     cron ジョブを含む以前に生成されたコンテナのスクリプトは、cron コンテナなしになりました。

   - **一時コンテナの使用** — 以前のバージョンでは、 `bin/magento-docker` コマンド操作は削除されなかったので、他の操作に使用できます。 さて、 `magento-docker` コマンドは、コマンドの完了後に作成したコンテナを削除します。

     Docker-compose 操作で作成されたコンテナを保持する場合は、 `docker-compose run` の代わりにコマンドを使用する `bin/magento-docker` コマンドを使用します。

   - **デプロイ後のフックの実行**- `cloud-deploy` コマンドは、デプロイ後のフックを実行しなくなりました。 新しい `cloud-post-deploy` コマンドを使用して、デプロイ後にフックを実行します。 スクリプトを更新し、デプロイ後のフックを実行するコマンドを追加します。

     ```shell
     bin/magento-docker ece-deploy
     bin/magento-docker ece-post-deploy
     ```

     または、 `docker-compose` コマンドを直接実行する `docker-compose run deploy cloud-post-deploy` コマンドを使用して、deploy コマンドを実行します。

- **データベースを更新しています** — データベースコンテナは、 `magento-db` 永続的な Docker ボリューム。 Docker 環境を更新しても、データベースは自動的に削除されなくなります。 必要に応じて、次のいずれかのコマンドを使用して手動で削除します。

   - を削除します。 `magento-db` コンテナ：

     ```bash
     docker volume rm magento-db
     ```

   - Docker コンテナのシャットダウン時に、関連するすべてのボリュームを削除します。

     ```bash
     docker-compose down -v
     ```

- **アーカイブファイルとバックアップファイルのファイル同期設定を上書きする** — 次の拡張子を持つアーカイブファイルとバックアップファイルは、Docker-sync または変異原を使用する際に同期されなくなりました： SQL、GZ、ZIP、BZ2。 これらのファイルタイプのデフォルトのファイル同期を上書きするには、ファイルの名前を変更して、別の拡張子で終わるようにします。 例： `synchronize-me.zip-backup`
