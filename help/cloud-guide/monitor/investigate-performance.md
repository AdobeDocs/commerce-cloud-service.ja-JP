---
title: New Relicの監視
description: New Relic ダッシュボードにアクセスし、クラウドインフラストラクチャプロジェクト上のAdobe Commerceからデータを分析する方法について説明します。
feature: Cloud, Observability
topic: Performance
exl-id: 8d1e2ad6-14d0-4754-9703-960d93e135a9
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '844'
ht-degree: 0%

---

# New Relicの監視

New Relicはインフラストラクチャを接続および監視し、 [!DNL Commerce] PHP エージェントを使用するアプリケーション。 クラウド環境がNew Relicに接続したら、New Relic アカウントにログインして、エージェントが収集したデータを確認できます。

日 _APM とサービス_ ページで、 **概要** をクリックして、アプリケーションに関するトランザクション情報を表示します。 この表示は、潜在的なエラーを特定し、アプリケーションとサービスの全体的な正常性を確認するのに役立ちます。

![クラウドプロジェクトのNew Relicの概要ページ](../../assets/new-relic/dashboard.png)

このビューを使用すると、応答の遅延やボトルネックが発生しているトランザクション、アプリケーションのスループット、web エラーなどを追跡できます。

追跡データを確認：

- **最も時間がかかる** – 並行してリクエストを追跡することで、時間消費を決定します。 例えば、製品ビューとカテゴリビューで費やしたトランザクション時間が最も長くなる場合があります。 顧客アカウントページが時間の消費で突然上位にランクされた場合、呼び出しやクエリによるドラッグのパフォーマンスがアプリケーションに影響を与える可能性があります。

- **最高のスループット** – 送信されたバイトのサイズと頻度に基づいて、最もヒットしたページを識別します。

収集されたすべてのデータは、データ、クエリまたは _Redis_ データ。 クエリによって問題が発生した場合、New Relicはそれらの問題を追跡して対応するための情報を提供します。

>[!TIP]
>
>このデータを使用したアプリケーションのパフォーマンスの問題のトラブルシューティングについて詳しくは、を参照してください。 [New Relicを使用したパフォーマンスのトラブルシューティング](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/troubleshoot-performance-using-new-relic-on-magento-commerce.html) が含まれる _Adobe Commerceヘルプセンター_.

## 管理されたアラートによるパフォーマンスの監視

Adobeはを提供します _Adobe Commerceの管理アラート_ パフォーマンス指標を追跡するためのアラートポリシー。 このポリシーには、インフラストラクチャやアプリケーションの問題がサイトのパフォーマンスに影響を与えた場合に、閾値およびトリガーに関する警告と重要な通知を設定するアラートの集合が含まれます。 ポリシーは、実稼動環境の次の指標を追跡します。

| 指標 | データ収集 | 対象 |
|:-------------------|:----------------|:----------------|
| [!DNL Apdex] スコア | APM | Pro および Starter |
| CPU 使用率 | NRI | プロ |
| ディスク容量 | NRI | プロ |
| エラー率 | APM | Pro および Starter |
| メモリ使用量 | NRI | プロ |
| MariaDB クエリの読み込み | NRI | プロ |
| Redis メモリ | NRI | プロ |

サイトインフラストラクチャまたはアプリケーションの状態がアラートしきい値をトリガーすると、New Relicからアラート通知が送信されるので、問題にプロアクティブに対処できます。 参照： [Adobe Commerceの管理アラート](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/support-tools/managed-alerts/managed-alerts-for-magento-commerce.html) が含まれる _Adobe Commerceヘルプセンター_ アラートしきい値の詳細と、アラートをトリガーした問題を解決するためのトラブルシューティング手順については、を参照してください。

>[!TIP]
>
>Pro ステージング環境および統合環境とスターター環境の場合は、を使用します [正常性通知](../integrations/health-notifications.md) ディスク容量を監視します。

>[!PREREQUISITES]
>
>- **New Relic資格情報**- クラウドプロジェクトのNew Relic アカウントにログインするための資格情報
>- **アクティブなNew Relicの統合** – クラウド環境がNew Relicに接続されていることを確認します
>- **ワークフロー通知** – 少なくとも 1 つを設定します [ワークフロー](#set-up-a-workflow-for-notifications) アラート通知を受信するには

**Adobe Commerceの管理アラートのポリシーを確認するには**:

1. にログイン [New Relic アカウント](https://login.newrelic.com/login).

1. を見つけます。 _Adobe Commerceの管理アラート_ ポリシー：

   - エクスプローラーナビゲーションメニューで、 **[!UICONTROL Alerts & AI]**.

   - 次の下 _検出_&#x200B;を選択し、 **[!UICONTROL Alert Conditions & Policies]**.

   - の上部でアカウントが選択されていることを確認します。 _アラート条件とポリシー_ 表示。

   - が含まれる _ポリシー_ リスト、選択 **Adobe Commerceの管理アラート** ポリシー。

     ![生成されたアラートポリシー](../../assets/new-relic/managed-alerts-policy.png)

     >[!NOTE]
     >
     >次の場合 _Adobe Commerceの管理アラート_ ポリシーは使用できません。を参照してください [Adobe Commerceの管理アラート](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/support-tools/managed-alerts/managed-alerts-for-magento-commerce.html) が含まれる _Adobe Commerceヘルプセンター_.

1. 「」をクリックします **[!UICONTROL Alert conditions]** タブをクリックして、ポリシーで定義されたアラート条件を確認します。

## アラートポリシーの作成

Adobe Commerceの管理アラート ポリシーに含まれるアラートを変更しないでください。 Adobeは、このポリシーのアラート条件を経時的に更新および改善します。これにより、ポリシーに追加したカスタマイズが上書きされます。

既存のアラートを変更する代わりに、アラートポリシーを作成できます。 次に、アラート条件を新しいポリシーにコピーします。 参照： [ポリシーまたは条件の更新](https://docs.newrelic.com/docs/alerts-applied-intelligence/new-relic-alerts/alert-policies/update-or-disable-policies-conditions/) が含まれる _New Relic_ ドキュメント。

>[!TIP]
>
>参照： [アラートの概要](https://docs.newrelic.com/docs/alerts-applied-intelligence/new-relic-alerts/learn-alerts/alerts-concepts-workflow/) が含まれる _New Relic_ アラート、アラートポリシーおよびワークフローについて詳しくは、ドキュメントを参照してください。

## 通知用のワークフローの設定

以下を設定できるようになりました _ワークフロー_（以前は通知チャネルと呼ばれていました）を使用すると、アラートポリシーなど、フィルターされたデータに基づいてサイトのパフォーマンスに関する通知を受信できます。 アプリケーションまたはインフラストラクチャの状態がアラートをトリガーする場合、パフォーマンスの問題に関する通知は、アラート・ポリシーに関連づけられたすべてのワークフローに送信されます。 また、イシューが承認されてクローズされたときに通知を受け取ることもできます。

New Relicには、メール、Slack、PagerDuty、Webhook など、様々なタイプのワークフロー通知を設定するためのテンプレートが用意されています。

**ワークフローを設定するには**:

1. にログイン [New Relic アカウント](https://login.newrelic.com/login).

1. ワークフローを作成します。

   - エクスプローラーナビゲーションメニューで、 **[!UICONTROL Alerts & AI]**.

   - の下の左側のナビゲーション _エンリッチメントと通知_&#x200B;を選択し、 **[!UICONTROL Workflows]**.

   - クリック **[!UICONTROL Add a workflow]** 右側です。

     ![New Relicによるワークフローの追加](../../assets/new-relic/add-a-workflow.png)

   - 日 _ワークフローの設定_ ページで、ワークフローの名前を入力します。

   - が含まれる _データをフィルター_ セクションで選択 **[!UICONTROL Managed Alerts for Adobe Commerce]** から **[!UICONTROL Policy]** ドロップダウンリスト。

   - が含まれる _通知_ 「」セクションでチャネルを選択し、指示に従います。

   - クリック **[!UICONTROL Test workflow]** をクリックして設定を検証します。

1. クリック **[!UICONTROL Activate workflow]**.

以下に関するNew Relic ドキュメントを参照してください [ワークフロー](https://docs.newrelic.com/docs/alerts-applied-intelligence/applied-intelligence/incident-workflows/incident-workflows/).

>[!WARNING]
>
>Adobe Commerceの管理アラート ポリシーのアラートには、Adobe Commerce on cloud infrastructure のお客様をサポートするAdobeチームに通知するように、デフォルトのワークフローが設定されています。 これらのデフォルトチャネルの設定を変更しないでください。また、割り当てられたアラートポリシーも削除しないでください。
