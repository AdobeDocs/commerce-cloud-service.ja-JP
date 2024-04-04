---
title: 自動スケーリング
description: クラウドインフラストラクチャ上のAdobe Commerceが、リソースの需要に応えて拡張する方法を説明します。
feature: Cloud, Auto Scaling
topic: Architecture
exl-id: 2ba49c55-d821-4934-965f-f35bd18ac95f
source-git-commit: c61d711b1041ecf76ec6468cd225a34fd77c24b1
workflow-type: tm+mt
source-wordcount: '549'
ht-degree: 0%

---

# 自動スケーリング

自動スケーリングにより、クラウドインフラストラクチャにリソースが自動的に追加または削除され、最適なパフォーマンスと妥当なコストが維持されます。 現在、この機能は、 [拡張アーキテクチャ](scaled-architecture.md).

## Web サーバーノード

The [web 層](scaled-architecture.md#web-tier) は、プロセスリクエストの増加やトラフィック要件の増加に対応するために拡大縮小されます。 現在、自動スケーリング機能では、Web サーバーノードを追加または削除して、水平方向にのみスケールします。

自動スケーリングイベントは、CPU 使用率とトラフィックが事前に定義されたしきい値に達した場合に発生します。

- **追加されたノード** — すべてのアクティブな Web ノードの CPU/コアは、1 分間で 75%の容量になり、5 分間連続でトラフィックが 20%増加しています。
- **削除されたノード** — すべてのアクティブな Web ノードの CPU/コアは、60 %で 20 分間読み込まれます。 ノードは、追加された順序で削除されます。

各商人の契約資源制限に基づいて、最小および最大のしきい値を決定し、設定することで、無限スケーリングのリスクを低減します。

## New Relicでのしきい値の監視

以下を使用すると、 [New Relicサービス](../monitor/new-relic-service.md) ：ホスト数や CPU 使用率など、特定のしきい値を監視する。 次のNew Relicクエリでは、 `cluster-id` 例えば、

>[!TIP]
>
>クエリの作成に関するリファレンスについては、 [NRQL 構文、句、および関数](https://docs.newrelic.com/docs/query-your-data/nrql-new-relic-query-language/get-started/nrql-syntax-clauses-functions/) （内） _New Relic_ ドキュメント。
>クエリを使用して [New Relic dashboard](https://docs.newrelic.com/docs/query-your-data/explore-query-data/dashboards/introduction-dashboards/).

### ホスト数

次の例のNew Relicクエリは、環境内のホスト数を示しています。

```sql
SELECT uniqueCount(SystemSample.entityId) AS 'Infrastructure hosts', uniqueCount(Transaction.host) AS 'APM hosts seen' FROM SystemSample, Transaction where (Transaction.appName = 'cluster-id_stg' AND Transaction.transactionType = 'Web') OR SystemSample.apmApplicationNames LIKE '%|cluster-id_stg|%' TIMESERIES SINCE 3 HOURS AGO
```

次のスクリーンショットでは、 **APM ホストが確認される** は、選択した期間に記録されたトランザクションを持つホストの数を指します。

![New Relicホスト数](../../assets/new-relic/host-count.png)

### CPU 使用率

次の例のNew Relicクエリは、Web ノードの CPU 使用率を示しています。

```sql
SELECT average(cpuPercent) FROM SystemSample FACET hostname, apmApplicationNames WHERE instanceType LIKE 'c%' TIMESERIES SINCE 3 HOURS AGO
```

![New Relic Web ノードの CPU 使用率](../../assets/new-relic/web-node-cpu-usage.png)

## 自動スケーリングを有効にする

クラウドインフラストラクチャプロジェクト上のAdobe Commerceの自動スケーリングを有効または無効にするには、 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket). チケットで次の理由を選択します。

- **連絡先の理由**：インフラストラクチャの変更リクエスト
- **Adobe Commerce Infrastructure の連絡理由**：その他のインフラストラクチャ変更リクエスト

>[!IMPORTANT]
>
>自動スケーリング機能は、予期しないイベントをキャプチャします。 自動スケーリングを有効にしている場合でも、Adobeでは引き続き [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) 今後のイベントを予想する場合は、

### 負荷テスト

Adobeは、クラウドプロジェクトでの自動スケーリングを有効にします _ステージング_ 最初にクラスターを作成します。 環境で負荷テストを実行して完了した後、Adobeは、実稼動クラスターでの自動スケーリングを有効にします。 負荷テストのガイダンスについては、 [パフォーマンステスト](../launch/checklist.md#performance-testing).

### IP の許可リストに加える

自動スケーリングを有効にした後、送信 Web ノードトラフィックは、サービスノードの IP アドレスから発生します。 Adobe Commerce on cloud infrastructure プ許可リストに加えるロジェクトにバンドルされていないサードパーティのサービスとのを使用する場合は、サードパーティのサービスプロジェクトで IP アドレスを検証許可リストに加えるします。

例：

- にサ許可リストに加えるービスノード (1、2、3) の IP アドレスが含まれている場合は、操作は必要ありません。
- 許可リストに加えるに、サービスノード (1、2、3) と Web ノード (4、5、6) の IP アドレスが含まれている場合（この場合は 6 ノードすべて）、必要なアクションはありません。
- に IP許可リストに加えるアドレスが含まれている場合 _のみ_ web ノード (4、5、6) の場合は、サービスノードの IP アドレスを含めるようにを更新する必要がありま許可リストに加えるす。
