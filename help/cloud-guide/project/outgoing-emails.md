---
title: 送信メールを設定
description: クラウドインフラストラクチャ上のAdobe Commerceに対して送信メールを有効にする方法を説明します。
exl-id: 814fe2a9-15bf-4bcb-a8de-ae288fd7f284
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '279'
ht-degree: 0%

---

# 送信メールを設定

各環境に対する送信メールは、 [!DNL Cloud Console] またはコマンドラインから。 統合およびステージング環境用の送信メールを有効にして、クラウドプロジェクトユーザーに 2 要素認証またはパスワードリセット用の電子メールを送信します。

デフォルトでは、実稼動環境では送信電子メールが有効になっています。 The [!UICONTROL Enable outgoing emails] は、 [`enable_smtp` プロパティ](#enable-emails-in-the-cli).

{{redeploy-warning}}

## での E メールの有効化 [!DNL Cloud Console]

以下を使用します。 **[!UICONTROL Outgoing emails]** 切り替え _環境の設定_ 電子メールのサポートを有効または無効にするビュー。

**電子メールのサポートを[!DNL Cloud Console]**:

1. にログインします。 [[!DNL Cloud Console]](https://console.adobecommerce.com).
1. 次の場所からプロジェクトを選択： _すべてのプロジェクト_ リスト。
1. 「プロジェクト」ダッシュボードで、右上の設定アイコンをクリックします。
1. クリック **[!UICONTROL Environments]** をクリックし、リストから特定の環境を選択します。
1. 送信メールを有効または無効にするには、切り替えます _送信メールを有効にする_ **オン** または **オフ**.

   ![送信メールの設定を有効にする](../../assets/outgoing-emails.png)

設定を変更すると、環境は、新しい設定でビルドおよびデプロイされます。

## CLI でのメールの有効化

アクティブな環境の E メール設定は、 `magento-cloud` CLI `environment:info` コマンドを使用して `enable_smtp` プロパティ。 SMTP アップデートを有効にする： `MAGENTO_CLOUD_SMTP_HOST` 環境変数に、メールを送信する SMTP ホストの IP アドレスを設定します。

**コマンドラインからメールサポートを管理するには**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. 環境の送信メール設定を確認します。

   ```bash
   magento-cloud environment:info -e <environment-id> | grep enable_smtp
   ```

1. 電子メールサポートの設定を変更するには、 `enable_smtp` 環境変数から `true` または `false`.

   ```bash
   magento-cloud environment:info --refresh -e <environment-id> enable_smtp true
   ```

   環境が構築されデプロイされるのを待ちます。

1. SSH を使用してリモート環境にログインします。

1. E メールが機能することを確認し、確認可能なアドレスにテスト用の E メールを送信します。

   ```bash
   php -r 'mail("mail@example.com", "test message", "just testing", "From: tester@example.com");'
   ```
