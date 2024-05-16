---
title: Commerceのクラウドアーキテクチャ
description: スタータープロジェクトアーキテクチャと Pro プロジェクトアーキテクチャがクラウドインフラストラクチャー上のCommerceとどのように違うかを説明します。
feature: Cloud, Iaas, Paas
topic: Architecture
recommendations: noDisplay
exl-id: 37cd6733-c10a-4d06-b784-171da576f9fc
source-git-commit: 1253d8357fd2554050d1775fefbc420a2097db5f
workflow-type: tm+mt
source-wordcount: '745'
ht-degree: 0%

---

# Commerceのクラウドアーキテクチャ

クラウドインフラストラクチャー上のAdobe Commerceには、スターターと Pro プランがあります。 各計画には、Adobe Commerceの開発とデプロイメントプロセスを推進する独自のアーキテクチャがあります。 スタータープランと Pro プラン アーキテクチャの両方で、継続的な統合をサポートしながら、複数の環境にわたってデータベース、Web サーバー、およびキャッシュ サーバーを展開し、エンドツーエンドのテストを行います。

比較のために、各プランには次のインフラストラクチャ機能とサポート対象製品が含まれています。

|          | スターター | プロ |
| -------- | --------------------| ------------------ |
| 主な機能 | <ul><li>[すべてのAdobe Commerce機能](https://experienceleague.adobe.com/docs/commerce-operations/release/features.html)</li><li>PayPal オンボーディングツール</li><li>[Commerce レポート](https://business.adobe.com/products/magento/business-intelligence.html?_ga=2.85288604.442698376.1665067470-1322106587.1655147209)</li></ul> | <ul><li>[すべてのAdobe Commerce機能](https://experienceleague.adobe.com/docs/commerce-operations/release/features.html)</li><li>PayPal オンボーディングツール</li><li>[Commerce レポート](https://business.adobe.com/products/magento/business-intelligence.html?_ga=2.85288604.442698376.1665067470-1322106587.1655147209)</li><li>[B2B モジュール](https://business.adobe.com/products/magento/b2b-ecommerce.html?_ga=2.105948422.442698376.1665067470-1322106587.1655147209)</li></ul> |
| インフラストラクチャとデプロイメント | <ul><li>ユーザー数に制限のない継続的なクラウド統合ツール</li><li>Fastly コンテンツ配信ネットワーク（CDN）、画像の最適化（IO）および寛大な帯域幅許可によるセキュリティの追加。 Web アプリケーションファイアウォール（WAF）サービスは、実稼動環境でのみ使用できます。</li><li>[New Relic](../monitor/new-relic-service.md) 3 つのブランチの APM （パフォーマンス監視）: `master` およびお好みの 2<br>Adobe Commerce用に最適化された Platform as a service （PaaS）の実稼動環境、ステージング環境、開発環境（合計 4 つのアクティブな環境）</li><li>エグレスフィルタリング（アウトバウンドファイアウォール）</li></ul> | <ul><li>ユーザー数に制限のない継続的なクラウド統合ツール</li><li>Fastly コンテンツ配信ネットワーク（CDN）、画像の最適化（IO）および寛大な帯域幅許可によるセキュリティの追加。 Web アプリケーションファイアウォール（WAF）サービスは、実稼動環境でのみ使用できます。</li><li>[New Relic](../monitor/new-relic-service.md) 実稼動環境のインフラストラクチャ + ステージング環境と実稼動環境の APM （パフォーマンス監視） この [管理されたアラートのポリシー](../monitor/investigate-performance.md#monitor-performance-with-managed-alerts) Adobe Commerceのポリシーでは、サイトのパフォーマンスに影響を与えるアプリケーションとインフラストラクチャの問題をプロアクティブに通知する監視のベストプラクティスを導入しています。</li><li>サービスとしてのプラットフォーム（PaaS）ベース [統合開発](pro-architecture.md#integration-environment) Adobe Commerce用に最適化された環境（合計 2 つのアクティブな環境）</li><li>サービスとしてのインフラストラクチャ（IaaS）：ステージング環境および実稼動環境用の専用の仮想インフラストラクチャ</li></ul> |
| 高可用性インフラストラクチャ | | [高可用性アーキテクチャ](pro-architecture.md#redundant-hardware) 基盤となる Infrastructure as a Service （IaaS）に 3 サーバをセットアップし、エンタープライズクラスの信頼性と可用性を提供 |
| 専用ハードウェア | | 基盤となるサービスとしてのインフラストラクチャ（IaaS）に分離された専用ハードウェアにより、より高いレベルの信頼性と可用性を提供 |
| 24 時間年中無休のメールサポート | コアアプリケーションとクラウドインフラストラクチャの 24 時間年中無休の監視およびメールのサポート | コアアプリケーションとクラウドインフラストラクチャの 24 時間年中無休の監視およびメールのサポート |
| 専任のカスタマー・テクニカル・アドバイザー（CTA） | | 最初のサイトのローンチまで、サブスクリプションから始まる最初のローンチ期間に対する専用のテクニカルアカウント管理 |
| アドオン\* | <ul><li>[B2B モジュール](https://business.adobe.com/products/magento/b2b-ecommerce.html)</li></ul> |

\* _追加料金で利用可能_

## スタータープロジェクト

この [スタータープランのアーキテクチャ](starter-architecture.md) には、次の 4 つの環境があります。

- **統合** – 統合環境には 2 つのテスト可能な環境があります。 各環境には、アクティブな Git ブランチ、データベース、web サーバー、キャッシュ、一部のサービス、環境変数および設定が含まれます。

- **ステージング** – コードと拡張機能がテストに合格すると、を結合できます `integration` ステージング環境に分岐し、実稼動前のテスト環境にします。 これには、 `staging` アクティブブランチ、データベース、web サーバー、キャッシング、サードパーティのサービス、環境変数、設定およびサービス（Fastly やNew Relicなど）。

- **実稼動** – コードの準備とテストが完了すると、すべてのコードがに結合されます `master` 実稼動ライブサイトにデプロイメントする場合。 この環境にはが含まれています `master` ブランチ、データベース、web サーバー、キャッシュ、サードパーティのサービス、環境変数、設定。

- **Inactive** – 非アクティブなブランチの数に制限はありません。

## Pro プロジェクト

この [Pro プランアーキテクチャ](pro-architecture.md) グローバルがある `master` 次の 3 つの環境を使用：

- **統合** – 統合環境は、データベース、Web サーバ、キャッシュ、一部のサービス、環境変数、構成を含む、テスト可能な環境を提供します。 ステージング環境に結合する前に、コードを開発、デプロイ、テストできます。

   - _Inactive_ – に基づいて、非アクティブなブランチの数に制限はありません `integration` 環境（アクティブなブランチは 1 つのみ（次を含まない） `integration` ）に設定します。

- **ステージング**- ステージング環境は実稼動前にテストするためのもので、データベース、web サーバー、キャッシュ、サードパーティのサービス、環境変数、設定、Fastly などのサービスが含まれています。

- **実稼動** – 実稼働環境には、データ、サービス、キャッシュ、ストアのための 3 ノードの高可用性アーキテクチャが含まれています。 実稼動環境は、環境変数、設定、サードパーティのサービスを備えたライブのパブリックストア環境です。

## サポート対象のソフトウェアとサービス

クラウドインフラストラクチャー上のAdobe Commerceでは次を使用します。

- オペレーティングシステム：Debian GNU/Linux
- Web サーバー：Nginx
- データベース：MySQL （MariaDB）
- コンテンツ配信ネットワーク（CDN）: Fastly CDN

次のサービスを設定できます。

- [PHP](../application/php-settings.md)
- [MySQL](../services/mysql.md)
- [Redis](../services/redis.md)
- [RabbitMQ](../services/rabbitmq.md)
- [Elasticsearch](../services/elasticsearch.md)
- [OpenSearch](../services/opensearch.md)

{{elasticsearch-support}}

>[!NOTE]
>
>参照： [必要システム構成](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/system-requirements.html) が含まれる _インストールガイド_ （推奨バージョン用）。

Fastly CDN モジュールは、ステージング環境と実稼動環境で CDN およびキャッシュサービスに使用されます。 参照： [Fastly サービスの設定](../cdn/fastly.md).

実装で使用するソフトウェアバージョンの設定について詳しくは、次のクラウドインフラストラクチャー上のAdobe Commerce設定ファイルを参照してください。

- [アプリケーション設定（.magento.app.yaml）](../application/configure-app-yaml.md)
- [環境設定（.magento.env.yaml）](../environment/configure-env-yaml.md)
- [ルートの設定（routes.yaml）](../routes/routes-yaml.md)
- [サービス設定（services.yaml）](../services/services-yaml.md)
