---
title: Commerce バージョンのアップグレード
description: クラウドインフラストラクチャプロジェクトでAdobe Commerceのバージョンをアップグレードする方法を説明します。
feature: Cloud, Upgrade
exl-id: 87821007-4979-4a20-940b-aa3c82c192d8
source-git-commit: 99272d08a11f850a79e8e24857b7072d1946f374
workflow-type: tm+mt
source-wordcount: '1439'
ht-degree: 0%

---

# Commerce バージョンのアップグレード

Adobe Commerceのコードベースを新しいバージョンにアップグレードできます。 プロジェクトをアップグレードする前に、 [必要システム構成](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/system-requirements.html) が含まれる _インストール_ 最新のソフトウェアバージョン要件に関するガイド。

プロジェクトの設定に応じて、アップグレードタスクには次のものが含まれる場合があります。

- 新しいAdobe Commerce バージョンとの互換性を確保するために、MariaDB （MySQL）、OpenSearch、RabbitMQ、Redis などの更新サービスを提供しています。
- 古い構成管理ファイルを変換します。
- を更新 `.magento.app.yaml` フックと環境変数の新しい設定を持つファイル。
- サードパーティの拡張機能をサポートされている最新バージョンにアップグレードします。
- を更新 `.gitignore` ファイル。

{{upgrade-tip}}

{{pro-update-service}}

## 古いバージョンからのアップグレード

2.1 より前の Commerce バージョンからアップグレードを開始する場合、Adobe Commerce コードベースの一部の制限事項が次の機能に影響する可能性があります _更新_ 特定の ECE ツールのリリースまたは _アップグレード_ を次にサポートされる Commerce バージョンに変更します。 次の表を使用して、最適なパスを決定します。

| 現在のバージョン | アップグレードパス |
| ----------------- | ------------ |
| 2.1.3 以前 | 続行する前に、Adobe Commerceをバージョン 2.1.4 以降にアップグレードしてください。 次に、を実行します [ece-Tools をインストールするための 1 回限りのアップグレード](../dev-tools/install-package.md). |
| 2.1.4 - 2.1.14 | [ECE ツールの更新](../dev-tools/update-package.md) パッケージ。<br>のリリースノートを参照してください [2002.0.9](../release-notes/cloud-release-archive.md#v200209) 2002.0.x 以降のリリース |
| 2.1.15 - 2.1.16 | [ECE ツールの更新](../dev-tools/update-package.md) パッケージ。<br>のリリースノートを参照してください[2002.0.9](../release-notes/cloud-release-archive.md#v200209) およびあと。 |
| 2.2.x 以降 | [ECE ツールの更新](../dev-tools/update-package.md) パッケージ。<br>のリリースノートを参照してください[2002.0.8](../release-notes/cloud-release-archive.md#v200208) およびあと。 |

{style="table-layout:auto"}

{{ece-tools-package}}

## 設定管理

2.1.4 以降や 2.2.x 以降など、Adobe Commerceの以前のバージョンでは、を使用していました `config.local.php` 構成管理のファイル。 Adobe Commerce バージョン 2.2.0 以降では、 `config.php` ファイル。以下とまったく同じように機能します。 `config.local.php` ファイルですが、有効なモジュールのリストや追加の設定オプションなど、様々な設定があります。

古いバージョンからアップグレードする場合は、 `config.local.php` 新しいを使用するファイル `config.php` ファイル。 次の手順を使用して、設定ファイルをバックアップし、作成します。

**テンポラリを作成するには `config.php` ファイル**:

1. のコピーを作成 `config.local.php` ファイルと名前 `config.php`.

1. このファイルをに追加します `app/etc` プロジェクトのフォルダー。

1. ブランチにファイルを追加してコミットします。

1. ファイルを統合ブランチにプッシュします。

1. アップグレードプロセスを続行します。

>[!WARNING]
>
>アップグレード後、 `config.php` ファイルを作成し、新しい完全なファイルを生成します。 このファイルを削除して置き換えることができるのは、1 回だけです。 新しいの生成後、完了 `config.php` ファイルを削除して新しいファイルを生成することはできません。 参照： [設定管理とパイプラインデプロイメント](../store/store-settings.md).

### Zend Framework Composer の依存関係の検証

へのアップグレード時 **2.2.x から 2.3.x 以降** Zend フレームワークの依存関係がに追加されていることを確認します。 `autoload` のプロパティ `composer.json` ラミナスをサポートするファイル。 このプラグインは、Laminas プロジェクトに移行された Zend フレームワークの新しい要件をサポートします。 参照： [Zend フレームワークの Laminas プロジェクトへの移行](https://community.magento.com/t5/Magento-DevBlog/Migration-of-Zend-Framework-to-the-Laminas-Project/ba-p/443251) 日 _Magento DevBlog_.

**を確認するには `auto-load:psr-4` 設定**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. 統合ブランチを確認します。

1. を開きます `composer.json` ファイルをテキストエディターで開きます。

1. を確認します `autoload:psr-4` コントローラの依存関係用の Zend プラグインマネージャーの実装に関する節。

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

1. Zend 依存性がない場合は、 `composer.json` ファイル：

   - 次の行をに追加します。 `autoload:psr-4` セクション。

     ```json
     "Zend\\Mvc\\Controller\\": "setup/src/Zend/Mvc/Controller/"
     ```

   - プロジェクトの依存関係を更新します。

     ```bash
     composer update
     ```

   - コードの変更を追加、コミットおよびプッシュします。

     ```bash
     git add -A
     ```

     ```bash
     git commit -m "Add Zend plugin manager implementation for controllers dependency for Laminas support"
     ```

     ```bash
     git push origin <branch-name>
     ```

   - 変更内容をステージング環境に結合してから、実稼動環境に結合します。

## 設定ファイル

アプリケーションをアップグレードする前に、クラウドインフラストラクチャー上のAdobe Commerceまたはアプリケーションのデフォルト設定に対する変更を考慮して、プロジェクト設定ファイルを更新する必要があります。 最新のデフォルトは、 [magento-cloud GitHub リポジトリ](https://github.com/magento/magento-cloud).

### .magento.app.yaml

に含まれる値を常に確認する [.magento.app.yaml](../application/configure-app-yaml.md) ファイル（インストールされているバージョン用）。アプリケーションのビルド方法とクラウドインフラストラクチャへのデプロイ方法を制御します。 次の例はバージョン 2.4.7 で、Composer 2.7.2 を使用します。この `build: flavor:` プロパティは Composer 2.x には使用されません。を参照してください。 [Composer 2 のインストールと使用](../application/properties.md#installing-and-using-composer-2).

**を更新するには `.magento.app.yaml` ファイル**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. を開いて編集する `magento.app.yaml` ファイル。

1. PHP オプションを更新します。

   ```yaml
   type: php:8.3
   
   build:
       flavor: none
   dependencies:
       php:
           composer/composer: '2.7.2'
   ```

1. を変更する `hooks` プロパティ `build` および `deploy` コマンド。

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

1. 次の環境変数をファイルの末尾に追加します。

   Adobe Commerce 2.2.x から 2.3.x の場合 – 

   ```yaml
   variables:
       env:
           CONFIG__DEFAULT__PAYPAL_ONBOARDING__MIDDLEMAN_DOMAIN: 'payment-broker.magento.com'
           CONFIG__STORES__DEFAULT__PAYMENT__BRAINTREE__CHANNEL: 'Magento_Enterprise_Cloud_BT'
           CONFIG__STORES__DEFAULT__PAYPAL__NOTATION_CODE: 'Magento_Enterprise_Cloud'
   ```

   Adobe Commerce 2.4.x の場合 – 

   ```yaml
   variables:
       env:
           CONFIG__DEFAULT__PAYPAL_ONBOARDING__MIDDLEMAN_DOMAIN: 'payment-broker.magento.com'
           CONFIG__STORES__DEFAULT__PAYPAL__NOTATION_CODE: 'Magento_Enterprise_Cloud'
   ```

1. ファイルを保存します。 リモート環境に対する変更は、まだコミットまたはプッシュしないでください。

1. アップグレードプロセスを続行します。

### composer.json

アップグレードする前に、必ずで依存関係を確認してください。 `composer.json` ファイルは、Adobe Commerceのバージョンと互換性があります。

**を更新するには `composer.json` Adobe Commerce バージョン 2.4.4 以降のファイル**:

1. 次を追加します `allow-plugins` に `config` セクション：

   ```json
   "config": {
      "allow-plugins": {
         "dealerdirect/phpcodesniffer-composer-installer": true,
         "laminas/laminas-dependency-plugin": true,
         "magento/*": true
      }
   },
   ```

1. 次のプラグインをに追加します。 `require` セクション：

   ```json
   "require": {
       "magento/composer-root-update-plugin": "^2.0.3"
   },
   ```

1. 次のコンポーネントをに追加します `extra:component_paths` セクション：

   ```json
   "extra": {
      "component_paths": {
         "tinymce/tinymce": "lib/web/tiny_mce_5"
      },
   },
   ```

1. ファイルを保存します。 ブランチの変更はまだコミットまたはプッシュしないでください。

1. アップグレードプロセスを続行します。

## プロジェクトのバックアップ

アップグレードの前に、プロジェクトのバックアップを作成することをお勧めします。 統合環境、ステージング環境、実稼動環境をバックアップするには、次の手順を使用します。

**統合環境のデータベースとコードをバックアップするには**:

1. リモート・データベースのローカル・バックアップを作成します。

   ```bash
   magento-cloud db:dump
   ```

   >[!NOTE]
   >
   >この `magento-cloud db:dump` コマンドは、 [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) を使用したコマンド `--single-transaction` フラグを使用すると、テーブルをロックせずにデータベースをバックアップできます。

1. コードとメディアをバックアップします。

   ```bash
   php bin/magento setup:backup --code [--media]
   ```

   オプションで、を省略することもできます。 `[--media]` 既にソース管理されている静的ファイルが多数ある場合。

**デプロイ前にステージング環境または実稼動環境のデータベースをバックアップするには、次の手順に従います**:

1. SSH を使用してリモート環境にログインします。

1. を作成 [データベース ダンプ](../storage/database-dump.md). DB ダンプのターゲット・ディレクトリを選択するには、 `--dump-directory` オプション。

   ```bash
   vendor/bin/ece-tools db-dump
   ```

   ダンプ操作は、を作成します `dump-<timestamp>.sql.gz` リモートプロジェクトディレクトリにファイルをアーカイブします。 参照： [データベースのバックアップ](../storage/database-dump.md).

## アプリケーションのアップグレード

をレビュー [サービスバージョン](../services/services-yaml.md#service-versions) アプリケーションをアップグレードする前の最新のソフトウェアバージョン要件に関する情報です。

**アプリケーションのバージョンをアップグレードするには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. を使用したアップグレードバージョンの設定 [バージョン制約構文](overview.md#cloud-metapackage).

   ```bash
   composer require "magento/magento-cloud-metapackage":">=CURRENT_VERSION <NEXT_VERSION" --no-update
   ```

   >[!NOTE]
   >
   >を正常に更新するには、バージョン制約構文を使用する必要があります `ece-tools` パッケージ。 バージョン制約は次にあります。 `composer.json` のバージョン用ファイル [アプリケーションテンプレート](https://github.com/magento/magento-cloud/blob/master/composer.json) をアップグレードに使用しています。

1. プロジェクトを更新します。

   ```bash
   composer update
   ```

1. コードの変更を追加、コミットおよびプッシュします。

   ```bash
   git add -A
   ```

   ```bash
   git commit -m "Upgrade"
   ```

   ```bash
   git push origin <branch-name>
   ```

   `git add -A` は、Composer マーシャリング ベース パッケージの方式により、変更されたすべてのファイルをソース コントロールに追加するために必要です。 両方 `composer install` および `composer update` ベースパッケージからファイルをマーシャリングする（`magento/magento2-base` および `magento/magento2-ee-base`）をパッケージルートに追加します。

   Composer マーシャリングが属するファイルは、古いバージョンのAdobe Commerceを上書きするために新しいバージョンのファイルです。 現在、Adobe Commerceではマーシャリングが無効になっているので、マーシャリングされたファイルをソース管理に追加する必要があります。

1. デプロイメントが完了するまで待ちます。

1. SSH を使用してログインし、バージョンを確認して、統合環境、ステージング環境、実稼動環境でアップグレードを検証します。

   ```bash
   php bin/magento --version
   ```

### config.php ファイルの作成

で述べたように [設定管理](#configuration-management)、アップグレード後に、更新済みのを作成する必要があります `config.php` ファイル。 統合環境の管理者を通じて、追加の設定変更を行います。

**システム固有の設定ファイルを作成するには**:

1. ターミナルから、SSH コマンドを使用して `/app/etc/config.php` 環境用のファイルです。

   ```bash
   ssh <SSH-URL> "<Command>"
   ```

   例えば Pro の場合、を実行するには `scd-dump` 日 `integration` ブランチ：

   ```bash
   ssh <project-id-integration>@ssh.us.magentosite.cloud "php vendor/bin/ece-tools config:dump"
   ```

1. を転送 `config.php` を使用してローカル ワークステーションにファイルを送信する `rsync` または `scp`. このファイルはブランチに対してのみローカルに追加できます。

   ```bash
   rsync <SSH-URL>:app/etc/config.php ./app/etc/config.php
   ```

1. コードの変更を追加、コミットおよびプッシュします。

   ```bash
   git add app/etc/config.php && git commit -m "Add system-specific configuration" && git push origin master
   ```

   これにより、更新済みのが生成されます `/app/etc/config.php` モジュールリストと設定が記述されたファイルです。

>[!WARNING]
>
>アップグレードの場合、を削除します `config.php` ファイル。 このファイルをコードに追加すると、以下を達成できます **ではない** 削除します。 設定を削除または編集する必要がある場合は、ファイルを手動で編集します。

### アップグレード拡張機能

Marketplace または他の会社のサイトでサードパーティの拡張機能およびモジュールページを確認し、クラウドインフラストラクチャでのAdobe CommerceおよびAdobe Commerceのサポートを検証します。 サードパーティの拡張機能とモジュールをアップグレードする必要がある場合、Adobeでは、拡張機能を無効にした新しい統合ブランチで作業することをお勧めします。

**拡張機能を検証およびアップグレードするには**:

1. ローカルワークステーションにブランチを作成します。

1. 必要に応じて、拡張機能を無効にします。

1. 使用可能な場合は、拡張機能のアップグレードをダウンロードします。

1. サードパーティのドキュメントに記載されているとおりに、アップグレードをインストールします。

1. 拡張機能を有効にしてテストします。

1. コードの変更を追加、コミットし、リモートにプッシュします。

1. 統合環境でにプッシュしてテストします。

1. ステージング環境にプッシュして、実稼動前の環境でテストします。

実稼動Adobeをアップグレードすることを強くお勧めします _次の前_ アップグレードされた拡張機能をサイト起動プロセスに含めます。

>[!NOTE]
>
>アプリケーションのバージョンをアップグレードすると、アップグレードプロセスがの最新バージョンに更新されます [Fastly CDN モジュール](../cdn/fastly.md#fastly-cdn-module-for-magento-2) 自動。

## アップグレードのトラブルシューティング

アップグレードに失敗すると、ストアフロントまたは管理パネルにアクセスできないことを示すエラーメッセージがブラウザーに表示されます。

```terminal
There has been an error processing your request
Exception printing is disabled by default for security reasons.
  Error log record number: <error-number>
```

**エラーを解決するには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. SSH を使用してリモート環境にログインします。

   ```bash
   magento-cloud ssh
   ```

1. を開きます `./app/var/report/<error number>` ファイル。

1. [ログの確認](../test/log-locations.md) 問題の原因を特定します。

1. コードの変更を追加、コミットおよびプッシュします。

   ```bash
   git add -A && git commit -m "Fixed deployment failure" && git push origin <branch-name>
   ```
