---
title: New Relic ログ管理
description: New Relic ログの使用方法を学ぶ
feature: Cloud, Logs, Observability
exl-id: d8afb5c0-9727-4123-8944-81cd6ad7cbb7
source-git-commit: ebe1746ee9fd08480da5ad07d7f1f8299ad9af7e
workflow-type: tm+mt
source-wordcount: '343'
ht-degree: 0%

---

# New Relic ログ管理

すべてのクラウドインフラストラクチャプロジェクトには、次のものが含まれます [New Relic ログ管理](https://docs.newrelic.com/docs/logs/get-started/get-started-log-management/). このサービスは、ステージング環境と実稼動環境のすべてのログデータを集計して、一元化されたログ管理ダッシュボードに表示するように事前に設定されています。

集計データには、次のログの情報が含まれます。

- すべて `ece-tools` とのアプリケーションログ `~/var/log` directory
- からのクラウドサービスのログ `var/log/platform/<project-ID>` directory
- Fastly CDN と WAF

プロジェクトがNew Relicに接続されている場合、New Relic ログサービスを使用して、次のようなタスクを実行できます。

- New Relic クエリを使用した集計ログデータの検索
- New Relic Logs アプリケーションを使用したログデータの視覚化
- カスタムグラフ、ダッシュボード、アラートの作成
- 単一ダッシュボードからのパフォーマンスの問題のトラブルシューティング

## ログデータの表示と分析

New Relic ログアプリケーションを使用して、集計ログデータを検索し、アプリケーション、インフラストラクチャ、CDN および WAF のエラーをトラブルシューティングします。 New Relic APM およびインフラストラクチャサービスから収集されたログデータを使用して、グラフ、ダッシュボード、アラートを作成できます。

**New Relic ログアプリケーションを使用するには**:

1. にログイン [New Relic アカウント](https://login.newrelic.com/login).

1. を選択 **ログ** エクスプローラーナビゲーションメニューから。

1. の上部でアカウントが選択されていることを確認します。 _すべてのログ_ 表示。

1. ログ クエリの時間範囲を選択します。

1. Cloud Services のインフラストラクチャログデータを確認するには（からのログ） `~/var/log/`）、クエリ文字列を入力します。 `has: "filePath"` が含まれる _ログを検索_ フィールド。 次に、 **[!UICONTROL Query logs]**.

   ログファイルの名前はに保存されます。 `filePath` 列（ログファイルへのフルパス）。

   ![Cloud project New Relic サービスのログデータ](../../assets/new-relic/var-log-query.png)

1. Fastly ログデータをレビューするには、クエリ文字列を入力します `has: "client_ip"` が含まれる _ログを検索_ フィールド。 次に、 **[!UICONTROL Query logs]**.

1. Fastly ログ結果を国コードでフィルタリングするには、次のボタンをクリックします **[!UICONTROL Add column]**&#x200B;を選択してから、 **[!UICONTROL geo_country_code]**.

   ![Cloud Project New Relic CDN ログ属性フィルター](../../assets/new-relic/fastly-countrycode-filter.png)

>[!TIP]
>
>からクエリビューを保存できます _保存したビュー_ ドロップダウン。 クリック **[!UICONTROL Create new]**&#x200B;を選択し、名前を入力してオプションを選択し、 **[!UICONTROL Save view]**.
>
>参照： [ログ管理の基本を学ぶ](https://docs.newrelic.com/docs/logs/get-started/get-started-log-management/) および [New Relicのクエリ言語の概要](https://docs.newrelic.com/docs/query-your-data/nrql-new-relic-query-language/get-started/introduction-nrql-new-relics-query-language/) 日 _New Relic ドキュメント_ サイト。
