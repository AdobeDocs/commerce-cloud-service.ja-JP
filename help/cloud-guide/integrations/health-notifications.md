---
title: ヘルス通知
description: クラウドインフラストラクチャプロジェクト上のAdobe Commerceのディスク領域使用量に関するSlack、E メール、PagerDuty の通知を設定する方法を説明します。
feature: Cloud, Observability, Integration
exl-id: d3173098-78ed-42a8-aeb3-9ccbaccc4d32
source-git-commit: 1253d8357fd2554050d1775fefbc420a2097db5f
workflow-type: tm+mt
source-wordcount: '312'
ht-degree: 0%

---

# ヘルス通知

クラウドインフラストラクチャ上のAdobe Commerceは、スターター環境または Pro 統合環境内のすべてのアプリケーションおよびサービスのディスク容量の使用状況を監視します。 空き容量が不足しているデータベースディスクは、データの破損の原因となる場合があります。 ヘルスステータスチェックは 5 分ごとに実行され、E メールや他の外部サービスで通知できます。 ヘルス通知には、次の 3 つのディスク不足警告があります。

- **警告** — 使用可能なディスク容量が 20%を下回る
- **重大** — 使用可能なディスク容量が 10%を下回る
- **すべてクリア** — 使用可能なディスク容量が、ディスク不足イベントの後、20%を超えて戻る

>[!NOTE]
>
>Pro 実稼動環境では、New RelicのAdobe Commerce用管理アラートポリシーを使用して、ディスク容量を監視できます。 詳しくは、 [管理されたアラートでパフォーマンスを監視する](../monitor/investigate-performance.md#monitor-performance-with-managed-alerts).

## 電子メール通知

ヘルス E メールの統合には、送信元アドレスと、少なくとも 1 つの受信者アドレスが必要です。 同じ電子メールアドレスを `from-address` そして `recipients` 住所。 次の例では、2 人の受信者とのヘルス E メールの統合を登録します。

```bash
magento-cloud integration:add --type health.email --from-address you@example.com --recipients them@example.com --recipients others@example.com
```

## Slackチャネル通知

Slackは、ボットと呼ばれるインタラクティブなアプリを使用して、チャットルームにメッセージを投稿する外部サービスです。 正常性通知をSlackで受け取る前に、カスタムを作成する必要があります [ボットユーザー](https://api.slack.com/bot-users) Slackグループ チャネル（複数可）のボットユーザーを設定したら、 [ボットトークン](https://api.slack.com/docs/token-types#bot) 統合を登録するためにSlackが提供します。 次の例では、ヘルス通知をSlackチャネルに登録します。

```bash
magento-cloud integration:add --type health.slack --token SLACK_BOT_TOKEN --channel '#slack-channel-name'
```

## PagerDuty 通知

PagerDuty は、オンコールチームのメンバーに重要な問題を通知できる外部サービスです。 PagerDuty で正常性通知を受け取る前に、 [PagerDuty の統合](https://developer.pagerduty.com/v2/docs/integrating) イベント API バージョン 2 を使用する 統合キーを使用する。または _ルーティングキー_、統合を登録します。 次の例では、ルーティングキーを使用して PagerDuty の通知を登録します。

```bash
magento-cloud integration:add --type health.pagerduty --routing-key PAGERDUTY_ROUTING_KEY
```
