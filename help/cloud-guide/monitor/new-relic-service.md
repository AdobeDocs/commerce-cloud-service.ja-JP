---
title: New Relicサービス
description: Adobe Commerce on cloud インフラストラクチャプロジェクトで使用できるNew Relicサービスについて説明します。
feature: Cloud, Observability
last-substantial-update: 2023-09-06T00:00:00Z
exl-id: 613f0694-5338-4037-8ee4-ac5eca376159
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '369'
ht-degree: 0%

---

# New Relicサービスの概要

クラウドインフラストラクチャ上のすべてのAdobe Commerceプロジェクトには、New Relicサービスへのアクセスが含まれており、パフォーマンスの監視と [!DNL Commerce] アプリケーションおよびクラウドインフラストラクチャ。

次のNew Relic機能は、実稼動環境とステージング環境で使用できます。

- [New Relic APM](#new-relic-apm) （Pro および Starter）
- [New Relic Infrastructure](#new-relic-infrastructure) （Pro のみ）
- [New Relic Log Management](#new-relic-logs) （Pro のみ）

>[!INFO]
>
>その他のNew Relic機能は、Adobe Commerceプロジェクトでは使用できません。

## New Relic APM

[New Relic（アプリケーションパフォーマンス管理）](https://docs.newrelic.com/introduction-apm/) は、アプリケーションの操作を分析および改善するのに役立つソフトウェア分析製品です。 New Relic APM は、クラウドインフラストラクチャプロジェクト上のすべてのAdobe Commerceで使用でき、次の機能を提供します。

- **特定のトランザクションに焦点を当てる** — 買い物かごへの追加、チェックアウト、支払いの処理など、サイト内の主な顧客行動を積極的にマークし、監視します。
- **データベースクエリの監視** — パフォーマンスに影響するデータベースクエリを検索および監視します。
- **アプリマップ** — サイト、拡張機能、および外部サービス内のアプリケーションの依存関係をすべて表示します。
- **[!DNL Apdex]スコア** — パフォーマンスを評価し、Flash 販売や Web イベントの影響を受けるサイトのパフォーマンスなど、問題が発生したタイミングを通知するアラートを作成します。 詳しくは、 [Apdex スコア](https://docs.newrelic.com/docs/apm/new-relic-apm/apdex/apdex-measure-user-satisfaction/).
- **Adobe Commerce用管理アラート** — このNew Relicのアラートポリシーを使用して、業界のベストプラクティスに基づいてアプリケーションとインフラストラクチャのパフォーマンスを監視します。 詳しくは、 [Adobe Commerceアラートポリシーの管理されたアラートでパフォーマンスを監視する](investigate-performance.md/#monitor-performance-with-managed-alerts).
- **デプロイメントの追跡** — デプロイメントイベントを監視し、全体的なパフォーマンスに対するデプロイメントの影響を分析します。 詳しくは、 [デプロイメントの追跡](track-deployments.md).

Adobe Commerce on cloud infrastructure プロジェクトには、New Relic APM サービス用のソフトウェアとライセンスキーが含まれています。 追加のソフトウェアを購入またはインストールする必要はありません。

## New Relic Infrastructure

Pro プロジェクトには、 [New Relic Infrastructure (NRI)](https://docs.newrelic.com/docs/infrastructure/infrastructure-monitoring/get-started/get-started-infrastructure-monitoring/) サービス。アプリケーションデータおよびパフォーマンス分析と自動的に接続し、動的なサーバー監視を提供します。 このサービスは、Pro 実稼動環境とステージング環境で使用できます。

## New Relic Log Management

すべてのクラウドインフラストラクチャプロジェクトには、以下が含まれます [New Relic Log Management](log-management.md). このサービスは、ステージング環境と実稼動環境からすべてのログデータを集計し、一元化されたログ管理ダッシュボードに表示するように事前設定されています。
