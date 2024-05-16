---
title: 統合の概要
description: クラウドインフラストラクチャプロジェクト上のAdobe Commerceのサードパーティ統合オプションについて説明します。
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

統合は、Git ホスティングやSlackボットなどの外部サービスを使用したり、GitHub のコードリビュープルリクエスト機能の使用など、現在の開発プロセスを維持管理したりするのに役立ちます。 クラウドインフラストラクチャー上のAdobe Commerce プロジェクトに次の統合を追加できます。

![統合](/help/assets/integrations.png)

>[!BEGINTABS]

>[!TAB CLI]

**Cloud CLI を使用して統合を追加するには**:

次のコマンドを実行すると、新しい統合のタイプとオプションを選択するための対話型プロンプトが表示されます。

```bash
magento-cloud integration:add
```

**プロジェクトに設定されている統合を一覧表示するには、次の手順に従います**:

```bash
magento-cloud integration:list
```

応答の例：

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

**を使用して統合を追加するには[!DNL Cloud Console]**:

1. 対象： _プロジェクト設定_&#x200B;を選択し、 **[!UICONTROL Integrations]**.

1. 統合タイプをクリックするか、 **[!UICONTROL Add integration]**.

1. 統合タイプの選択と設定の手順について説明します。

1. 統合を追加すると、統合ビューのリストに表示されます。

>[!ENDTABS]

## Commerce Webhook

を使用して、クラウドプロジェクトにCommerce Webhook を設定できます [ENABLE_WEBHOOK グローバル変数](../environment/variables-global.md#enable_webhooks). Commerce Webhook は、Commerceが生成したイベントに応答して、外部サーバーにリクエストを送信します。 この [_Webhook ガイド_](https://developer.adobe.com/commerce/extensibility/webhooks) では、この機能について詳しく説明します。

## 汎用 Webhook

へのカスタム Webhook 統合を使用すると、クラウドインフラストラクチャーとリポジトリのイベントを取得してレポートできます `POST` に対する JSON メッセージ _webhook_ URL。

**Webhook URL を追加するには、次の構文を使用します**:

```bash
magento-cloud integration:add --type=webhook --url=https://hook-url.example.com
```

- `type` – を指定する `webhook` 統合タイプ。
- `url`- JSON メッセージを受信できる Webhook URL を指定します。

応答のサンプルでは、統合をカスタマイズする機会を提供する一連のプロンプトを示しています。 デフォルト（空白）の応答を使用すると、プロジェクト内のすべての環境のすべてのイベントに関するメッセージが送信されます。

レポート固有になるように統合をカスタマイズできます [イベント](#events-to-report)ブランチにコードをプッシュするなど。 例えば、 `environment.push` ユーザーがブランチにコードをプッシュする際にメッセージを送信するイベントです。

```terminal
Events to report (--events)
A list of events to report, e.g. environment.push
Default: *
Enter comma-separated values (or leave this blank)
>
```

でイベントの報告を選択できます。 `pending`, `in_progress`、または `complete` 都道府県：

```terminal
States to report (--states)
A list of states to report, e.g. pending, in_progress, complete
Default: complete
Enter comma-separated values (or leave this blank)
>
```

また、次のことができます _次を含める_ または _除外_ 特定の環境向けのメッセージ：

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

統合が完了すると、値の概要が表示されます。

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

既存の統合は更新できます。 例えば、の状態を次のように変更します。 `complete` 対象： `pending` 次を使用します。

```bash
magento-cloud integration:update --states=pending <int-id>
```

応答の例：

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

### 報告するイベント

| イベント | 説明 |
| ----- | :-----------|
| `environment.access.add` | ユーザーに環境へのアクセス権が付与されました |
| `environment.access.remove` | ユーザーが環境から削除されました |
| `environment.activate` | 環境を含むブランチが「アクティブ化」されました |
| `environment.backup` | ユーザーがスナップショットをトリガーしました |
| `environment.branch` | 管理コンソールを使用してブランチが作成されました |
| `environment.deactivate` | ブランチが「非アクティブ化」されました。 コードはまだそこにありますが、環境は破壊されました |
| `environment.delete` | 分岐が削除されました |
| `environment.initialize` | この `master` 最初のコミットで初期化されたプロジェクトのブランチ |
| `environment.merge` | アクティブなブランチが、管理コンソールまたは API を使用して統合されました |
| `environment.push` | ユーザーがブランチにコードをプッシュした |
| `environment.restore` | ユーザーがスナップショットを復元しました |
| `environment.route.create` | 管理コンソールを使用してルートが作成されました |
| `environment.route.delete` | 管理コンソールを使用してルートが削除されました |
| `environment.route.update` | 管理コンソールを使用してルートが変更されました |
| `environment.subscription.update` | この `master` サブスクリプションが変更されたので環境のサイズが変更されましたが、コンテンツの変更はありません |
| `environment.synchronize` | 環境が親環境からデータまたはコードをコピーした |
| `environment.update.http_access` | 環境の HTTP アクセスルールが変更されました |
| `environment.update.restrict_robots` | ブロック オールロボット機能が有効または無効になっています |
| `environment.update.smtp` | 環境に対するメールの送信が有効または無効になっています |
| `environment.variable.create` | 変数が作成されました |
| `environment.variable.delete` | 変数が削除されました |
| `environment.variable.update` | 変数が変更されました |
| `project.domain.create` | ドメインが作成され、プロジェクトに追加されました |
| `project.domain.delete` | プロジェクトに関連付けられているドメインが削除されました |
| `project.domain.update` | プロジェクトに関連付けられているドメインが更新されました |
