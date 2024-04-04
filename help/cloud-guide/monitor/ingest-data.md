---
title: データ取り込み
description: New Relicでコマースデータの取り込みを表示および管理する方法について説明します。
feature: Cloud, Observability
exl-id: f88bf20c-604b-4986-b71c-bb726b2f00b8
source-git-commit: bf3debc5986d51a721537b52ffced58b2ee521ea
workflow-type: tm+mt
source-wordcount: '210'
ht-degree: 0%

---

# データ取り込み

New Relicは、効率的な監視と分析を実現するために、豊富なデータに依存していますが、大規模なデータセットは、タイムリーな結果、パフォーマンス、コンプライアンスに影響を与える可能性があります。 このトピックでは、データ取り込みの管理と、最も効果的にデータを絞り込むための戦略に関するガイダンスを提供します。

New Relic _データ管理_ データ・ソース別のプラン使用状況の要約を表示します。

**取り込みデータおよび取り込みソースを表示するには**:

1. New Relicのユーザーメニューで、 **[!UICONTROL Manage your data]**.
1. クリック **[!UICONTROL Data management]** （内） _管理_ リスト。

   ![データ管理](../../assets/new-relic/data-ingestion.png)

   The **[!UICONTROL Data ingestion]** 「 」タブには、1 日に取り込まれたデータとデータのソースが表示されます。
「データ保持」タブには、データの保存期間が表示され、制御されます。

1. を選択します。 **[!UICONTROL Limits]** 」タブに移動し、アカウントの制限を確認します。

Adobe Commerceのデータソースには、次のものが含まれます。

- **APM イベント** — グラフとダッシュボードで使用されるイベントデータ
- **インフラ**—CPU、ストレージ、ネットワークなどのプロセスとホストのメトリック
- **ログ**—CDN、APM、アプリケーションサーバーのログ

ログデータは取り込みの大部分に寄与します。 方法を見る [ログデータの表示と分析](log-management.md#view-and-analyze-log-data) また、Adobe担当者と協力して、データの取り込みと保持のニーズに応える戦略を策定します。 詳細を表示： [データ取り込みの管理](https://docs.newrelic.com/docs/data-apis/manage-data/manage-data-coming-new-relic/) （内） _New Relicドキュメント_.
