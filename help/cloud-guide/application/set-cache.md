---
title: 静的ファイルのキャッシュの設定
description: アプリケーション設定ファイルでキャッシュストレージオプション  [!DNL Commerce]  設定する方法を説明します。
feature: Cloud, Configuration, Cache, SCD
exl-id: ca6db004-47fc-45ea-b8db-c0ecc3c2136b
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '113'
ht-degree: 0%

---

# 静的ファイルのキャッシュの設定

メディアおよび静的ファイルのキャッシュ TTL （time-to-live）は、`expires` キーを使用して `.magento.app.yaml` 設定ファイルに設定されます。

>[!NOTE]
>
>実稼動環境を更新する前に、ステージング環境で変更をテストすることが重要です。 [Adobe Commerce サポートチケットを送信 ](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) して、これらの環境の設定を更新するためのヘルプを入手します。

1. `.magento.app.yaml` ファイルの [`web` プロパティに TTL 時間（秒単位） ](web-property.md) 指定します。 `expires` キーは `locations` の下、または `"/media"` と `"/static"` の下に追加できます。

   キャッシュの有効期限が切れないようにするには、`expires: -1` のキーと値のペアを使用します。 次の例を参照してください。

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
