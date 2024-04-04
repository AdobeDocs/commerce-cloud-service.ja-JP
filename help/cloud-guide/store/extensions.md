---
title: 拡張機能の管理
description: クラウドインフラストラクチャ上のAdobe Commerceに拡張機能をインストールして管理する方法について説明します。
feature: Cloud, Extensions, Upgrade
exl-id: 9c6e98ca-85da-4342-8402-d576eb382ba2
source-git-commit: bb7a866b1896a8a43d01ad3f83dc655bcf383374
workflow-type: tm+mt
source-wordcount: '626'
ht-degree: 0%

---

# 拡張機能の管理

Adobe Commerceアプリケーションの機能を拡張するには、 [Commerce Marketplace](https://marketplace.magento.com). 例えば、テーマを追加してストアフロントの外観と操作性を変更したり、言語パッケージを追加してストアフロントと管理者をローカライズしたりできます。

## 拡張機能のコンポーザー名

この節では、Commerce Marketplaceから拡張機能のコンポーザー名とバージョンを取得する方法について説明しますが、の名前とバージョンを検索できます。 _任意_ モジュールの Composer ファイル内のモジュール。 を開きます。 `composer.json` ファイルをテキストエディターで編集し、 `"name"` および `"version"` 値。

**モジュールからモジュールのコンポーザー名を取得するには、以下を実行します。Commerce Marketplace**:

1. にログインします。 [Commerce Marketplace](https://marketplace.magento.com) コンポーネントの購入に使用したユーザー名とパスワード。

1. 右上隅で、ユーザー名をクリックし、「 」を選択します。 **マイプロファイル**.

   ![Marketplace アカウントへのアクセス](../../assets/marketplace/my-profile.png)

1. 次の日： _マイアカウント_ ページ、クリック **マイ購入**.

   ![Marketplace の購入履歴](../../assets/marketplace/my-purchases.png)

1. 次の日： _マイ購入_ ページで、購入したモジュールを選択し、 **技術的な詳細**.

1. クリック **コピー** コピーするには [!UICONTROL Component name] をクリップボードに追加します。

1. テキストエディターを開き、コンポーネント名を貼り付け、コロン (`:`) をクリックします。

1. In **技術的な詳細**&#x200B;をクリックし、 **コピー** コピーするには [!UICONTROL Component version] をクリップボードに追加します。

1. テキストエディターで、コロンの後にコンポーネント名を追加します。 例：

   ```text
   extension-name/magento2:1.0.1
   ```

## 拡張機能のインストール

Adobeでは、実装に拡張機能を追加する場合は、開発ブランチで作業することをお勧めします。 拡張機能をインストールする際に、拡張機能名 (`<VendorName>_<ComponentName>`) は、 [`app/etc/config.php`](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/files/deployment-files.html) ファイル。 ファイルを直接編集する必要はありません。

**拡張機能をインストールするには、以下を実行します。**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. 開発ブランチを作成するか、チェックアウトします。 詳しくは、 [分岐](../development/cli-branches.md).

1. コンポーザーの名前とバージョンを使用して、拡張機能を `require` のセクション `composer.json` ファイル。

   ```bash
   composer require <extension-name>:<version> --no-update
   ```

1. プロジェクトの依存関係を更新します。

   ```bash
   composer update
   ```

1. コードの変更を追加、コミット、およびプッシュします。

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
   >拡張機能をインストールする場合は、 `composer.lock` ファイルを作成します。 The `composer install` コマンドは、 `composer.lock` ファイルを使用して、リモート環境で定義された依存関係を有効にします。

1. ビルドとデプロイが完了したら、SSH を使用してリモート環境にログインし、インストールされている拡張機能を確認します。

   ```bash
   bin/magento module:status <extension-name>
   ```

   拡張機能名は、次の形式を使用します。 `<VendorName>_<ComponentName>`.

   レスポンスのサンプル：

   ```terminal
   Module is enabled
   ```

   デプロイメントエラーが発生した場合は、 [拡張機能のデプロイメント失敗](../deploy/recover-failed-deployment.md).

## 拡張機能の管理

Composer を使用して拡張機能を追加すると、デプロイメントプロセスによって自動的に拡張機能が有効になります。 既に拡張機能がインストールされている場合は、CLI を使用して拡張機能を有効または無効にできます。 拡張機能を管理する場合は、次の形式を使用します。 `<VendorName>_<ComponentName>`

リモート環境へのログイン中に、拡張機能を有効または無効にしないでください。

**拡張機能を有効または無効にするには**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. モジュールを有効または無効にします。 The `module` コマンドは、 `config.php` ファイルのステータスを指定します。

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

1. コードの変更を追加、コミット、およびプッシュします。

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

続行する前に、拡張機能のコンポーザー名とバージョンが必要です。 また、拡張機能がプロジェクトおよびAdobe Commerceのバージョンと互換性があることを確認します。 特に [必要な PHP バージョンの確認](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/system-requirements.html) 始める前に

**拡張機能を更新するには、以下を実行します。**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. 開発ブランチを作成するか、チェックアウトします。 詳しくは、 [分岐](../development/cli-branches.md).

1. を開きます。 `composer.json` ファイルを編集します。

1. 拡張機能を見つけて、バージョンを更新します。

1. 変更を保存し、テキストエディターを終了します。

1. プロジェクトの依存関係を更新します。

   ```bash
   composer update
   ```

1. コードの変更を追加、コミット、およびプッシュします。

   ```bash
   git add -A
   ```

   ```bash
   git commit -m "Update <extension-name>"
   ```

   ```bash
   git push origin <branch-names>
   ```

エラーが発生した場合は、 [コンポーネントの障害からの回復](../deploy/recover-failed-deployment.md). Adobe Commerceでの拡張機能の使用について詳しくは、 [拡張機能](https://experienceleague.adobe.com/docs/commerce-admin/start/resources/extensions.html) （内） _管理ガイド_.
