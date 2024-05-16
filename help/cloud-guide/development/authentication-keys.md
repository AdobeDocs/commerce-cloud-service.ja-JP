---
title: 認証キー
description: クラウドインフラストラクチャー上のAdobe Commerceの開発プロジェクトに認証キーを適用する方法を説明します。
feature: Cloud, Security
topic: Security
exl-id: b05cd4c2-0804-49c8-980a-4c7b6932082b
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '301'
ht-degree: 0%

---

# 認証キー

Adobe Commerce リポジトリにアクセスし、クラウドインフラストラクチャプロジェクト上のAdobe Commerceのインストールおよびアップデートコマンドを有効にするには、認証キーが必要です。 Composer 認証資格情報を指定する方法は 2 つあります。

- **認証ファイル**—Adobe Commerceを含むファイル [認証資格情報](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/prerequisites/authentication-keys.html) Adobe Commerce（クラウドインフラストラクチャー上のルートディレクトリ）
- **環境変数** – 誤って公開してしまうのを防ぐため、クラウドインフラストラクチャプロジェクト上のAdobe Commerceに認証キーを設定するための環境変数。

>[!BEGINSHADEBOX]

**セキュリティ メモ**

Adobeでは、を使用することをお勧めします [環境変数](#composer-auth-environment-variable) クラウドプロジェクトを使用する方法で、認証資格情報が誤って漏洩されるのを防ぎます。

ローカル開発ファイル方式は、Cloud Docker for Commerceを認証ツールとして使用する場合に最適ですが、 `auth.json` 公開 Git ベースのリポジトリにファイルを送信します。 を追加できます `auth.json` にファイルします。 [`.gitignore` ファイル](../project/file-structure.md#ignoring-files).

>[!ENDSHADEBOX]

## 認証ファイル

**を作成するには `auth.json` ファイル**:

1. を持っていない場合 `auth.json` ファイルをプロジェクトのルートディレクトリに作成します。

   - テキストエディターを使用して、 `auth.json` ファイルをプロジェクトのルートディレクトリに置きます。
   - の内容をコピーします [サンプル `auth.json`](https://github.com/magento/magento2/blob/2.3/auth.json.sample) 新しい `auth.json` ファイル。

1. 置換 `<public-key>` および `<private-key>` （Adobe Commerce認証資格情報を使用）

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

次の方法は、公開 Git ベースのリポジトリ内の機密性の高い認証情報が誤って公開されるのを防ぐための最適な方法です。

**環境変数を使用して認証キーを追加するには**:

1. が含まれる _[!DNL Cloud Console]_を開き、プロジェクトのナビゲーションの右側にある「設定」アイコンをクリックします。

   ![プロジェクトの設定](../../assets/icon-configure.png){width="36"}

1. が含まれる _プロジェクト設定_ リスト、クリック **[!UICONTROL Variables]**.

1. クリック **[!UICONTROL Create variable]**.

1. が含まれる **[!UICONTROL Variable name]** フィールド、を入力 `env:COMPOSER_AUTH`.

1. が含まれる _値_ フィールドで、次を追加して置換 `<public-key>` および `<private-key>` Adobe Commerce認証資格情報を使用：

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

1. を選択 **[!UICONTROL Available during buildtime]** およびを選択解除 **[!UICONTROL Available during runtime]**.

1. クリック **[!UICONTROL Create variable]**.

1. を削除 `auth.json` 各環境のファイル。
