---
title: ECE-Tools を使用するようにプロジェクトをアップグレード
description: クラウドインフラストラクチャプロジェクトのAdobe Commerceをアップグレードして、ECE-Tools パッケージを使用し、最新の修正点および機能を活用する方法について説明します。
feature: Cloud, Install
exl-id: 820cca84-2817-4881-829f-ebb78400d8c7
source-git-commit: bcdb59f0d2a17e55e8b0479ee69fac06c710638f
workflow-type: tm+mt
source-wordcount: '365'
ht-degree: 0%

---

# ECE-Tools パッケージを使用するようにプロジェクトをアップグレード

Adobeは、を廃止しました `magento/magento-cloud-configuration` および `magento/ece-patches` を支持するパッケージ `ece-tools` 多くのクラウドプロセスを簡素化するパッケージ。 古いAdobe Commerce on cloud infrastructure プロジェクトを使用する場合は、次のようになります _ではない_ contain `ece-tools` パッケージ化したら、1 回限りの手動を実行する必要があります _アップグレード_ プロジェクトに対して処理を行います。

>[!WARNING]
>
>プロジェクトにが含まれている場合 `ece-tools` パッケージが追加されました。次のアップグレードはスキップできます。 検証するには、を取得します [!DNL Commerce] を使用したバージョン `php vendor/bin/ece-tools -V` ローカルプロジェクトのルートディレクトリにあるコマンド。

このプロジェクトのアップグレード プロセスでは、 `magento/magento-cloud-metapackage` でのバージョン制約 `composer.json` ファイルをルートディレクトリに配置します。 この制約により、現在のAdobe Commerce バージョンをアップグレードすることなく、クラウドインフラストラクチャメタパッケージ（非推奨パッケージの削除など）上のAdobe Commerceを更新できます。

{{upgrade-tip}}

## 非推奨（廃止予定）のパッケージを削除

アップグレードを実行する前に、を使用します `ece-tools` パッケージ、 `composer.lock` 次の非推奨パッケージ用のファイル：

- `magento/magento-cloud-configuration`
- `magento/ece-patches`

## メタパッケージの更新

Adobe Commerceの各バージョンには、次に基づいて異なる制約が必要です。

```terminal
>=current_version <next_version
```

- の場合 `current_version`を開き、インストールするAdobe Commerceのバージョンを指定します。
- の場合 `next_version`で指定した値の後の次のパッチバージョンを指定します。 `current_version`.

Adobe Commerceのインストール `2.3.5-p2`、設定 `current_version` 対象： `2.3.5` および `next_version` 対象： `2.3.6`. 制約 `">=2.3.5 <2.3.6"` 2.3.5 用に利用可能な最新のパッケージをインストールします。

常に最新のメタパッケージ制約は [`magento-cloud` template](https://github.com/magento/magento-cloud/blob/master/composer.json).

次の例では、クラウドインフラストラクチャメタパッケージ上のAdobe Commerceを、現在のバージョン 2.4.7 以上、次のバージョン 2.4.8 以下の任意のバージョンに制限します。

```json
"require": {
    "magento/magento-cloud-metapackage": ">=2.4.7 <2.4.8"
},
```

## プロジェクトのアップグレード

使用するようにプロジェクトをアップグレードするには `ece-tools` パッケージを使用する場合は、メタパッケージを更新し、 `.magento.app.yaml` プロパティをフックし、Composer の更新を実行します。

**ece-tools を使用するようにプロジェクトをアップグレードするには**:

1. を更新 `magento/magento-cloud-metapackage` でのバージョン制約 `composer.json` ファイル。

   ```bash
   composer require "magento/magento-cloud-metapackage":">=2.4.7 <2.4.8" --no-update
   ```

1. メタパッケージを更新します。

   ```bash
   composer update magento/magento-cloud-metapackage
   ```

1. でフック コマンドを変更する `magento.app.yaml` ファイル。

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

1. を確認して削除します [非推奨パッケージ](#remove-deprecated-packages). 非推奨パッケージを使用すると、アップグレードが正常に行われなくなる可能性があります。

   ```bash
   composer remove magento/magento-cloud-configuration
   ```

   ```bash
   composer remove magento/ece-patches
   ```

1. を更新する必要がある場合があります `ece-tools` パッケージ。

   ```bash
   composer update magento/ece-tools
   ```

1. コードの変更内容を追加してコミットします。 この例では、次のファイルが更新されました。

   ```terminal
   .magento.app.yaml
   composer.json
   composer.lock
   ```

1. コードの変更をリモートサーバーにプッシュし、このブランチを `integration` 分岐。

   ```bash
   git push origin <branch-name>
   ```
