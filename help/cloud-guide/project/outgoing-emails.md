---
title: 送信メールの設定
description: クラウドインフラストラクチャでAdobe Commerce用の送信メールを有効にする方法を説明します。
exl-id: 814fe2a9-15bf-4bcb-a8de-ae288fd7f284
source-git-commit: 59f82d891bb7b1953c1e19b4c1d0a272defb89c1
workflow-type: tm+mt
source-wordcount: '363'
ht-degree: 0%

---

# 送信メールの設定

から環境ごとに送信メールを有効または無効にできます [!DNL Cloud Console] または、コマンドラインから実行します。 統合およびステージング環境で送信メールを有効にして、クラウドプロジェクトユーザーに二要素認証またはパスワードリセットのメールを送信できるようにします。

デフォルトでは、実稼動環境とステージング環境で送信メールが有効になっています。 ただし、 [!UICONTROL Enable outgoing emails] を設定するまで、環境設定で無効と表示されることがあります。 `enable_smtp` を使用したプロパティ [コマンドライン](#enable-emails-in-the-cli) または [クラウドコンソール](outgoing-emails.md#enable-emails-in-the-cloud-console).

を更新中 [!UICONTROL enable_smtp] プロパティ値の基準 [コマンドライン](#enable-emails-in-the-cli) 次も変更： [!UICONTROL Enable outgoing emails] cloud Console でのこの環境の値の設定。

{{redeploy-warning}}

## Cloud Console でメールを有効にする

の使用 **[!UICONTROL Outgoing emails]** での切り替え _環境の設定_ 表示してメールのサポートを有効または無効にします。

実稼動環境またはステージング環境で送信メールを無効にするか再度有効にする必要がある場合は、 [Adobe Commerce サポートチケット](https://experienceleague.adobe.com/en/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide).

>[!TIP]
>
>送信メールのステータスが、Cloud Console の Pro 環境に反映されない場合があります。 代わりに、を使用します [コマンドライン](#enable-emails-in-the-cli) （送信メールの有効化およびテスト用）。

**からのメールサポートを管理するには[!DNL Cloud Console]**:

1. にログインします [[!DNL Cloud Console]](https://console.adobecommerce.com).
1. 「」からプロジェクトを選択 _すべてのプロジェクト_ リスト。
1. プロジェクトダッシュボードで、右上の設定アイコンをクリックします。
1. クリック **[!UICONTROL Environments]** リストから特定の環境を選択します。
1. 送信メールを有効または無効にするには、を切り替えます _送信メールを有効にする_ **日付：** または **オフ**.

   ![送信メールの設定を有効にする](../../assets/outgoing-emails.png)

設定を変更すると、環境は新しい設定でビルドおよびデプロイされます。

## CLI でのメールの有効化

アクティブな環境のメール設定を変更するには、 `magento-cloud` CLI `environment:info` 設定するコマンド `enable_smtp` プロパティ。 SMTP を有効にすると、が更新される `MAGENTO_CLOUD_SMTP_HOST` メール送信用の SMTP ホストの IP アドレスを指定する環境変数。

**コマンドラインからメールサポートを管理するには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. 環境の送信メールの設定を確認します。

   ```bash
   magento-cloud environment:info -e <environment-id> | grep enable_smtp
   ```

1. を設定して、メールサポートの設定を変更します。 `enable_smtp` 環境変数をに `true` または `false`.

   ```bash
   magento-cloud environment:info --refresh -e <environment-id> enable_smtp true
   ```

   環境がビルドおよびデプロイされるのを待ちます。

1. SSH を使用してリモート環境にログインします。

1. メールが機能することを確認します。確認可能なアドレスにテストメールを送信します。

   ```bash
   php -r 'mail("mail@example.com", "test message", "just testing", "From: tester@example.com");'
   ```

1. メールが SendGrid で受信されることを確認します。

   ```bash
   grep mail@example.com /var/log/mail.log
   ```
