---
title: コマース用クラウドアーキテクチャ
description: Cloud インフラストラクチャ上の Commerce の Starter および Pro プロジェクトアーキテクチャのコントラストを説明します。
feature: Cloud, Iaas, Paas
topic: Architecture
recommendations: noDisplay
exl-id: 37cd6733-c10a-4d06-b784-171da576f9fc
source-git-commit: 1253d8357fd2554050d1775fefbc420a2097db5f
workflow-type: tm+mt
source-wordcount: '745'
ht-degree: 0%

---

# コマース用クラウドアーキテクチャ

Adobe Commerce on cloud インフラストラクチャには、Starter と Pro のプランがあります。 各プランには、Adobe Commerceの開発およびデプロイメントプロセスを推進する独自のアーキテクチャがあります。 スタータープランと Pro プランアーキテクチャの両方で、データベース、Web サーバ、キャッシュサーバを複数の環境に分散して導入し、継続的な統合をサポートします。

比較のため、各プランには、次のインフラストラクチャ機能とサポート対象製品が含まれています。

|          | スターター | Pro |
| -------- | --------------------| ------------------ |
| 主な機能 | <ul><li>[すべてのAdobe Commerce機能](https://experienceleague.adobe.com/docs/commerce-operations/release/features.html)</li><li>PayPal オンボーディングツール</li><li>[コマースレポート](https://business.adobe.com/products/magento/business-intelligence.html?_ga=2.85288604.442698376.1665067470-1322106587.1655147209)</li></ul> | <ul><li>[すべてのAdobe Commerce機能](https://experienceleague.adobe.com/docs/commerce-operations/release/features.html)</li><li>PayPal オンボーディングツール</li><li>[コマースレポート](https://business.adobe.com/products/magento/business-intelligence.html?_ga=2.85288604.442698376.1665067470-1322106587.1655147209)</li><li>[B2B モジュール](https://business.adobe.com/products/magento/b2b-ecommerce.html?_ga=2.105948422.442698376.1665067470-1322106587.1655147209)</li></ul> |
| インフラストラクチャと導入 | <ul><li>無制限のユーザーを含む継続的なクラウド統合ツール</li><li>Fastly Content Delivery Network(CDN)、Image Optimization(IO)、および大量の帯域幅を許可したセキュリティが追加されました。 Web Application Firewall(WAF) サービスは、実稼動環境でのみ使用できます。</li><li>[New Relic](../monitor/new-relic-service.md) 3 つのブランチでの APM（パフォーマンス監視）: `master` お好みの 2<br>Adobe Commerce向けに最適化された、Platform as a Service(PaaS) の実稼動環境、ステージング環境、および開発環境（合計 4 つのアクティブ環境）</li><li>エグレスフィルタリング（アウトバウンドファイアウォール）</li></ul> | <ul><li>無制限のユーザーを含む継続的なクラウド統合ツール</li><li>Fastly Content Delivery Network(CDN)、Image Optimization(IO)、および大量の帯域幅を許可したセキュリティが追加されました。 Web Application Firewall(WAF) サービスは、実稼動環境でのみ使用できます。</li><li>[New Relic](../monitor/new-relic-service.md) 実稼動環境のインフラストラクチャと、ステージング環境および実稼動環境の APM（パフォーマンス監視）。 The [管理されたアラートポリシー](../monitor/investigate-performance.md#monitor-performance-with-managed-alerts) Adobe Commerceのポリシーには、サイトのパフォーマンスに影響するアプリケーションとインフラストラクチャの問題を事前に通知する、監視のベストプラクティスが実装されています。</li><li>Platform as a Service(PaaS) ベース [統合の開発](pro-architecture.md#integration-environment) Adobe Commerce向けに最適化された環境（合計 2 つのアクティブな環境）</li><li>Infrastructure as a service(IaaS)：ステージング環境と本番環境用に専用の仮想インフラストラクチャ</li></ul> |
| 高可用性インフラストラクチャ | | [高可用性アーキテクチャ](pro-architecture.md#redundant-hardware) 基盤となる Infrastructure as a service(IaaS) に 3 サーバを設定し、エンタープライズクラスの信頼性と可用性を提供 |
| 専用ハードウェア | | 基盤となるインフラストラクチャ (IaaS) に独立した専用ハードウェアを搭載し、より高いレベルの信頼性と可用性を提供 |
| 24 時間 365 日のメールサポート | コアアプリケーションとクラウドインフラストラクチャの 24 時間 365 日の監視と E メールのサポート | コアアプリケーションとクラウドインフラストラクチャの 24 時間 365 日の監視と E メールのサポート |
| 専門のお客様テクニカルアドバイザ (CTA) | | 初回起動時の専用テクニカルアカウント管理。最初のサイトが起動されるまで、サブスクリプションから始まります。 |
| アドオン\* | <ul><li>[B2B モジュール](https://business.adobe.com/products/magento/b2b-ecommerce.html)</li></ul> |

\* _追加料金で利用できます_

## スタータープロジェクト

The [スタータープランのアーキテクチャ](starter-architecture.md) には 4 つの環境があります。

- **統合** — 統合環境は、テスト可能な 2 つの環境を提供します。 各環境には、アクティブな Git ブランチ、データベース、Web サーバー、キャッシュ、一部のサービス、環境変数および設定が含まれます。

- **ステージング** — コードと拡張機能がテストに合格すると、 `integration` ステージング環境に分岐します。これは、実稼動前のテスト環境になります。 これには、 `staging` Fastly やNew Relicなど、アクティブなブランチ、データベース、web サーバー、キャッシュ、サードパーティのサービス、環境変数、設定、サービス。

- **実稼動** — コードの準備が整い、テストが完了すると、すべてのコードが `master` 実稼動ライブサイトにデプロイする場合。 この環境には、アクティブな `master` ブランチ、データベース、Web サーバー、キャッシュ、サードパーティのサービス、環境変数、および設定に関する情報を提供します。

- **非アクティブ** — 非アクティブなブランチは無制限に数えられます。

## プロプロジェクト

The [プロプランアーキテクチャ](pro-architecture.md) はグローバル `master` 3 つの環境を使用：

- **統合** — 統合環境は、データベース、Web サーバー、キャッシュ、一部のサービス、環境変数、設定を含む、テスト可能な環境を提供します。 ステージング環境に結合する前に、コードを開発、デプロイ、テストできます。

   - _非アクティブ_ — 非アクティブなブランチの数は、 `integration` 環境、ただし 1 つのアクティブなブランチ ( `integration` ) をクリックします。

- **ステージング** — ステージング環境は、実稼動前のテスト用で、データベース、Web サーバー、キャッシュ、サードパーティのサービス、環境変数、設定、サービス（Fastly など）が含まれます。

- **実稼動** — 実稼動環境には、データ、サービス、キャッシュ、保存用の 3 ノードの高可用性アーキテクチャが含まれています。 実稼動環境は、環境変数、設定、サードパーティのサービスを使用したライブ、パブリックストア環境です。

## サポートされるソフトウェアとサービス

Adobe Commerce on cloud infrastructure では、次のものを使用します。

- オペレーティングシステム： Debian GNU/Linux
- Web サーバ：Nginx
- データベース： MySQL (MariaDB)
- コンテンツ配信ネットワーク (CDN): Fastly CDN

次のサービスを設定できます。

- [PHP](../application/php-settings.md)
- [MySQL](../services/mysql.md)
- [レディス](../services/redis.md)
- [RabbitMQ](../services/rabbitmq.md)
- [Elasticsearch](../services/elasticsearch.md)
- [OpenSearch](../services/opensearch.md)

{{elasticsearch-support}}

>[!NOTE]
>
>詳しくは、 [必要システム構成](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/system-requirements.html) （内） _インストールガイド_ を参照してください。

Fastly CDN モジュールは、ステージング環境および実稼動環境での CDN およびキャッシュサービスに使用されます。 詳しくは、 [Fastly サービスの設定](../cdn/fastly.md).

実装で使用するソフトウェアバージョンの設定について詳しくは、次のAdobe Commerce on クラウドインフラストラクチャ設定ファイルを参照してください。

- [アプリケーション設定 (.magento.app.yaml)](../application/configure-app-yaml.md)
- [環境設定 (.magento.env.yaml)](../environment/configure-env-yaml.md)
- [ルート設定 (routes.yaml)](../routes/routes-yaml.md)
- [サービス設定 (services.yaml)](../services/services-yaml.md)
