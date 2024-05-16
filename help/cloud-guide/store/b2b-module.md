---
title: B2B モジュールの有効化
description: クラウドインフラストラクチャー上でAdobe Commerceの B2B モジュールを有効にする方法について説明します。
feature: Cloud, B2B
exl-id: 01d02ea0-1e7d-4608-adbf-1dad7f5e2182
source-git-commit: bb7a866b1896a8a43d01ad3f83dc655bcf383374
workflow-type: tm+mt
source-wordcount: '336'
ht-degree: 0%

---

# B2B モジュールの有効化

お客様が企業の場合は、B2B for Adobe Commerce モジュールをインストールして、B2B B モデルに対応するようにAdobe Commerce on Cloud Infrastructure Pro プロジェクトを拡張できます。 このトピックでは、クラウドインフラストラクチャ上でのAdobe Commerceの B2B モジュールのインストールと設定に関する具体的な情報を提供しますが、B2B に関するその他の情報は次のガイドで確認できます。

- [Adobe Commerce B2B デベロッパーガイド](https://developer.adobe.com/commerce/webapi/rest/b2b/)
- [Adobe Commerce B2B ユーザーガイド](https://experienceleague.adobe.com/docs/commerce-admin/b2b/guide-overview.html)

>[!TIP]
>
>B2B はクラウドインフラストラクチャー上のAdobe Commerceのモジュールなので、Adobeでは、開始前にAdobe Commerce アプリケーションを統合環境またはステージング環境にデプロイすることをお勧めします。

## B2B モジュールのインストール

Adobeは、B2B モジュールをプロジェクトに追加する際には、開発ブランチで作業することをお勧めします。 ブランチがない場合は、 [開発用のブランチの作成](../development/cli-branches.md#create-a-branch-for-development). B2B モジュールをインストールする場合、 `Magento_B2b` モジュール名は、 `app/etc/config.php` ファイル。 ファイルを直接編集する必要はありません。

**B2B モジュールをインストールするには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. 開発ブランチを作成またはチェックアウトします。

1. に B2B モジュールを追加 `require` の節 `composer.json` ファイル。

   ```bash
   composer require magento/extension-b2b --no-update
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
   git commit -m "Install the B2B module."
   ```

   ```bash
   git push origin <branch-name>
   ```

1. ビルドおよびデプロイが完了したら、SSH を使用してリモート環境にログインし、B2B モジュールがインストールされていることを確認します。

   ```bash
   bin/magento module:status Magento_B2b
   ```

   拡張機能名には、次の形式を使用します。 `<VendorName>_<ComponentName>`.

   応答の例：

   ```terminal
   Magento_B2b : Module is enabled
   ```

   デプロイメントエラーが発生した場合は、を参照してください。 [コンポーネント障害からのリカバリ](../deploy/recover-failed-deployment.md).

## B2B モジュールの有効化

Composer を使用して B2B モジュールをインストールすると、デプロイメントプロセスによってモジュールが自動的に有効になります。 既に B2B モジュールがインストールされている場合は、CLI を使用してモジュールを有効または無効にできます

B2B モジュールを有効にします。

```bash
bin/magento module:enable Magento_B2b
```

応答の例：

```terminal
The following modules have been enabled:
- Magento_B2b

Cache cleared successfully.
Generated classes cleared successfully. Please run the 'setup:di:compile' command to generate classes.
Info: Some modules might require static view files to be cleared. To do this, run 'module:enable' with the --clear-static-content option to clear them.
```

参照： [拡張機能の管理](extensions.md).

## B2B モジュールの設定

Adobe Commerce用 B2B モジュールをインストールした後、次の操作を行う必要があります [メッセージコンシューマーの開始](https://experienceleague.adobe.com/docs/commerce-admin/b2b/install.html#start-message-consumers) これにより、 _共有カタログ_ モジュールと、次の操作が必要です [b2B 機能の有効化](https://experienceleague.adobe.com/docs/commerce-admin/b2b/enable-basic-features.html).
