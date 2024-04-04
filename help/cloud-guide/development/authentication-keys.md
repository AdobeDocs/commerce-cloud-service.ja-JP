---
title: 認証キー
description: クラウドインフラストラクチャ上のAdobe Commerceの開発プロジェクトに認証キーを適用する方法を説明します。
feature: Cloud, Security
topic: Security
exl-id: b05cd4c2-0804-49c8-980a-4c7b6932082b
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '301'
ht-degree: 0%

---

# 認証キー

Adobe Commerceリポジトリにアクセスし、クラウドインフラストラクチャプロジェクトでAdobe Commerceのインストールおよび更新コマンドを有効にするには、認証キーが必要です。 Composer の認証資格情報を指定する方法は 2 つあります。

- **認証ファイル**— Adobe Commerceを含むファイル [認証資格情報](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/prerequisites/authentication-keys.html) クラウドインフラストラクチャのルートディレクトリのAdobe Commerceで、
- **環境変数** — 誤って認識されるのを防ぐために、クラウドインフラストラクチャプロジェクト上のAdobe Commerceで認証キーを設定する環境変数。

>[!BEGINSHADEBOX]

**セキュリティメモ**

Adobeは、 [環境変数](#composer-auth-environment-variable) メソッドを使用して、認証資格情報が誤って公開されるのを防ぎます。

認証ファイル方式は、 Cloud Docker for Commerce をローカル開発ツールとして使用する場合に最適ですが、 `auth.json` ファイルを公開 Git ベースのリポジトリに保存します。 次の項目を追加できます。 `auth.json` ファイルを [`.gitignore` ファイル](../project/file-structure.md#ignoring-files).

>[!ENDSHADEBOX]

## 認証ファイル

**次の手順で、 `auth.json` ファイル**:

1. 次の条件を満たしていない場合、 `auth.json` ファイルをプロジェクトのルートディレクトリに作成します。

   - テキストエディターを使用して、 `auth.json` ファイルをプロジェクトのルートディレクトリに保存します。
   - の内容をコピーします。 [サンプル `auth.json`](https://github.com/magento/magento2/blob/2.3/auth.json.sample) 新しい `auth.json` ファイル。

1. 置換 `<public-key>` および `<private-key>` Adobe Commerce認証資格情報を使用して、

   ```json
   {
       "http-basic": {
           "repo.magento.com": {
               "username": "<public-key>",
               "password": "<private-key>"
           }
       }
   }
   ```

1. 変更を保存し、テキストエディターを終了します。

## Composer 認証環境変数

次の方法は、公開 Git ベースのリポジトリで機密性の高い資格情報が誤って公開されるのを防ぐ最善の方法です。

**環境変数を使用して認証キーを追加するには**:

1. Adobe Analytics の _[!DNL Cloud Console]_で、プロジェクトナビゲーションの右側にある設定アイコンをクリックします。

   ![プロジェクトを設定](../../assets/icon-configure.png){width="36"}

1. Adobe Analytics の _プロジェクト設定_ リスト、クリック **[!UICONTROL Variables]**.

1. クリック **[!UICONTROL Create variable]**.

1. Adobe Analytics の **[!UICONTROL Variable name]** フィールドに入力 `env:COMPOSER_AUTH`.

1. Adobe Analytics の _値_ フィールドに次を追加し、 `<public-key>` および `<private-key>` Adobe Commerce認証資格情報を使用：

   ```json
   {
       "http-basic": {
           "repo.magento.com": {
               "username": "<public-key>",
               "password": "<private-key>"
           }
       }
   }
   ```

1. 選択 **[!UICONTROL Available during buildtime]** 選択を解除します。 **[!UICONTROL Available during runtime]**.

1. クリック **[!UICONTROL Create variable]**.

1. を削除します。 `auth.json` ファイルを作成します。
