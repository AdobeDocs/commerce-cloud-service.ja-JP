---
title: コマースバージョンをアップグレード
description: クラウドインフラストラクチャプロジェクトでAdobe Commerceのバージョンをアップグレードする方法について説明します。
feature: Cloud, Upgrade
exl-id: 87821007-4979-4a20-940b-aa3c82c192d8
source-git-commit: 745a9f08353bd5dfbb871ca88947157c145c7c70
workflow-type: tm+mt
source-wordcount: '1439'
ht-degree: 0%

---

# コマースバージョンをアップグレード

Adobe Commerce Code Base を新しいバージョンにアップグレードできます。 プロジェクトをアップグレードする前に、 [必要システム構成](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/system-requirements.html) （内） _インストール_ 最新のソフトウェアバージョンの要件に関するガイド。

プロジェクトの設定に応じて、アップグレードタスクには次のものが含まれます。

- 新しいAdobe Commerceバージョンとの互換性を確保するために、MariaDB(MySQL)、OpenSearch、RabbitMQ、Redis などのサービスを更新しました。
- 古い構成管理ファイルを変換します。
- を更新します。 `.magento.app.yaml` ファイルの新しい設定が追加されました。
- サードパーティの拡張機能を、サポートされている最新バージョンにアップグレードします。
- を更新します。 `.gitignore` ファイル。

{{upgrade-tip}}

{{pro-update-service}}

## 古いバージョンからのアップグレード

2.1 より前のコマースバージョンからアップグレードを開始する場合、Adobe Commerceコードベースの一部の制限は、 _更新_ 特定の ECE-Tools リリースまたは _アップグレード_ を次のサポート対象の Commerce バージョンに変更します。 次の表を使用して、最適なパスを判断します。

| 現在のバージョン | アップグレードパス |
| ----------------- | ------------ |
| 2.1.3 以前 | 続行する前に、Adobe Commerceをバージョン 2.1.4 以降にアップグレードしてください。 次に、 [ECE-Tools をインストールするための 1 回限りのアップグレード](../dev-tools/install-package.md). |
| 2.1.4 - 2.1.14 | [ECE-Tools を更新](../dev-tools/update-package.md) パッケージ。<br>次のリリースノートを参照してください： [2002.0.9](../release-notes/cloud-release-archive.md#v200209) および 2002.0.x リリース以降 |
| 2.1.15 - 2.1.16 | [ECE-Tools を更新](../dev-tools/update-package.md) パッケージ。<br>次のリリースノートを参照してください：[2002.0.9](../release-notes/cloud-release-archive.md#v200209) 後で。 |
| 2.2.x 以降 | [ECE-Tools を更新](../dev-tools/update-package.md) パッケージ。<br>次のリリースノートを参照してください：[2002.0.8](../release-notes/cloud-release-archive.md#v200208) 後で。 |

{style="table-layout:auto"}

{{ece-tools-package}}

## 設定の管理

2.1.4 以降から 2.2.x 以降など、Adobe Commerceの古いバージョンでは、 `config.local.php` ファイルを参照してください。 Adobe Commerceバージョン 2.2.0 以降では、 `config.php` ファイルで、 `config.local.php` ファイルには異なる設定があり、有効なモジュールのリストと追加の設定オプションが含まれます。

古いバージョンからアップグレードする場合は、 `config.local.php` 新しい `config.php` ファイル。 次の手順を実行して、設定ファイルをバックアップし、作成します。

**一時的なを作成するには `config.php` ファイル**:

1. のコピーを作成 `config.local.php` ファイルに名前を付けます。 `config.php`.

1. このファイルを `app/etc` プロジェクトのフォルダー。

1. ブランチにファイルを追加してコミットします。

1. ファイルを統合ブランチにプッシュします。

1. アップグレードプロセスを続行します。

>[!WARNING]
>
>アップグレード後に、 `config.php` ファイルを作成し、完全な新しいファイルを生成します。 このファイルを削除して置き換える操作は、一度だけ実行できます。 新しいを生成した後、完了 `config.php` ファイルを削除して新しいファイルを生成することはできません。 詳しくは、 [設定の管理とパイプラインのデプロイメント](../store/store-settings.md).

### Zend Framework コンポーザーの依存関係の検証

にアップグレードする場合 **2.2.x 以降の 2.3.x 以降**、Zend Framework の依存関係が `autoload` のプロパティ `composer.json` ファイルを使用して Laminas をサポートします。 このプラグインは、Laminas プロジェクトに移行された Zend Framework の新しい要件をサポートします。 詳しくは、 [Laminas プロジェクトへの Zend Framework の移行](https://community.magento.com/t5/Magento-DevBlog/Migration-of-Zend-Framework-to-the-Laminas-Project/ba-p/443251) の _MagentoDevBlog_.

**次の手順で `auto-load:psr-4` 設定**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. 統合ブランチを確認します。

1. を開きます。 `composer.json` ファイルを編集します。

1. 次を確認します。 `autoload:psr-4` コントローラ依存関係に関する Zend プラグインマネージャ実装の節。

   ```json
    "autoload": {
       "psr-4": {
          "Magento\\Framework\\": "lib/internal/Magento/Framework/",
          "Magento\\Setup\\": "setup/src/Magento/Setup/",
          "Magento\\": "app/code/Magento/",
          "Zend\\Mvc\\Controller\\": "setup/src/Zend/Mvc/Controller/"
       },
   }
   ```

1. Zend 依存関係が見つからない場合は、 `composer.json` ファイル：

   - 次の行を `autoload:psr-4` 」セクションに入力します。

     ```json
     "Zend\\Mvc\\Controller\\": "setup/src/Zend/Mvc/Controller/"
     ```

   - プロジェクトの依存関係を更新します。

     ```bash
     composer update
     ```

   - コードの変更を追加、コミット、およびプッシュします。

     ```bash
     git add -A
     ```

     ```bash
     git commit -m "Add Zend plugin manager implementation for controllers dependency for Laminas support"
     ```

     ```bash
     git push origin <branch-name>
     ```

   - ステージング環境に対する変更をマージしてから、実稼動環境にマージします。

## 設定ファイル

アプリケーションをアップグレードする前に、クラウドインフラストラクチャ上のAdobe Commerceまたはアプリケーションのデフォルトの設定に対する変更を考慮するように、プロジェクト設定ファイルを更新する必要があります。 最新のデフォルト設定は、 [magento-cloud GitHub リポジトリ](https://github.com/magento/magento-cloud).

### .magento.app.yaml

常に [.magento.app.yaml](../application/configure-app-yaml.md) ファイルを作成します。これは、アプリケーションがクラウドインフラストラクチャに構築およびデプロイする方法を制御するからです。 次の例は、バージョン 2.4.6 用で、Composer 2.2.21 を使用しています。The `build: flavor:` プロパティは Composer 2.x では使用されません。詳しくは、 [Composer 2 のインストールと使用](../application/properties.md#installing-and-using-composer-2).

**次の手順で `.magento.app.yaml` ファイル**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. を開き、 `magento.app.yaml` ファイル。

1. PHP オプションを更新します。

   ```yaml
   type: php:8.2
   
   build:
       flavor: none
   dependencies:
       php:
           composer/composer: '2.2.21'
   ```

1. を変更します。 `hooks` プロパティ `build` および `deploy` コマンド。

   ```yaml
   hooks:
       # We run build hooks before your application has been packaged.
       build: |
           set -e
           composer install
           php ./vendor/bin/ece-tools run scenario/build/generate.xml
           php ./vendor/bin/ece-tools run scenario/build/transfer.xml
       # We run deploy hook after your application has been deployed and started.
       deploy: |
           php ./vendor/bin/ece-tools run scenario/deploy.xml
       # We run post deploy hook to clean and warm the cache. Available with ECE-Tools 2002.0.10.
       post_deploy: |
           php ./vendor/bin/ece-tools run scenario/post-deploy.xml
   ```

1. ファイルの最後に次の環境変数を追加します。

   Adobe Commerce 2.2.x～2.3.x の場合 —

   ```yaml
   variables:
       env:
           CONFIG__DEFAULT__PAYPAL_ONBOARDING__MIDDLEMAN_DOMAIN: 'payment-broker.magento.com'
           CONFIG__STORES__DEFAULT__PAYMENT__BRAINTREE__CHANNEL: 'Magento_Enterprise_Cloud_BT'
           CONFIG__STORES__DEFAULT__PAYPAL__NOTATION_CODE: 'Magento_Enterprise_Cloud'
   ```

   Adobe Commerce 2.4.x — の場合

   ```yaml
   variables:
       env:
           CONFIG__DEFAULT__PAYPAL_ONBOARDING__MIDDLEMAN_DOMAIN: 'payment-broker.magento.com'
           CONFIG__STORES__DEFAULT__PAYPAL__NOTATION_CODE: 'Magento_Enterprise_Cloud'
   ```

1. ファイルを保存します。 まだリモート環境に変更をコミットしたりプッシュしたりしないでください。

1. アップグレードプロセスを続行します。

### composer.json

アップグレードする前に、常に `composer.json` ファイルはAdobe Commerceバージョンと互換性があります。

**次の手順で `composer.json` Adobe Commerceバージョン 2.4.4 以降のファイル**:

1. 以下を追加します。 `allow-plugins` から `config` セクション：

   ```json
   "config": {
      "allow-plugins": {
         "dealerdirect/phpcodesniffer-composer-installer": true,
         "laminas/laminas-dependency-plugin": true,
         "magento/*": true
      }
   },
   ```

1. 次のプラグインを `require` セクション：

   ```json
   "require": {
       "magento/composer-root-update-plugin": "^2.0.3"
   },
   ```

1. 次のコンポーネントを `extra:component_paths` セクション：

   ```json
   "extra": {
      "component_paths": {
         "tinymce/tinymce": "lib/web/tiny_mce_5"
      },
   },
   ```

1. ファイルを保存します。 まだブランチに変更をコミットしたりプッシュしたりしないでください。

1. アップグレードプロセスを続行します。

## プロジェクトのバックアップ

アップグレードの前に、プロジェクトのバックアップを作成することをお勧めします。 次の手順を使用して、統合環境、ステージング環境および実稼動環境をバックアップします。

**統合環境のデータベースとコードをバックアップするには**:

1. リモート・データベースのローカル・バックアップを作成します。

   ```bash
   magento-cloud db:dump
   ```

   >[!NOTE]
   >
   >The `magento-cloud db:dump` コマンドを実行 [mysqlump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) コマンドを `--single-transaction` フラグを使用して、テーブルをロックせずにデータベースをバックアップできます。

1. コードとメディアをバックアップします。

   ```bash
   php bin/magento setup:backup --code [--media]
   ```

   必要に応じて、を省略できます。 `[--media]` 既にソース管理下にある多数の静的ファイルがある場合。

**デプロイする前にステージング環境または実稼動環境のデータベースをバックアップするには**:

1. SSH を使用してリモート環境にログインします。

1. の作成 [データベースダンプ](../storage/database-dump.md). DB ダンプのターゲットディレクトリを選択するには、 `--dump-directory` オプション。

   ```bash
   vendor/bin/ece-tools db-dump
   ```

   ダンプ操作により、 `dump-<timestamp>.sql.gz` リモートプロジェクトディレクトリのアーカイブファイル。 詳しくは、 [データベースのバックアップ](../storage/database-dump.md).

## アプリケーションのアップグレード

以下を確認します。 [サービスバージョン](../services/services-yaml.md#service-versions) アプリケーションをアップグレードする前の最新のソフトウェアバージョンの要件に関する情報。

**アプリケーションのバージョンをアップグレードするには**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. を使用してアップグレードバージョンを設定します。 [バージョン制約の構文](overview.md#cloud-metapackage).

   ```bash
   composer require "magento/magento-cloud-metapackage":">=CURRENT_VERSION <NEXT_VERSION" --no-update
   ```

   >[!NOTE]
   >
   >バージョン制約の構文を使用して、 `ece-tools` パッケージ。 バージョン制約は、 `composer.json` のバージョン用のファイル [アプリケーションテンプレート](https://github.com/magento/magento-cloud/blob/master/composer.json) を使用してアップグレードを行っています。

1. プロジェクトを更新します。

   ```bash
   composer update
   ```

1. コードの変更を追加、コミット、およびプッシュします。

   ```bash
   git add -A
   ```

   ```bash
   git commit -m "Upgrade"
   ```

   ```bash
   git push origin <branch-name>
   ```

   `git add -A` は、Composer がベースパッケージをマーシャリングする方法により、変更されたすべてのファイルをソース管理に追加するために必要です。 両方 `composer install` および `composer update` ベースパッケージ (`magento/magento2-base` および `magento/magento2-ee-base`) をパッケージのルートに追加します。

   Composer がマーシャリングするファイルは、Adobe Commerceの新しいバージョンに属しており、同じファイルの古いバージョンを上書きします。 現在、マーシャリングはAdobe Commerceで無効になっているので、ソース管理にマーシャリングされたファイルを追加する必要があります。

1. デプロイメントが完了するまで待ちます。

1. SSH を使用してログインし、バージョンを確認して、統合、ステージングまたは実稼動環境でアップグレードを検証します。

   ```bash
   php bin/magento --version
   ```

### config.php ファイルの作成

例： [設定の管理](#configuration-management)アップグレード後に、更新された `config.php` ファイル。 その他の設定変更は、統合環境の管理者におこないます。

**システム固有の設定ファイルを作成するには**:

1. ターミナルから、SSH コマンドを使用して `/app/etc/config.php` ファイルを作成します。

   ```bash
   ssh <SSH-URL> "<Command>"
   ```

   例えば、Pro の場合、を実行するには、 `scd-dump` の `integration` ブランチ：

   ```bash
   ssh <project-id-integration>@ssh.us.magentosite.cloud "php vendor/bin/ece-tools config:dump"
   ```

1. を転送します。 `config.php` を使用してローカルワークステーションにファイルを送信する `rsync` または `scp`. このファイルは、ローカルのブランチにのみ追加できます。

   ```bash
   rsync <SSH-URL>:app/etc/config.php ./app/etc/config.php
   ```

1. コードの変更を追加、コミット、およびプッシュします。

   ```bash
   git add app/etc/config.php && git commit -m "Add system-specific configuration" && git push origin master
   ```

   これにより、更新された `/app/etc/config.php` ファイルに、モジュールリストと構成設定を含めます。

>[!WARNING]
>
>アップグレードの場合、 `config.php` ファイル。 このファイルをコードに追加したら、 **not** 削除します。 設定を削除または編集する必要がある場合は、ファイルを手動で編集します。

### 拡張機能のアップグレード

Marketplace またはその他の会社のサイトでサードパーティの拡張機能およびモジュールページを確認し、クラウドインフラストラクチャ上でのAdobe CommerceおよびAdobe Commerceのサポートを検証します。 サードパーティの拡張機能およびモジュールをアップグレードする必要がある場合は、Adobeは、新しい統合ブランチで、拡張機能を無効にすることをお勧めします。

**拡張機能を確認してアップグレードするには**:

1. ローカルワークステーションにブランチを作成します。

1. 必要に応じて、拡張機能を無効にします。

1. 利用可能な場合は、拡張機能のアップグレードをダウンロードします。

1. サードパーティのドキュメントに記載されているように、アップグレードをインストールします。

1. 拡張機能を有効にしてテストします。

1. コードの変更をリモートに追加、コミット、およびプッシュします。

1. 統合環境でにプッシュしてテストします。

1. ステージング環境にプッシュして、実稼動前の環境でテストします。

Adobeでは、実稼動環境のアップグレードを強くお勧めします _前_ サイト launch プロセスにアップグレードされた拡張機能を含めます。

>[!NOTE]
>
>アプリケーションのバージョンをアップグレードすると、アップグレードプロセスは最新バージョンの [Fastly CDN モジュール](../cdn/fastly.md#fastly-cdn-module-for-magento-2) 自動的に。

## アップグレードのトラブルシューティング

アップグレードに失敗した場合は、ストアフロントまたは管理パネルにアクセスできないことを示すエラーメッセージがブラウザーに表示されます。

```terminal
There has been an error processing your request
Exception printing is disabled by default for security reasons.
  Error log record number: <error-number>
```

**エラーを解決するには**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. SSH を使用してリモート環境にログインします。

   ```bash
   magento-cloud ssh
   ```

1. を開きます。 `./app/var/report/<error number>` ファイル。

1. [ログを調べる](../test/log-locations.md) 問題の発生元を特定します。

1. コードの変更を追加、コミット、およびプッシュします。

   ```bash
   git add -A && git commit -m "Fixed deployment failure" && git push origin <branch-name>
   ```
