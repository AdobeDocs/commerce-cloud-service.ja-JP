---
title: 静的ファイルのキャッシュを設定する
description: でキャッシュストレージオプションを設定する方法を説明します。 [!DNL Commerce] アプリケーション設定ファイル。
feature: Cloud, Configuration, Cache, SCD
exl-id: ca6db004-47fc-45ea-b8db-c0ecc3c2136b
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '113'
ht-degree: 0%

---

# 静的ファイルのキャッシュを設定する

メディアおよび静的ファイルのキャッシュ TTL（有効期間）は、 `.magento.app.yaml` 設定ファイルを `expires` キー。

>[!NOTE]
>
>実稼動環境を更新する前に、ステージング環境で変更をテストすることが重要です。 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) を参照してください。

1. TTL 時間（秒）を [`web` プロパティ](web-property.md) の `.magento.app.yaml` ファイル。 次の項目を追加できます。 `expires` の下の鍵 `locations` または `"/media"` および `"/static"`.

   キャッシュの有効期限が切れないようにするには、 `expires: -1` キーと値のペアとして渡される変数です。 次の例を参照してください。

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

1. コードの変更を追加、コミット、およびプッシュします。

   ```bash
   git add -A && git commit -m "Set cache TTL for static files" && git push origin <branch-name>
   ```
