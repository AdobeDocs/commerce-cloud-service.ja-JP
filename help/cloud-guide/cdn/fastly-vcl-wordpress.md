---
title: CMS バックエンドへのリクエストの再ルーティング
description: Fastly エッジモジュールを使用して、Adobe Commerce ストアから受信したリクエストを別の WordPress サイトに再ルーティングする方法を説明します。
feature: Cloud, Configuration, Routes
exl-id: 5bd9c56f-4412-4643-89b6-590a8ec65ac0
source-git-commit: 7a181af2149eef7bfaed4dd4d256b8fa19ae1dda
workflow-type: tm+mt
source-wordcount: '307'
ht-degree: 0%

---

# CMS バックエンドへのリクエストの再ルーティング

Fastly Edge モジュールを使用して、Adobe Commerce ストアから受信したリクエストを別の WordPress サイトに再ルーティングする _その他の CMS/バックエンドの統合_ エッジディクショナリを使用。 同様のプロセスに従って、他の CMS バックエンドにリクエストを再ルーティングできます。

VCL コードを手動で書いて Fastly API を使用してアップロードするのではなく、Fastly エッジモジュールを使用して、カスタム VCL コードを作成して管理者からアップロードします。

>[!NOTE]
>
>カスタム VCL 設定をステージング環境に追加して、実稼動環境で Fastly サービス設定を更新する前にテストできるようにすることをお勧めします。

**前提条件**

{{$include /help/_includes/vcl-snippet-prerequisites.md}}

**Adobe Commerceから WordPress にリクエストを再ルーティングするには**:

1. ステージング環境または実稼動環境で Fastly エッジモジュールを有効にします。

   - 管理者にログインします。

   - に移動します。 **ストア** > 設定 > **設定** > **詳細** > **システム** > **フルページキャッシュ** > **Fastly 設定** > **詳細設定**.

   - の値を設定 **Fastly エッジモジュール** 対象： **はい**.

   - 設定を保存します。

1. WordPress バックエンドに再ルーティングする URL パスを特定します。

1. 次のタスクを実行して Fastly サービスを設定し、WordPress バックエンドにリクエストを再ルーティングするためのカスタム VCL コードを作成します。

   - Adobe Commerce ストアからバックエンドへのルートを変更するパスを指定する Edge Dictionary を作成します。

   - Fastly サービス設定に WordPress バックエンドを追加し、URL 書き換えのリクエスト条件を添付します。

   - の設定 _その他の CMS/バックエンドの統合_ Adobe Commerceから WordPress バックエンドへの URL 書き換えを処理する Edge モジュール。

     手順について詳しくは、 [Fastly エッジモジュール – その他の CMS/バックエンド統合](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/Edge-Modules/EDGE-MODULE-OTHER-CMS-INTEGRATION.md) が含まれる _Magento 2 用 Fastly CDN モジュール_ ドキュメント。

1. Fastly サービス設定を更新した後、Adobe Commerce ストアをテストして、WordPress に指定した URL リクエストが正しく再ルーティングされていることを確認します。
