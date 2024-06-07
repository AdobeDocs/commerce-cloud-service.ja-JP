---
title: 拡張機能の管理
description: クラウドインフラストラクチャー上のAdobe Commerceで拡張機能をインストールおよび管理する方法について説明します。
feature: Cloud, Extensions, Upgrade
exl-id: 9c6e98ca-85da-4342-8402-d576eb382ba2
source-git-commit: f8fb9d4d43c85f91ff87686160bcddb7cd417635
workflow-type: tm+mt
source-wordcount: '646'
ht-degree: 0%

---

# 拡張機能の管理

から拡張機能を追加することで、Adobe Commerce アプリケーションの機能を拡張できます。 [Commerce Marketplace](https://marketplace.magento.com). 例えば、テーマを追加してストアフロントのルックアンドフィールを変更したり、言語パッケージを追加してストアフロントと管理者をローカライズしたりできます。

>[!NOTE]
>
>インストールの問題を回避するには、すべての Marketplace での購入を、クラウドプロジェクトを所有するのと同じアカウント（MAGEID）を使用して完了する必要があります。

## 拡張機能のコンポーザー名

このセクションでは、Commerce Marketplaceから Composer 名と拡張機能のバージョンを取得する方法について説明しますが、 _いずれか_ モジュールの Composer ファイル内のモジュール。 を開きます `composer.json` ファイルをテキストエディターで開き、 `"name"` および `"version"` 値。

**Commerce Marketplaceからモジュールの Composer 名を取得するには**:

1. へのログイン [Commerce Marketplace](https://marketplace.magento.com) と、コンポーネントの購入に使用したユーザー名およびパスワード。

1. 右上隅のユーザー名をクリックし、を選択します。 **マイプロファイル**.

   ![Marketplace アカウントにアクセス](../../assets/marketplace/my-profile.png)

1. 日 _マイアカウント_ ページ、クリック **購入状況**.

   ![Marketplace の購入履歴](../../assets/marketplace/my-purchases.png)

1. 日 _購入状況_ ページで購入したモジュールを選択し、クリックします **技術的詳細**.

1. クリック **コピー** をコピーします [!UICONTROL Component name] をクリップボードに追加します。

1. テキストエディターを開き、コンポーネント名を貼り付けて、コロン文字（`:`）に設定します。

1. 対象： **技術的詳細**&#x200B;を選択し、 **コピー** をコピーします [!UICONTROL Component version] をクリップボードに追加します。

1. テキストエディターで、コンポーネント名のコロンの後にバージョン番号を追加します。 例：

   ```text
   extension-name/magento2:1.0.1
   ```

## 拡張機能のインストール

Adobeでは、実装に拡張機能を追加する際に、開発ブランチで作業することをお勧めします。 拡張機能をインストールする場合、拡張機能名（`<VendorName>_<ComponentName>`）が [`app/etc/config.php`](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/files/deployment-files.html) ファイル。 ファイルを直接編集する必要はありません。

**拡張機能をインストールするには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. 開発ブランチを作成またはチェックアウトします。 参照： [分岐](../development/cli-branches.md).

1. Composer の名前とバージョンを使用して、拡張機能をに追加します `require` の節 `composer.json` ファイル。

   ```bash
   composer require <extension-name>:<version> --no-update
   ```

1. プロジェクトの依存関係を更新します。

   ```bash
   composer update
   ```

1. コードの変更を追加、コミットおよびプッシュします。

   ```bash
   git add -A
   ```

   ```bash
   git commit -m "Install <extension-name>"
   ```

   ```bash
   git push origin <branch-name>
   ```

   >[!WARNING]
   >
   >拡張機能をインストールする際は、を含める必要があります `composer.lock` コードの変更をリモート環境にプッシュする際のファイル。 この `composer install` コマンドは、 `composer.lock` ファイル：リモート環境で定義された依存関係を有効にします。

1. ビルドおよびデプロイが完了したら、SSH を使用してリモート環境にログインし、インストールされている拡張機能を確認します。

   ```bash
   bin/magento module:status <extension-name>
   ```

   拡張機能名には、次の形式を使用します。 `<VendorName>_<ComponentName>`.

   応答の例：

   ```terminal
   Module is enabled
   ```

   デプロイメントエラーが発生した場合は、を参照してください。 [拡張機能のデプロイメントの失敗](../deploy/recover-failed-deployment.md).

## 拡張機能の管理

Composer を使用して拡張機能を追加すると、デプロイメント プロセスによって拡張機能が自動的に有効になります。 拡張機能が既にインストールされている場合は、CLI を使用して拡張機能を有効または無効にできます。 拡張機能を管理する場合は、次の形式を使用します。 `<VendorName>_<ComponentName>`

リモート環境にログインしている間は、拡張機能を有効または無効にしないでください。

**拡張機能を有効または無効にするには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. モジュールを有効または無効にします。 この `module` コマンドは、 `config.php` モジュールのリクエストされたステータスを持つファイル。

   >モジュールを有効にします。

   ```bash
   bin/magento module:enable <module-name>
   ```

   >モジュールを無効にします。

   ```bash
   bin/magento module:disable <module-name>
   ```

1. モジュールを有効にした場合は、 `ece-tools` をクリックして設定を更新します。

   ```bash
   ./vendor/bin/ece-tools module:refresh
   ```

1. モジュールのステータスを確認します。

   ```bash
   bin/magento module:status <module-name>
   ```

1. コードの変更を追加、コミットおよびプッシュします。

   ```bash
   git add -A
   ```

   ```bash
   git commit -m "Disable <extension-name>"
   ```

   ```bash
   git push origin <branch-names>
   ```

## 拡張機能のアップグレード

続行する前に、拡張機能の Composer 名とバージョンが必要です。 また、拡張機能がプロジェクトおよびAdobe Commerceのバージョンと互換性があることを確認します。 特に、 [必要な PHP のバージョンを確認する](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/system-requirements.html) 始める前に。

**拡張機能を更新するには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. 開発ブランチを作成またはチェックアウトします。 参照： [分岐](../development/cli-branches.md).

1. を開きます `composer.json` ファイルをテキストエディターで開きます。

1. 拡張機能を探して、バージョンを更新します。

1. 変更を保存し、テキストエディターを終了します。

1. プロジェクトの依存関係を更新します。

   ```bash
   composer update
   ```

1. コードの変更を追加、コミット、プッシュします。

   ```bash
   git add -A
   ```

   ```bash
   git commit -m "Update <extension-name>"
   ```

   ```bash
   git push origin <branch-names>
   ```

エラーが発生した場合は、を参照してください [コンポーネント障害からのリカバリ](../deploy/recover-failed-deployment.md). Adobe Commerceでの拡張機能の使用について詳しくは、 [拡張機能](https://experienceleague.adobe.com/docs/commerce-admin/start/resources/extensions.html) が含まれる _管理ガイド_.
