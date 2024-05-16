---
title: データ取り込み
description: New RelicでCommerceのデータ取得を表示および管理する方法について説明します。
feature: Cloud, Observability
exl-id: f88bf20c-604b-4986-b71c-bb726b2f00b8
source-git-commit: bf3debc5986d51a721537b52ffced58b2ee521ea
workflow-type: tm+mt
source-wordcount: '210'
ht-degree: 0%

---

# データ取り込み

New Relicは、効果的な監視と分析を提供するためにリッチデータに依存していますが、大規模なデータセットは、タイムリーな結果、パフォーマンス、コンプライアンスに影響を与える可能性があります。 このトピックでは、データの取り込みを管理し、データを最も効果的に調整する戦略に関するガイダンスを提供します。

New Relicはを提供します。 _データ管理_ データ・ソース別の計画使用を要約するビュー。

**取り込んだデータとソースを表示するには**:

1. New Relicのユーザーメニューで、 **[!UICONTROL Manage your data]**.
1. クリック **[!UICONTROL Data management]** が含まれる _管理_ リスト。

   ![データ管理](../../assets/new-relic/data-ingestion.png)

   この **[!UICONTROL Data ingestion]** タブには、その日に取り込まれたデータと、そのデータのソースが表示されます。
データ保持タブには、データが保存される期間を表示および制御します。

1. 「」を選択します **[!UICONTROL Limits]** タブをクリックして、アカウントの制限を確認します。

Adobe Commerceのデータソースには、次のものが含まれます。

- **APM イベント**- グラフおよびダッシュボードで使用するイベントデータ
- **インフラストラクチャ**—CPU、ストレージ、ネットワークなどのプロセスおよびホストのメトリック
- **ログ**—CDN、APM およびアプリケーションサーバーのログ

ログデータは、取り込みの大部分に貢献します。 方法を参照する [ログデータの表示と分析](log-management.md#view-and-analyze-log-data) Adobe担当者と協力して、データの取り込みと保持のニーズに対する戦略を立てます。 詳細を読む： [データ取り込みの管理](https://docs.newrelic.com/docs/data-apis/manage-data/manage-data-coming-new-relic/) が含まれる _New Relic ドキュメント_.
