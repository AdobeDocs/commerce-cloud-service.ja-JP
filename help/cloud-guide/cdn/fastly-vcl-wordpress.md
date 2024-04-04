---
title: CMS バックエンドにリクエストを再ルーティングする
description: Fastly エッジモジュールを使用して、Adobe Commerceストアからの受信リクエストを別の WordPress サイトに再ルーティングする方法を説明します。
feature: Cloud, Configuration, Routes
exl-id: 5bd9c56f-4412-4643-89b6-590a8ec65ac0
source-git-commit: 7a181af2149eef7bfaed4dd4d256b8fa19ae1dda
workflow-type: tm+mt
source-wordcount: '307'
ht-degree: 0%

---

# CMS バックエンドにリクエストを再ルーティングする

Fastly Edge Module を使用して、Adobe Commerceストアからの受信要求を別の WordPress サイトに再ルーティングします _その他の CMS/バックエンド統合_ を Edge 辞書に置き換えます。 同様のプロセスに従って、他の CMS バックエンドにリクエストを再ルーティングできます。

Fastly Edge モジュールを使用すると、VCL コードを手動で書き込み、Fastly API を使用してアップロードする代わりに、管理者からカスタム VCL コードを作成およびアップロードできます。

>[!NOTE]
>
>カスタム VCL 設定をステージング環境に追加し、実稼動環境で Fastly サービス設定を更新する前にテストすることをお勧めします。

**前提条件**

{{$include /help/_includes/vcl-snippet-prerequisites.md}}

**Adobe Commerceから WordPress に要求を再ルーティングするには**:

1. ステージング環境または実稼動環境で Fastly エッジモジュールを有効にします。

   - 管理者にログインします。

   - に移動します。 **ストア** /設定/ **設定** > **詳細** > **システム** > **フルページキャッシュ** > **Fastly 設定** > **詳細設定**.

   - 次の値を設定： **Fastly エッジモジュール** から **はい**.

   - 設定を保存します。

1. WordPress のバックエンドに再ルーティングする URL パスを指定します。

1. Fastly サービスを設定し、WordPress のバックエンドに要求を再ルーティングするためのカスタム VCL コードを作成するには、次のタスクを実行します。

   - Adobe Commerceストアからバックエンドに再ルーティングするパスを指定するエッジディクショナリを作成します。

   - WordPress バックエンドを Fastly サービス設定に追加し、URL の書き換えの要求条件を付けます。

   - を設定します。 _その他の CMS/バックエンド統合_ Edge モジュール：Adobe Commerceから WordPress バックエンドに URL を書き換えます。

     詳しい手順については、 [ファストリーエッジモジュール — その他の CMS/バックエンド統合](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/Edge-Modules/EDGE-MODULE-OTHER-CMS-INTEGRATION.md) （内） _Magento2 の Fastly CDN モジュール_ ドキュメント。

1. Fastly サービス設定を更新した後、Adobe Commerceストアをテストして、WordPress に対する指定された URL 要求が正しく再ルーティングされることを確認します。
