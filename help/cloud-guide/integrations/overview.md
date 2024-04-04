---
title: 統合の概要
description: Adobe Commerce on cloud infrastructure プロジェクトのサードパーティ統合オプションについて説明します。
role: Developer
feature: Cloud, Integration
last-substantial-update: 2024-02-06T00:00:00Z
exl-id: 2dddba73-5b88-4b5d-a0e1-2f1c1f52354c
source-git-commit: abe9aa36b907be8bdfdf42e6f28f1e1eac68fecf
workflow-type: tm+mt
source-wordcount: '564'
ht-degree: 0%

---

# 統合の概要

統合は、Git ホスティングやSlackボットなどの外部サービスを使用し、GitHub でのコードレビュープルリクエスト機能の使用など、現在の開発プロセスを維持する場合に役立ちます。 Adobe Commerce on cloud infrastructure プロジェクトに、次の統合を追加できます。

![統合](/help/assets/integrations.png)

>[!BEGINTABS]

>[!TAB CLI]

**Cloud CLI を使用して統合を追加するには、以下を実行します。**:

次のコマンドは、新しい統合のタイプとオプションを選択するためのインタラクティブプロンプトを開始します。

```bash
magento-cloud integration:add
```

**プロジェクトに設定された統合をリストするには**:

```bash
magento-cloud integration:list
```

レスポンスのサンプル：

```terminal
+----------+--------------+---------------------------------------------------------------------------+
| ID       | Type         | Summary                                                                   |
+----------+--------------+---------------------------------------------------------------------------+
| <int-id> | bitbucket    | Repository: user/magento-int                                              |
|          |              | Hook URL:                                                                 |
|          |              | https://magento-url.cloud/api/projects/projectID/integrations/int-ID/hook |
| <int-id> | health.email | From: you@example.com                                                     |
|          |              | To: them@example.com                                                      |
+----------+--------------+---------------------------------------------------------------------------+
```

>[!TAB コンソール]

**を使用して統合を追加するには、以下を実行します。[!DNL Cloud Console]**:

1. In _プロジェクト設定_&#x200B;をクリックし、 **[!UICONTROL Integrations]**.

1. 統合タイプをクリックするか、「 **[!UICONTROL Add integration]**.

1. 統合タイプの選択と設定の手順を順を追って説明します。

1. 統合を追加すると、統合ビューのリストに表示されます。

>[!ENDTABS]

## コマース Web フック

クラウドプロジェクト内の Commerce Web フックは、 [ENABLE_WEBHOOKS グローバル変数](../environment/variables-global.md#enable_webhooks). コマース Web フックは、コマース生成イベントに応じて、リクエストを外部サーバーに送信します。 The [_ウェブフックガイド_](https://developer.adobe.com/commerce/extensibility/webhooks) では、この機能の詳細について説明します。

## 汎用ウェブフック

カスタム Webhook 統合を使用して、Cloud のインフラストラクチャとリポジトリのイベントを取得し、レポートできます。 `POST` への JSON メッセージ _webhook_ URL。

**Webhook URL を追加するには、次の構文を使用します。**:

```bash
magento-cloud integration:add --type=webhook --url=https://hook-url.example.com
```

- `type`— `webhook` 統合タイプ。
- `url`—JSON メッセージを受け取ることのできる Webhook URL を指定します。

このサンプル応答には、統合をカスタマイズする機会を提供する一連のプロンプトが表示されます。 デフォルト（空白）の応答を使用すると、プロジェクト内のすべての環境のすべてのイベントに関するメッセージが送信されます。

特定のレポートに合わせて統合をカスタマイズできます [イベント](#events-to-report)（コードをブランチにプッシュするなど）。 例えば、 `environment.push` イベントを使用して、ユーザーがコードをブランチにプッシュしたときにメッセージを送信できます。

```terminal
Events to report (--events)
A list of events to report, e.g. environment.push
Default: *
Enter comma-separated values (or leave this blank)
>
```

イベントのレポートは、 `pending`, `in_progress`または `complete` 状態：

```terminal
States to report (--states)
A list of states to report, e.g. pending, in_progress, complete
Default: complete
Enter comma-separated values (or leave this blank)
>
```

また、次のことが可能です。 _含める_ または _除外_ 特定の環境向けのメッセージ：

```terminal
Included environments (--environments)
The environment IDs to include
Default: *
Enter comma-separated values (or leave this blank)
>

Excluded environments (--excluded-environments)
The environment IDs to exclude
Enter comma-separated values (or leave this blank)
>
```

統合が完了すると、次の値の概要が表示されます。

```terminal
Created integration integration-ID (type: webhook)
+-----------------------+------------------------------+
| Property              | Value                        |
+-----------------------+------------------------------+
| id                    | integration-ID               |
| type                  | webhook                      |
| events                | - '*'                        |
| environments          | - '*'                        |
| excluded_environments | {  }                         |
| states                | - complete                   |
| url                   | https://hook-url.example.com |
+-----------------------+------------------------------+
```

### 既存の統合を更新

既存の統合を更新できます。 例えば、次の状態から `complete` から `pending` 次の方法を使用します。

```bash
magento-cloud integration:update --states=pending <int-id>
```

レスポンスのサンプル：

```terminal
Integration integration-ID (webhook) updated
+-----------------------+------------------------------+
| Property              | Value                        |
+-----------------------+------------------------------+
| id                    | integration-ID               |
| type                  | webhook                      |
| events                | - '*'                        |
| environments          | - '*'                        |
| excluded_environments | {  }                         |
| states                | - pending                    |
| url                   | https://hook-url.example.com |
+-----------------------+------------------------------+
```

### レポートするイベント

| イベント | 説明 |
| ----- | :-----------|
| `environment.access.add` | ユーザーに環境へのアクセス権が付与されました |
| `environment.access.remove` | ユーザーが環境から削除されました |
| `environment.activate` | 環境でブランチが「アクティブ化」されました |
| `environment.backup` | ユーザーがスナップショットをトリガーしました |
| `environment.branch` | 管理コンソールを使用してブランチが作成されました |
| `environment.deactivate` | ブランチが「非アクティブ化」されました。 コードはまだ存在しますが、環境は破壊されました |
| `environment.delete` | ブランチが削除されました |
| `environment.initialize` | The `master` 最初のコミットで初期化されたプロジェクトのブランチ |
| `environment.merge` | アクティブなブランチが管理コンソールまたは API を使用して結合されました |
| `environment.push` | ユーザーがコードをブランチにプッシュしました |
| `environment.restore` | ユーザーがスナップショットをリストアしました |
| `environment.route.create` | 管理コンソールを使用してルートが作成されました |
| `environment.route.delete` | 管理コンソールを使用してルートが削除されました |
| `environment.route.update` | 管理コンソールを使用してルートが変更されました |
| `environment.subscription.update` | The `master` 配信登録が変更されたので、環境がサイズ変更されましたが、コンテンツの変更はありません |
| `environment.synchronize` | 環境の親環境からデータまたはコードが再コピーされました |
| `environment.update.http_access` | 環境の HTTP アクセスルールが変更されました |
| `environment.update.restrict_robots` | block-all-robots 機能が有効または無効になっています |
| `environment.update.smtp` | 環境で E メールの送信が有効または無効になっています |
| `environment.variable.create` | 変数が作成されました |
| `environment.variable.delete` | 変数が削除されました |
| `environment.variable.update` | 変数が変更されました |
| `project.domain.create` | ドメインが作成され、プロジェクトに追加されました |
| `project.domain.delete` | プロジェクトに関連付けられているドメインが削除されました |
| `project.domain.update` | プロジェクトに関連付けられているドメインが更新されました |
