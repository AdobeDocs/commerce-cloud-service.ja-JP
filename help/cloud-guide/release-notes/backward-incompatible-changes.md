---
title: 後方互換性のない変更
description: 既存のクラウドプロジェクトをアップグレードする際の後方互換性について説明します。
feature: Cloud, Release Notes
recommendations: noDisplay, catalog
exl-id: 9847c565-6d59-429a-9593-c2eba5cf2da4
source-git-commit: f9edcc85c14354a2eddacbc5219557e107a367cb
workflow-type: tm+mt
source-wordcount: '791'
ht-degree: 0%

---

# 後方互換性のない変更

後方互換性のない変更を行うには、の最新リリースにアップグレードする際に、既存のクラウドプロジェクトのクラウド設定とプロセスを調整する必要がある場合があります `ece-tools` Commerce パッケージ用のパッケージまたはその他の Cloud Tools Suite。

## 変更先 `ece-tools` package

一部の機能は以前にに含まれています `ece-tools` パッケージは、別個のパッケージで提供されるようになりました。 これらのパッケージは、 `ece-tools`:ece-tools をインストールまたは更新すると、自動的にインストールおよび更新されます。

新しいアーキテクチャは、インストールや更新のプロセスには影響を与えません。 ただし、クラウドインフラストラクチャプロジェクトでAdobe Commerceを使用する場合、コマンドの構文とプロセスをいくつか変更する必要が生じる場合があります。 詳しくは、次の後方互換性のない変更情報と [Cloud Tools Suite リリースノート](cloud-tools-suite.md).

### サービス バージョン要件の変更

を使用する Cloud プロジェクトの最小 PHP バージョン要件を 7.0.x から 7.1.x に変更しました。 `ece-tools` v2002.1.0 以降。 環境設定で PHP 7.0 が指定されている場合は、 [php 設定](../application/php-settings.md) が含まれる `.magento.app.yaml` ファイル。

>[!WARNING]
>
>PHP のバージョン要件が変更されたため、 `ece-tools` 2002.1.0 では、Adobe Commerce 2.1.15 以降を実行しているクラウドインフラストラクチャプロジェクトのAdobe Commerceのみをサポートしています。 以前のリリースをプロジェクトで使用している場合は、次の操作を行う必要があります [アップグレード](../development/commerce-version.md) をに更新する前に `ece-tools` 2002.1.0。

### 環境設定の変更

次の表は、で削除または非推奨になった、環境変数およびその他の環境設定ファイルの情報を示しています `ece-tools` v2002.1.0。

| 項目 | 置き換え |
| -------- | ----------- |
| `SCD_EXCLUDE_THEMES` 変数 | [`SCD_MATRIX`](../environment/variables-build.md#scd_matrix) |
| `STATIC_CONTENT_THREADS` 変数 | [`SCD_THREADS`](../environment/variables-build.md#scd_threads) |
| `DO_DEPLOY_STATIC_CONTENT` 変数 | [`SKIP_SCD`](../environment/variables-build.md#skip_scd) |
| `STATIC_CONTENT_SYMLINK` 変数 | なし。 現在は、ビルドにより、常に静的コンテンツディレクトリへのシンボリックリンクが作成されます `pub/static`. |
| `build_options.ini` ファイル | の使用 [`.magento.env.yaml`](../application/configure-app-yaml.md) すべての環境でビルドおよびデプロイアクションを管理するための環境変数を設定するファイル。<p>を含むクラウド環境を作成する場合 `build_options.ini` ファイルの場合、ビルドは失敗します。 |

### CLI コマンドの変更点

次の表に、コマンドまたはスクリプトの更新が必要になる可能性のある ECE-Tools v2002.1.0 の CLI コマンドの変更点を要約します。

| コマンド | 置き換え |
|-------- | ----------- |
| `m2-ece-build` | `vendor/bin/ece-tools build` |
| `m2-ece-deploy` | `vendor/bin/ece-tools deploy` |
| `m2-ece-scd-dump` | `vendor/bin/ece-tools config:dump` |
| `vendor/bin/ece-tools patch` | `vendor/bin/ece-patches apply` |
| `vendor/bin/ece-tools docker:build` | `vendor/bin/ece-docker build:compose` |
| `vendor/bin/ece-tools docker:config:convert` | `vendor/bin/ece-docker  image:generate:php` |

以前の ECE-Tools リリースでは、 `m2-ece-build` および `m2-ece-deploy` でデプロイメントフックを設定するコマンド `.magento.app.yaml` ファイル。 を v2002.1.0 に更新する場合は、 `hooks` での設定 `.magento.app.yaml` 古いコマンド用のファイルを作成し、必要に応じて置き換えます。

## クラウドパッチの変更

- **ダウンロードしたパッチの削除** – その `magento/magento-cloud-patches` パッケージバンドルには、から使用可能なすべてのパッチが含まれています [ソフトウェアのダウンロード](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/prerequisites/commerce.html) ページ化し、クラウドにデプロイするときに自動的に適用されます。 ECE-Tools 2002.1.0 以降にアップグレードした後にパッチの競合が発生しないようにするには、ダウンロードしてプロジェクトに追加したAdobe提供のパッチを手動で削除します。

- **パッチ適用コマンドの更新** – パッチを適用するためのコマンドを `vendor/bin/ece-tools` ディレクトリ `vendor/bin/ece-patches` ディレクトリ。 このコマンドを使用してパッチを手動で適用する場合は、新しいパスを使用します。

  > パッチを手動で適用

  ```bash
  php ./vendor/bin/ece-patches apply
  ```

## Cloud Docker の変更点

- **PHP の最小バージョン要件は PHP 7.1 になりました**- Cloud Docker for Commerce ホストで以前のバージョンが実行されている場合は、PHP v7.1 以降にアップグレードしてください。

- **Cloud Docker for Commerce コマンドの変更点**-

   - **Docker ビルド操作用の Cloud Docker for Commerce コマンドの更新** – から Cloud Docker for Commerce コマンドを移動しました `vendor/bin/ece-tools` ディレクトリ `vendor/bin/ece-docker` ディレクトリ。 新しいパスを使用するようにスクリプトとコマンドを更新します。

     へのアップグレード後 `ece-tools` 2002.1.0 で、次のコマンドを使用して使用可能な状態を表示します `ece-docker` コマンド。

     ```bash
     php ./vendor/bin/ece-docker list
     ```

   - **Cloud docker-compose コマンドの更新** – コマンド ファイルのパスの名前を `./bin/docker` 対象： `./bin/magento-docker`. 新しいパスを使用するようにスクリプトとコマンドを更新します。

   - **Cron コンテナがデフォルトの Docker 設定に含まれなくなりました** – 今すぐ、を追加する必要があります `--with-cron` のオプション `ece-docker build:compose` Docker 環境設定に Cron コンテナを含めるコマンド。 参照： [Cron ジョブの管理](https://developer.adobe.com/commerce/cloud-tools/docker/configure/manage-cron-jobs/) が含まれる _Cloud Docker for Commerce_ ガイド。

     以前に cron ジョブでコンテナを生成したスクリプトは、cron コンテナを使用しなくなりました。

   - **一時コンテナの使用** – 以前のバージョンでは、によって作成されたコンテナ `bin/magento-docker` コマンド操作は削除されなかったので、他の操作に使用できます。 さて、 `magento-docker` コマンドは、コマンドの完了後に作成したコンテナをすべて削除します。

     Docker-compose 操作で作成されたコンテナを保持する場合は、を使用します `docker-compose run` の代わりにをコマンドします。 `bin/magento-docker` コマンド。

   - **デプロイ後のフックの実行** – その `cloud-deploy` コマンドは、デプロイ後のフックを実行しなくなりました。 新しいを使用 `cloud-post-deploy` デプロイ後にデプロイ後のフックを実行するコマンド。 スクリプトを更新して、コマンドを追加し、デプロイ後のフックを実行します。

     ```shell
     bin/magento-docker ece-deploy
     bin/magento-docker ece-post-deploy
     ```

     または、次を使用する場合： `docker-compose` コマンドを直接実行する `docker-compose run deploy cloud-post-deploy` deploy コマンドの後のコマンドです。

- **データベースの更新** – データベース コンテナは次に格納されています： `magento-db` 永続的な Docker ボリューム。 Docker 環境を更新すると、データベースは自動的には削除されなくなります。 必要に応じて、次のいずれかのコマンドを使用して手動で削除します。

   - を削除 `magento-db` コンテナ：

     ```bash
     docker volume rm magento-db
     ```

   - Docker コンテナをシャットダウンする際に、関連するボリュームをすべて削除します。

     ```bash
     docker-compose down -v
     ```

- **アーカイブ ファイルとバックアップ ファイルのファイル同期設定を上書きする**-Docker-sync または mutagen:SQL、GZ、ZIP および BZ2 を使用する場合、拡張子が次のアーカイブおよびバックアップファイルは同期されなくなります。 これらのファイルタイプのデフォルトのファイル同期を上書きするには、ファイル名を別の拡張子に変更します。 例： `synchronize-me.zip-backup`
