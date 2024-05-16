---
title: 静的ファイルのキャッシュの設定
description: でキャッシュストレージオプションを設定する方法を説明します [!DNL Commerce] アプリケーション設定ファイル。
feature: Cloud, Configuration, Cache, SCD
exl-id: ca6db004-47fc-45ea-b8db-c0ecc3c2136b
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '113'
ht-degree: 0%

---

# 静的ファイルのキャッシュの設定

メディアおよび静的ファイルのキャッシュ TTL （有効期間）は、次で設定します。 `.magento.app.yaml` を使用した設定ファイル `expires` キー。

>[!NOTE]
>
>実稼動環境を更新する前に、ステージング環境で変更をテストすることが重要です。 [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) これらの環境の設定の更新に関するヘルプ。

1. 以下で TTL 時間（秒単位）を指定 [`web` プロパティ](web-property.md) の `.magento.app.yaml` ファイル。 を追加できます `expires` キーの下 `locations` または以下 `"/media"` および `"/static"`.

   キャッシュの有効期限が切れないようにするには、 `expires: -1` キーと値のペア 次の例を参照してください。

   ```yaml
   # The configuration of app when it is exposed to the web.
   web:
     locations:
       "/media":
         ...
         expires: -1
   
       "/static":
         ...
         expires: -1
   ```

1. コードの変更を追加、コミット、プッシュします。

   ```bash
   git add -A && git commit -m "Set cache TTL for static files" && git push origin <branch-name>
   ```
