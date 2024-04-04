---
title: ECE-Tools を使用するには、プロジェクトをアップグレードします
description: ECE-Tools パッケージを使用してAdobe Commerce on cloud infrastructure プロジェクトをアップグレードし、最新の修正および機能を活用する方法を説明します。
feature: Cloud, Install
exl-id: 820cca84-2817-4881-829f-ebb78400d8c7
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '365'
ht-degree: 0%

---

# ECE-Tools パッケージを使用するには、プロジェクトをアップグレードします

Adobeが廃止されました： `magento/magento-cloud-configuration` および `magento/ece-patches` ～に有利な小包 `ece-tools` パッケージを使用すると、多くのクラウドプロセスを簡略化できます。 以前のAdobe Commerce on クラウドインフラストラクチャプロジェクトを使用している場合、 _not_ を含む `ece-tools` パッケージを作成したら、1 回限りの手動操作を実行する必要があります。 _アップグレード_ をプロジェクトに追加します。

>[!WARNING]
>
>プロジェクトに `ece-tools` パッケージの場合は、次のアップグレードをスキップできます。 確認するには、 [!DNL Commerce] を使用したバージョン `php vendor/bin/ece-tools -V` コマンドをローカルプロジェクトのルートディレクトリに追加します。

このプロジェクトのアップグレードプロセスでは、 `magento/magento-cloud-metapackage` バージョン制約 `composer.json` ファイルをルートディレクトリに配置します。 この制約により、現在のAdobe Commerceバージョンをアップグレードせずに、クラウドインフラストラクチャのメタパッケージ（非推奨のパッケージの削除を含む）に関するAdobe Commerceの更新を有効にします。

{{upgrade-tip}}

## 非推奨（廃止予定）のパッケージを削除

アップグレードを実行して `ece-tools` パッケージ、チェック `composer.lock` 次の非推奨パッケージのファイル：

- `magento/magento-cloud-configuration`
- `magento/ece-patches`

## メタパッケージの更新

各Adobe Commerceバージョンには、次に基づく異なる制約が必要です。

```terminal
>=current_version <next_version
```

- の場合 `current_version`、インストールするAdobe Commerceのバージョンを指定します。
- の場合 `next_version`に設定する場合は、 `current_version`.

Adobe Commerce `2.3.5-p2`，設定 `current_version` から `2.3.5` そして `next_version` から `2.3.6`. 制約 `">=2.3.5 <2.3.6"` は、2.3.5 で利用可能な最新のパッケージをインストールします。

最新のメタパッケージ制約は、 [`magento-cloud` テンプレート](https://github.com/magento/magento-cloud/blob/master/composer.json).

次の例では、クラウドインフラストラクチャメタパッケージ上のAdobe Commerceの、現在のバージョン 2.4.5 以上で、次のバージョン 2.4.6 未満のバージョンに制約を設定します。

```json
"require": {
    "magento/magento-cloud-metapackage": ">=2.4.5 <2.4.6"
},
```

## プロジェクトのアップグレード

を使用するようにプロジェクトをアップグレードするには `ece-tools` パッケージを更新するには、メタパッケージと `.magento.app.yaml` フックのプロパティを作成し、Composer の更新を実行します。

**ece-tools を使用するようにプロジェクトをアップグレードするには**:

1. を更新します。 `magento/magento-cloud-metapackage` バージョン制約 `composer.json` ファイル。

   ```bash
   composer require "magento/magento-cloud-metapackage":">=2.4.5 <2.4.6" --no-update
   ```

1. メタパッケージを更新します。

   ```bash
   composer update magento/magento-cloud-metapackage
   ```

1. でフックコマンドを修正する `magento.app.yaml` ファイル。

   ```yaml
   hooks:
       # We run build hooks before your application has been packaged.
       build: |
           set -e
           php ./vendor/bin/ece-tools run scenario/build/generate.xml
           php ./vendor/bin/ece-tools run scenario/build/transfer.xml
       # We run deploy hook after your application has been deployed and started.
       deploy: |
           php ./vendor/bin/ece-tools run scenario/deploy.xml
       # We run post deploy hook to clean and warm the cache. Available with ECE-Tools 2002.0.10.
       post_deploy: |
           php ./vendor/bin/ece-tools run scenario/post-deploy.xml
   ```

1. を確認し、を削除します。 [廃止されたパッケージ](#remove-deprecated-packages). 非推奨のパッケージは、アップグレードの成功を妨げる可能性があります。

   ```bash
   composer remove magento/magento-cloud-configuration
   ```

   ```bash
   composer remove magento/ece-patches
   ```

1. 場合によっては、 `ece-tools` パッケージ。

   ```bash
   composer update magento/ece-tools
   ```

1. コードの変更を追加してコミットします。 この例では、次のファイルが更新されました。

   ```terminal
   .magento.app.yaml
   composer.json
   composer.lock
   ```

1. コードの変更をリモートサーバーにプッシュし、このブランチを `integration` 分岐。

   ```bash
   git push origin <branch-name>
   ```
