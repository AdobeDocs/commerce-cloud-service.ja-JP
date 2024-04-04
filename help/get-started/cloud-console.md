---
title: "にログインします。 [!DNL Cloud Console]"
description: 詳しくは、 [!DNL Cloud Console] クラウド上のAdobe Commerceインフラストラクチャ用。
recommendations: noDisplay, catalog
last-substantial-update: 2024-02-06T00:00:00Z
exl-id: c19a36b6-e5e8-461c-a82c-68b7bf121999
source-git-commit: abe9aa36b907be8bdfdf42e6f28f1e1eac68fecf
workflow-type: tm+mt
source-wordcount: '389'
ht-degree: 0%

---


# にログインします。 [!DNL Cloud Console]

The [!DNL Cloud Console] は、コマースコードを作成、管理、デプロイするためのインタラクティブな方法を提供します。 The [!DNL Cloud Console] は、より最新で使いやすいエクスペリエンスで、将来のインターフェイス強化の基盤となります。

[にログインします。 [!DNL Cloud Console]](https://console.adobecommerce.com) をクリックして、プロジェクトリストを表示します。

![プロジェクトリスト](../assets/ui-allprojects-list.png)

## 機能

新機能や改善された機能には、次のものが含まれます。

- プロジェクトと環境の特性の概要を明確にする
- 並べ替え可能な履歴を含むアクティビティストリーム
- スタータープロジェクトの手動バックアップ管理と履歴
- ログ表示の強化
- 並べ替え可能なリスト
- 統合を追加するためのシンプルなフォームとガイダンス
- Web コンテンツアクセシビリティガイドライン (WCAG) への準拠

![[!DNL Cloud Console]](../assets/CloudConsole.svg)

新機能または改善された機能を次に示します。

| 機能 | 改善点 |
| -------------- | ----------------------------------- |
| [アクティビティストリーム](../cloud-guide/project/activity-stream.md) | 実行中、保留中または履歴アクションの並べ替え可能なリストを操作します。 アクティビティを選択してログを表示するか、実行中のビルドをキャンセルします。 |
| [プロジェクトと環境の概要](../cloud-guide/project/overview.md#project-overview) | プロジェクトを開き、プロジェクトの詳細と環境の一覧の概要を確認します。 環境の概要には、環境のステータス、アプリケーションアクセス、最近のアクティビティの詳細が表示されます。 |
| [統合フォーム](../cloud-guide/integrations/overview.md) | シンプルなフォームとガイダンスを使用して、Bitbucket やSlack通知などの統合を追加します。 |
| [プロジェクトリスト](../cloud-guide/project/overview.md#cloud-console) | The _すべてのプロジェクト_ 表示には、アクセス権を持つすべてのプロジェクトが一覧表示されます。 次をクリックできます。 **[!UICONTROL Show filters]** タイプ、地域、プランでプロジェクトリストをフィルタリングします。 |
| [変数の表示オプション](../cloud-guide/environment/variable-levels.md) | ビルドまたはランタイム中に、プロジェクトレベルまたは環境レベルの変数の表示を制限します。 |

<!-- The following are features yet to be activated:
| **Apps and services topology** | The Apps & Services topology is visible on Project and Environment views. This interactive diagram allows you to select a service and view the relationship details, such as name, type, version, port, and more. Click **[!UICONTROL View details]** to access the overview and configuration panel for each service. | -->

## コンソールに関する質問

**_スナップショット機能の場所_**?

の場合 [!DNL Starter] プロジェクトの場合、スナップショット機能は、現在はと呼ばれています。 _バックアップ_. 手動バックアップを作成する [!DNL Starter] 環境を [!DNL Cloud Console] または、Cloud CLI からスナップショットを作成します。 環境の管理者の役割が必要です。

プロジェクトナビゲーションバーから環境を選択します。 環境がアクティブである必要があります。 を選択します。 **[!UICONTROL Backups]** タブをクリックします。 現在、このオプションは Pro 環境では使用できません。

**_ここで、は環境用に設定されたルートのリストです。_**?

設定されたルートのリストは、 _サービス_ 」タブを使用します。

プロジェクトナビゲーションバーから環境を選択します。 を選択します。 **[!UICONTROL Services]** タブをクリックします。 The **発送担当** 「概要」には、設定済みのルートが表示されます。 現在、新しい [!DNL Cloud Console].

## アカウントメニュー

右上隅には、アカウントメニューが表示されます。 メニューの下向き矢印をクリックし、「 」を選択します。 **[!UICONTROL My Profile]**. Adobe Analytics の _マイプロファイル_ 表示：ユーザーの詳細と表示設定を制御し、 [セキュリティ認証](../cloud-guide/project/user-access.md#user-authentication-requirements), [API トークン](../cloud-guide/project/user-access.md#create-an-api-token)、および [SSH キー](../cloud-guide/development/secure-connections.md).
