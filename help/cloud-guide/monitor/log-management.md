---
title: New Relic Log Management
description: New Relicログの使用方法を説明します
feature: Cloud, Logs, Observability
exl-id: d8afb5c0-9727-4123-8944-81cd6ad7cbb7
source-git-commit: ebe1746ee9fd08480da5ad07d7f1f8299ad9af7e
workflow-type: tm+mt
source-wordcount: '343'
ht-degree: 0%

---

# New Relic Log Management

すべてのクラウドインフラストラクチャプロジェクトには、以下が含まれます [New Relic Log Management](https://docs.newrelic.com/docs/logs/get-started/get-started-log-management/). このサービスは、ステージング環境と実稼動環境からすべてのログデータを集計し、一元化されたログ管理ダッシュボードに表示するように事前設定されています。

集計されたデータには、次のログの情報が含まれます。

- すべて `ece-tools` およびからのアプリケーションログ `~/var/log` directory
- からのクラウドサービスのログ `var/log/platform/<project-ID>` directory
- Fastly CDN と WAF

プロジェクトがNew Relicに接続されている場合、New Relic Logs サービスを使用して、次のようなタスクを完了できます。

- New Relicクエリを使用して集計ログデータを検索する
- New Relic Logs アプリケーションでのログデータの視覚化
- カスタムグラフ、ダッシュボードおよびアラートの作成
- 単一のダッシュボードからパフォーマンスの問題をトラブルシューティングする

## ログデータの表示と分析

New Relic Logs アプリケーションを使用して、集計されたログデータを検索し、アプリケーション、インフラストラクチャ、CDN、WAF のエラーのトラブルシューティングをおこないます。 New Relic APM およびインフラストラクチャサービスから収集されたログデータを使用して、グラフ、ダッシュボード、アラートを作成できます。

**New Relic Logs アプリケーションを使用するには**:

1. にログインします。 [New Relicアカウント](https://login.newrelic.com/login).

1. 選択 **ログ** エクスプローラーナビゲーションメニューから。

1. アカウントが選択されていることを _すべてのログ_ 表示。

1. ログクエリの時間範囲を選択します。

1. クラウドサービスのインフラストラクチャログデータを確認するには、次の手順に従います（ログを次から）。 `~/var/log/`)、クエリ文字列を入力します。 `has: "filePath"` （内） _ログを検索_ フィールドに入力します。 次に、「 **[!UICONTROL Query logs]**.

   ログファイルの名前は、 `filePath` 列には、ログファイルへのフルパスが含まれます。

   ![クラウドプロジェクトのNew Relicサービスログデータ](../../assets/new-relic/var-log-query.png)

1. Fastly ログデータを確認するには、クエリ文字列を入力します `has: "client_ip"` （内） _ログを検索_ フィールドに入力します。 次に、「 **[!UICONTROL Query logs]**.

1. 国コードで Fastly ログの結果をフィルターするには、 **[!UICONTROL Add column]**&#x200B;を選択し、「 **[!UICONTROL geo_country_code]**.

   ![Cloud project New Relic CDN ログ属性フィルター](../../assets/new-relic/fastly-countrycode-filter.png)

>[!TIP]
>
>クエリビューは、 _保存済みビュー_ ドロップダウン。 クリック **[!UICONTROL Create new]**、名前を入力し、オプションを選択して、 **[!UICONTROL Save view]**.
>
>詳しくは、 [ログ管理の概要](https://docs.newrelic.com/docs/logs/get-started/get-started-log-management/) および [New Relicクエリ言語の概要](https://docs.newrelic.com/docs/query-your-data/nrql-new-relic-query-language/get-started/introduction-nrql-new-relics-query-language/) の _New Relic Docs_ サイト。
