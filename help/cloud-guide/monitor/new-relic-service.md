---
title: New Relic サービス
description: クラウドインフラストラクチャプロジェクト上のAdobe Commerceで使用できるNew Relic サービスについて説明します。
feature: Cloud, Observability
last-substantial-update: 2023-09-06T00:00:00Z
exl-id: 613f0694-5338-4037-8ee4-ac5eca376159
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '369'
ht-degree: 0%

---

# New Relic サービスの概要

クラウドインフラストラクチャ上のすべてのAdobe Commerce プロジェクトには、パフォーマンスの監視とイベントの調査に役立つNew Relic サービスへのアクセスが含まれます [!DNL Commerce] アプリケーションとクラウドインフラストラクチャ

実稼動環境とステージング環境では、次のNew Relic機能を使用できます。

- [NEW RELIC APM](#new-relic-apm) （Pro および Starter）
- [New Relic インフラストラクチャ](#new-relic-infrastructure) （Pro のみ）
- [New Relic ログ管理](#new-relic-logs) （Pro のみ）

>[!INFO]
>
>その他のNew Relic機能は、Adobe Commerce プロジェクトでは使用できません。

## NEW RELIC APM

[APM （Application Performance Management）用New Relic](https://docs.newrelic.com/introduction-apm/) は、アプリケーションのインタラクションの分析と改善に役立つソフトウェア分析製品です。 New Relic APM は、クラウドインフラストラクチャプロジェクト上のすべてのAdobe Commerceで使用でき、次の機能が用意されています。

- **特定のトランザクションに焦点を当てる**- サイト内の主な顧客アクション（買い物かごへの追加、チェックアウト、支払いの処理など）をアクティブにマークおよび監視します。
- **データベースクエリの監視**- パフォーマンスに影響を与えるデータベース・クエリーを検索および監視します。
- **アプリ マップ**- サイト、拡張機能、外部サービス内のすべてのアプリケーション依存関係を表示します。
- **[!DNL Apdex]スコア** – パフォーマンスを評価し、問題を特定して発生時に通知するアラートを作成します。たとえば、フラッシュ販売や Web イベントの影響を受けるサイトのパフォーマンスなどです。 参照： [Apdex スコア](https://docs.newrelic.com/docs/apm/new-relic-apm/apdex/apdex-measure-user-satisfaction/).
- **Adobe Commerceの管理アラート** – このNew Relic アラートポリシーを使用して、業界のベストプラクティスに基づいてアプリケーションとインフラストラクチャのパフォーマンスを監視します。 参照： [Adobe Commerceの管理アラートのアラートポリシーを使用したパフォーマンスの監視](investigate-performance.md/#monitor-performance-with-managed-alerts).
- **デプロイメントの追跡** – 導入イベントを監視し、導入の全体的なパフォーマンスへの影響を分析します。 参照： [デプロイメントの追跡](track-deployments.md).

Adobe Commerce on cloud infrastructure プロジェクトには、New Relic APM サービスのソフトウェアとライセンスキーが含まれています。 追加のソフトウェアを購入またはインストールする必要はありません。

## New Relic インフラストラクチャ

Pro プロジェクトには、 [New Relicインフラストラクチャ（NRI）](https://docs.newrelic.com/docs/infrastructure/infrastructure-monitoring/get-started/get-started-infrastructure-monitoring/) サービス。アプリケーションデータおよび performance analytics と自動的に接続され、動的なサーバー監視を提供します。 このサービスは、実稼動環境とステージング環境で利用できます。

## New Relic ログ管理

すべてのクラウドインフラストラクチャプロジェクトには、次のものが含まれます [New Relic ログ管理](log-management.md). このサービスは、ステージング環境と実稼動環境のすべてのログデータを集計して、一元化されたログ管理ダッシュボードに表示するように事前に設定されています。
