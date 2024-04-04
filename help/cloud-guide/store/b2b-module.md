---
title: B2B モジュールを有効にする
description: クラウドインフラストラクチャ上でAdobe Commerceの B2B モジュールを有効にする方法を説明します。
feature: Cloud, B2B
exl-id: 01d02ea0-1e7d-4608-adbf-1dad7f5e2182
source-git-commit: bb7a866b1896a8a43d01ad3f83dc655bcf383374
workflow-type: tm+mt
source-wordcount: '336'
ht-degree: 0%

---

# B2B モジュールを有効にする

お客様が会社の場合は、B2B for Adobe Commerceモジュールをインストールして、Cloud Infrastructure Pro プロジェクト上のAdobe Commerceを拡張し、B2B モデルに対応できます。 このトピックでは、クラウドインフラストラクチャにAdobe Commerce用の B2B モジュールをインストールおよび設定する際の情報を提供しますが、次のガイドで追加の B2B 情報を参照できます。

- [Adobe Commerce B2B 開発者ガイド](https://developer.adobe.com/commerce/webapi/rest/b2b/)
- [Adobe Commerce B2B ユーザーガイド](https://experienceleague.adobe.com/docs/commerce-admin/b2b/guide-overview.html)

>[!TIP]
>
>B2B はクラウドインフラストラクチャ上のAdobe Commerceのモジュールなので、Adobeでは、開始する前にAdobe Commerceアプリケーションを統合またはステージング環境にデプロイすることをお勧めします。

## B2B モジュールのインストール

Adobeは、B2B モジュールをプロジェクトに追加する際に開発ブランチで作業することをお勧めします。 ブランチがない場合は、 [開発用のブランチの作成](../development/cli-branches.md#create-a-branch-for-development). B2B モジュールをインストールする場合、 `Magento_B2b` モジュール名は `app/etc/config.php` ファイル。 ファイルを直接編集する必要はありません。

**B2B モジュールをインストールするには**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. 開発ブランチを作成するか、チェックアウトします。

1. B2B モジュールを `require` のセクション `composer.json` ファイル。

   ```bash
   composer require magento/extension-b2b --no-update
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
   git commit -m "Install the B2B module."
   ```

   ```bash
   git push origin <branch-name>
   ```

1. ビルドとデプロイが完了したら、SSH を使用してリモート環境にログインし、B2B モジュールがインストールされていることを確認します。

   ```bash
   bin/magento module:status Magento_B2b
   ```

   拡張機能名は、次の形式を使用します。 `<VendorName>_<ComponentName>`.

   レスポンスのサンプル：

   ```terminal
   Magento_B2b : Module is enabled
   ```

   デプロイメントエラーが発生した場合は、 [コンポーネントの障害からの回復](../deploy/recover-failed-deployment.md).

## B2B モジュールを有効にする

Composer を使用して B2B モジュールをインストールすると、デプロイメントプロセスによってモジュールが自動的に有効になります。 既に B2B モジュールがインストールされている場合は、CLI を使用してモジュールを有効または無効にできます

B2B モジュールを有効にします。

```bash
bin/magento module:enable Magento_B2b
```

レスポンスのサンプル：

```terminal
The following modules have been enabled:
- Magento_B2b

Cache cleared successfully.
Generated classes cleared successfully. Please run the 'setup:di:compile' command to generate classes.
Info: Some modules might require static view files to be cleared. To do this, run 'module:enable' with the --clear-static-content option to clear them.
```

詳しくは、 [拡張機能の管理](extensions.md).

## B2B モジュールの設定

B2B for Adobe Commerceモジュールをインストールした後、 [メッセージ消費者を起動](https://experienceleague.adobe.com/docs/commerce-admin/b2b/install.html#start-message-consumers) これにより、 _共有カタログ_ モジュールを選択し、 [B2B 機能を有効にする](https://experienceleague.adobe.com/docs/commerce-admin/b2b/enable-basic-features.html).
