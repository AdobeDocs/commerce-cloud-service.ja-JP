---
title: 正常性通知
description: クラウドインフラストラクチャプロジェクト上のAdobe Commerceでディスク容量の使用状況に関するSlack、メール、PagerDuty 通知を設定する方法について説明します。
feature: Cloud, Observability, Integration
exl-id: d3173098-78ed-42a8-aeb3-9ccbaccc4d32
source-git-commit: 1253d8357fd2554050d1775fefbc420a2097db5f
workflow-type: tm+mt
source-wordcount: '312'
ht-degree: 0%

---

# 正常性通知

クラウドインフラストラクチャー上のAdobe Commerceは、Starter 環境または Pro 統合環境のすべてのアプリケーションおよびサービスのディスクスペースの使用状況を監視します。 データベース ディスクの領域が不足すると、データが破損する可能性があります。 ヘルスステータスチェックは 5 分ごとに行われ、メールまたはその他の外部サービスで通知できます。 正常性通知には、ディスクの少ない次の 3 つの警告があります。

- **警告** – 使用可能なディスク領域が 20% 未満に減少
- **重大** – 使用可能なディスク領域が 10% 未満に減少
- **すべてをクリア** – ディスク不足イベントの後、使用可能なディスク領域が 20% を超えて返される

>[!NOTE]
>
>Pro 実稼動環境では、New Relicの Managed alerts for Adobe Commerce アラートポリシーを使用して、ディスク容量を監視できます。 参照： [管理されたアラートによるパフォーマンスの監視](../monitor/investigate-performance.md#monitor-performance-with-managed-alerts).

## メール通知

ヘルスメール統合には、送信元アドレスと少なくとも 1 つの受信者アドレスが必要です。 に同じメールアドレスを使用できます `from-address` および `recipients` 住所。 次の例では、2 人の受信者と 1 つのヘルスメール統合を登録します。

```bash
magento-cloud integration:add --type health.email --from-address you@example.com --recipients them@example.com --recipients others@example.com
```

## Slackチャネルの通知

Slackは、ボットと呼ばれるインタラクティブアプリを使用してチャットルームでメッセージを投稿する外部サービスです。 Slackで正常性の通知を受け取るには、まずカスタムを作成する必要があります [ボットユーザー](https://api.slack.com/bot-users) Slackグループ用。 チャネルまたはチャネルのボットユーザーを設定したら、 [ボットトークン](https://api.slack.com/docs/token-types#bot) 統合登録のためにSlackが提供します。 次の例では、ヘルス通知をSlackチャネルに登録します。

```bash
magento-cloud integration:add --type health.slack --token SLACK_BOT_TOKEN --channel '#slack-channel-name'
```

## PagerDuty 通知

PagerDuty は、オンコールチームメンバーに重要な問題を通知できる外部サービスです。 PagerDuty で正常性通知を受信する前に、 [PagerDuty 統合](https://developer.pagerduty.com/v2/docs/integrating) これは、Events API バージョン 2 を使用します。 統合キーを使用する _ルーティングキー_：統合を登録します。 次の例では、ルーティング キーを使用して PagerDuty の通知を登録します。

```bash
magento-cloud integration:add --type health.pagerduty --routing-key PAGERDUTY_ROUTING_KEY
```
