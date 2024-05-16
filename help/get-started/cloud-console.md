---
title: '"にログイン [!DNL Cloud Console]“'
description: について説明します [!DNL Cloud Console] クラウドインフラストラクチャー上のAdobe Commerceの場合。
recommendations: noDisplay, catalog
last-substantial-update: 2024-02-06T00:00:00Z
exl-id: c19a36b6-e5e8-461c-a82c-68b7bf121999
source-git-commit: abe9aa36b907be8bdfdf42e6f28f1e1eac68fecf
workflow-type: tm+mt
source-wordcount: '389'
ht-degree: 0%

---


# へのログイン [!DNL Cloud Console]

この [!DNL Cloud Console] は、Commerce コードを作成、管理、デプロイするためのインタラクティブなメソッドを提供します。 この [!DNL Cloud Console] はより現代的で使いやすいエクスペリエンスであり、今後のインターフェイス機能強化の基盤となります。

[にログインします [!DNL Cloud Console]](https://console.adobecommerce.com) をクリックしてプロジェクトリストを表示します。

![プロジェクトリスト](../assets/ui-allprojects-list.png)

## 機能

新機能または強化された機能は次のとおりです。

- プロジェクトと環境の特性の明確な概要
- 並べ替え可能な履歴を含むアクティビティストリーム
- スタータープロジェクトの手動バックアップ管理と履歴
- 強化されたログビュー
- 並べ替え可能リスト
- 統合を追加するためのシンプルなフォームとガイダンス
- Web コンテンツアクセシビリティガイドライン（WCAG）への準拠

![[!DNL Cloud Console]](../assets/CloudConsole.svg)

新機能または改善された機能は次のとおりです。

| 機能 | 改善点 |
| -------------- | ----------------------------------- |
| [アクティビティストリーム](../cloud-guide/project/activity-stream.md) | 実行中、保留中または履歴のアクションの並べ替え可能なリストを操作します。 アクティビティを選択してログを表示するか、実行中のビルドをキャンセルします。 |
| [プロジェクトと環境の概要](../cloud-guide/project/overview.md#project-overview) | プロジェクトを開き、プロジェクトの詳細と環境リストの概要を確認します。 環境の概要には、環境のステータス、アプリケーションへのアクセス、最近のアクティビティに関する詳細が表示されます。 |
| [統合フォーム](../cloud-guide/integrations/overview.md) | 簡単なフォームとガイダンスを使用して、Bitbucket やSlack通知などの統合機能を追加します。 |
| [プロジェクトリスト](../cloud-guide/project/overview.md#cloud-console) | この _すべてのプロジェクト_ 「表示」には、アクセス権を持つすべてのプロジェクトが一覧表示されます。 次のいずれかをクリックできます。 **[!UICONTROL Show filters]** さらに、タイプ、地域またはプランでプロジェクトリストをフィルタリングできます。 |
| [変数の表示オプション](../cloud-guide/environment/variable-levels.md) | ビルドまたは実行中に、プロジェクトレベルまたは環境レベルの変数の表示を制限します。 |

<!-- The following are features yet to be activated:
| **Apps and services topology** | The Apps & Services topology is visible on Project and Environment views. This interactive diagram allows you to select a service and view the relationship details, such as name, type, version, port, and more. Click **[!UICONTROL View details]** to access the overview and configuration panel for each service. | -->

## コンソールに関する質問

**_スナップショット機能について_**?

の場合 [!DNL Starter] プロジェクトのスナップショット機能は、 _バックアップ_. の手動バックアップを作成できます [!DNL Starter] からの環境 [!DNL Cloud Console] または、Cloud CLI からスナップショットを作成します。 環境の管理者の役割が必要です。

プロジェクトナビゲーションバーから環境を選択します。 環境がアクティブである必要があります。 「」を選択します **[!UICONTROL Backups]** タブ。 現在、このオプションは Pro 環境では使用できません。

**_ここで、は環境に設定されたルートのリストです_**?

設定されたルートのリストは、次にあります _サービス_ 環境の場合は tab キーを押します。

プロジェクトナビゲーションバーから環境を選択します。 「」を選択します **[!UICONTROL Services]** タブ。 この **発送担当** 概要：設定済みのルートが表示されます。 現在、新しいからルートを追加することはできません [!DNL Cloud Console].

## アカウントメニュー

右上隅にアカウントメニューがあります。 メニューの下矢印をクリックし、を選択します。 **[!UICONTROL My Profile]**. が含まれる _マイプロファイル_ 表示、ユーザーの詳細と表示設定を制御、管理できます。 [セキュリティ認証](../cloud-guide/project/user-access.md#user-authentication-requirements), [API トークン](../cloud-guide/project/user-access.md#create-an-api-token)、および [SSH キー](../cloud-guide/development/secure-connections.md).
