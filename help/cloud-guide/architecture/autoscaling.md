---
title: 自動スケーリング
description: クラウドインフラストラクチャー上のAdobe Commerceをリソースのニーズに合わせて拡張する方法について説明します。
feature: Cloud, Auto Scaling
topic: Architecture
exl-id: 2ba49c55-d821-4934-965f-f35bd18ac95f
source-git-commit: c61d711b1041ecf76ec6468cd225a34fd77c24b1
workflow-type: tm+mt
source-wordcount: '549'
ht-degree: 0%

---

# 自動スケーリング

自動スケーリングでは、最適なパフォーマンスと妥当なコストを維持するために、クラウドインフラストラクチャにリソースを自動的に追加または削除します。 現在、この機能は、で設定されたプロジェクトでのみ使用できます [拡張されたアーキテクチャ](scaled-architecture.md).

## Web サーバーノード

この [web 層](scaled-architecture.md#web-tier) は、プロセスリクエストの増加とトラフィック要件の増加に対応するために拡張されます。 現在、自動スケーリング機能は、web サーバーノードを追加または削除することで水平方向にのみスケーリングできます。

自動スケーリング イベントは、CPU 使用率とトラフィックが事前定義されたしきい値に達した場合に発生します。

- **追加されたノード** – すべてのアクティブな Web ノードの CPU/コアは、1 分間は 75% の処理能力で、5 分連続でトラフィックが 20% 増加します。
- **削除されたノード** – すべてのアクティブな Web ノードの CPU/コアが 60% で 20 分間読み込まれます。 ノードは、追加された順序で削除されます。

最小および最大しきい値は、各マーチャントの契約済みリソース制限に基づいて決定および設定されます。これにより、無限スケーリングのリスクが軽減されます。

## New Relicによるしきい値の監視

を使用できます [New Relic サービス](../monitor/new-relic-service.md) ホスト数や CPU 使用率などの特定のしきい値を監視します。 次のNew Relic クエリでは、変数表記を使用しています `cluster-id` 例です。

>[!TIP]
>
>クエリの作成について詳しくは、 [NRQL 構文、句、関数](https://docs.newrelic.com/docs/query-your-data/nrql-new-relic-query-language/get-started/nrql-syntax-clauses-functions/) が含まれる _New Relic_ ドキュメント。
>クエリを使用したの構築 [New Relic ダッシュボード](https://docs.newrelic.com/docs/query-your-data/explore-query-data/dashboards/introduction-dashboards/).

### ホスト数

次のNew Relic クエリの例では、環境内のホスト数を示しています。

```sql
SELECT uniqueCount(SystemSample.entityId) AS 'Infrastructure hosts', uniqueCount(Transaction.host) AS 'APM hosts seen' FROM SystemSample, Transaction where (Transaction.appName = 'cluster-id_stg' AND Transaction.transactionType = 'Web') OR SystemSample.apmApplicationNames LIKE '%|cluster-id_stg|%' TIMESERIES SINCE 3 HOURS AGO
```

次のスクリーンショットでは、 **APM ホストが表示される** 選択した期間中にトランザクションがログに記録されたホストの数を参照します。

![New Relic ホスト数](../../assets/new-relic/host-count.png)

### CPU 使用率

次のNew Relic クエリの例は、web ノードの CPU 使用率を示しています。

```sql
SELECT average(cpuPercent) FROM SystemSample FACET hostname, apmApplicationNames WHERE instanceType LIKE 'c%' TIMESERIES SINCE 3 HOURS AGO
```

![New Relic web ノードの CPU 使用率](../../assets/new-relic/web-node-cpu-usage.png)

## 自動スケーリングを有効にする

Adobe Commerce on cloud infrastructure プロジェクトの自動スケーリングを有効または無効にするには： [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket). チケットで次の理由を選択します。

- **連絡先の理由**: インフラストラクチャ変更リクエスト
- **Adobe Commerce インフラストラクチャ連絡先の理由**：その他のインフラストラクチャ変更リクエスト

>[!IMPORTANT]
>
>自動スケーリング機能は、予期しないイベントをキャプチャします。 自動スケーリングを有効にしている場合でも、Adobeでは次の操作を続行することをお勧めします [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) 今後のイベントを想定している場合。

### 負荷テスト

Adobeを使用すると、クラウドプロジェクトの自動スケーリングが可能になります _ステージング_ 最初にクラスター化します。 環境で負荷テストを実行して完了すると、Adobeは実稼動クラスターで自動スケーリングを有効にします。 負荷テストのガイダンスについては、 [パフォーマンステスト](../launch/checklist.md#performance-testing).

### IP許可リスト

自動スケーリングを有効にした後、送信 web ノードトラフィックは、サービスノードの IP アドレスから発信されます。 クラウドインフラストラクチャプロジェクトのAdobe Commerce許可リストに加えるにバンドルされていないサードパーティサービスと許可リストを使用する場合は、サードパーティサービスの IP アドレスを確認します。

例：

- 許可リストにサービスノードの IP アドレス（1、2、3）が含まれている場合は、対処は必要ありません。
- 許可リストにサービスノード（1、2、3）と web ノード（4、5、6）の IP アドレスが含まれている場合（この場合、6 つのノードすべて）、アクションは必要ありません。
- （許可リストに IP アドレスが含まれている場合） _のみ_ web ノード（4、5、6）の場合、許可リストを更新して、サービスノードの IP アドレスを含める必要があります。
