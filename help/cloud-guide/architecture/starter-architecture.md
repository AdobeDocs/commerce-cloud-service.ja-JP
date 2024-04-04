---
title: スターターアーキテクチャ
description: スターターアーキテクチャでサポートされる環境について説明します。
feature: Cloud, Paas
exl-id: 03365d32-4eb4-42d4-82a7-771df5e7b3da
source-git-commit: c61d711b1041ecf76ec6468cd225a34fd77c24b1
workflow-type: tm+mt
source-wordcount: '903'
ht-degree: 0%

---

# スターターアーキテクチャ

クラウドインフラストラクチャ上のAdobe Commerceスターターアーキテクチャは、最大で **4** 環境 ( `master` 初期プロジェクトコード、ステージング環境、最大 2 つの統合環境を含む環境。

すべての環境は、PaaS(Platform as a Service) コンテナに含まれています。 これらのコンテナは、サーバーのグリッド上の高度に制限されたコンテナ内にデプロイされます。 これらの環境は読み取り専用で、ローカルワークスペースからプッシュされたブランチからデプロイ済みのコード変更を受け入れます。 各環境は、データベースと Web サーバーを提供します。

任意の開発および分岐手法を使用できます。 プロジェクトへの初期アクセス権を取得したら、 `staging` 環境を `master` 環境。 次に、 `integration` ～から分岐する環境 `staging`.

## スターター環境のアーキテクチャ

次の図は、スターター環境の階層関係を示しています。

![スタータープロジェクトの概要](../../assets/starter/architecture.png)

## 実稼動環境

実稼動環境は、公開される単一サイトおよび複数サイトのストアフロントを実行する Cloud インフラストラクチャにAdobe Commerceをデプロイするためのソースコードを提供します。 実稼動環境では、 `master` ブランチを使用して、web サーバー、データベース、設定済みのサービスおよびアプリケーションコードを設定および有効化します。

これは、 `production` 環境は読み取り専用です。 `integration` 環境を使用してコードを変更し、 `integration` から `staging`最後に `production` 環境。 詳しくは、 [ストアをデプロイ](../deploy/staging-production.md) および [サイトの起動](../launch/overview.md).

Adobeでは、 `staging` を押す前に分岐する `master` ブランチ（にデプロイされます） `production` 環境。

## ステージング環境

Adobeは、という名前のブランチを作成することをお勧めします。 `staging` から `master`. The `staging` branch は、コードをステージング環境にデプロイし、コード、モジュールと拡張機能、支払いゲートウェイ、配送先、製品データなどをテストするための実稼動前環境を提供します。 この環境では、Fastly、New Relic APM、検索など、実稼動環境に合致するすべてのサービスの設定を提供します。

このガイドの他の節では、セキュアなステージング環境での最終的なコードデプロイメントと実稼動レベルのインタラクションのテスト手順を説明します。 最高のパフォーマンスと機能のテストをおこなうには、データベースをステージング環境にレプリケートします。

>[!WARNING]
>
>Adobeでは、実稼動環境にデプロイする前に、ステージング環境ですべてのマーチャントおよび顧客とのやり取りをテストすることをお勧めします。 詳しくは、 [ストアをデプロイ](../deploy/staging-production.md) および [デプロイメントをテスト](../test/staging-and-production.md).

## 統合環境

開発者は、 `integration` 開発、デプロイ、テストをおこなう環境：

- Adobe Commerce application code

- カスタムコード

- 拡張機能

- サービス

**推奨される使用例：**

統合環境は、限られたテストおよび開発を目的として設計されています。 例えば、統合環境を使用して、次のタスクを実行できます。

- 継続的統合 (CI) プロセスに対する変更が Cloud と互換性があることを確認する

- ホーム、カテゴリ、製品の詳細ページ (PDP)、チェックアウト、管理など、主要ページ上の重要なワークフローをテストする。

統合環境でのパフォーマンスを最高にするには、次のベストプラクティスに従います。

- カタログサイズを制限

- 使用を 1 人または 2 人の同時ユーザーに制限

- cron ジョブを無効にし、必要に応じて手動で実行します。

次の条件を満たすことができます： **2 つ** アクティブな統合環境。 統合環境を作成するには、 `staging` 分岐。 統合環境を作成する場合、環境名はブランチ名に一致します。 統合環境は、Web サーバとデータベースとを含む。 Fastly CDN やNew Relicが利用できないなど、すべてのサービスが含まれるわけではありません。

コードストレージ用の非アクティブなブランチの数に制限はありません。 非アクティブなブランチにアクセス、表示、テストするには、そのブランチをアクティブ化する必要があります

{{enhanced-integration-envs}}

## 実稼動およびステージングのテクノロジースタック

実稼動環境とステージング環境には、次のテクノロジーが含まれます。 これらのテクノロジーの変更と設定は、 [`.magento.app.yaml`](../application/configure-app-yaml.md) ファイル。

- HTTP キャッシュと CDN に失敗
- PHP-FPM と通信する Nginx Web サーバ、複数のワーカーを持つ 1 つのインスタンス
- Redis サーバー
- Adobe Commerce 2.2 から 2.4.3-p2 へのカタログ検索のElasticsearch
- OpenSearch でのAdobe Commerce 2.3.7-p3、2.4.3-p2 および 2.4.4 以降のカタログ検索
- エグレスフィルタリング（アウトバウンドファイアウォール）

### サービス

クラウドインフラストラクチャ上のAdobe Commerceは、現在、PHP、MySQL(MariaDB)、Elasticsearch(Adobe Commerce 2.2 ～ 2.4.3-p2)、OpenSearch（2.3.7-p3、2.4.3-p2、2.4.4 以降）、Redis および [!DNL RabbitMQ].

各サービスは、個別の安全なコンテナで実行されます。 コンテナは、プロジェクト内で一緒に管理されます。 次のような一部のサービスが標準です。

- HTTP ルータ（受信リクエストの処理、キャッシュとリダイレクトの処理）

- PHP アプリケーションサーバー

- Git

- セキュアシェル (SSH)

### ソフトウェアバージョン

Adobe Commerce on cloud infrastructure は、Debian GNU/Linux オペレーティングシステムと NGINX Web サーバーを使用します。 このソフトウェアはアップグレードできませんが、次のバージョンを構成できます。

- [PHP](../application/php-settings.md)

- [MySQL](../services/mysql.md)

- [レディス](../services/redis.md)

- [RabbitMQ](../services/rabbitmq.md)

- [Elasticsearch](../services/elasticsearch.md)

- [OpenSearch](../services/opensearch.md)

ステージング環境および実稼動環境では、CDN とキャッシュに Fastly を使用します。 プロジェクトの初期プロビジョニング中に、 Fastly CDN 拡張機能の最新バージョンがインストールされます。 拡張機能をアップグレードして、最新のバグ修正および改善を取得できます。 詳しくは、 [Magento2 の Fastly CDN モジュール](https://github.com/fastly/fastly-magento2). また、 [New Relic](../monitor/account-management.md) （パフォーマンス監視用）

以下のファイルを使用して、実装で使用するソフトウェアバージョンを設定します。

- [&#39;.magento.app.yaml&#39;](../application/configure-app-yaml.md)

- [&#39;routes.yaml&#39;](../routes/routes-yaml.md)

- [&#39;services.yaml&#39;](../services/services-yaml.md)

### バックアップと災害復旧

データベースとファイルシステムのバックアップは、 [!DNL Cloud Console] または CLI。 詳しくは、 [バックアップ管理](../storage/snapshots.md).

## 開発の準備

次のワークフローは、コードのブランチ、ストアの開発およびデプロイのプロセスの概要を示します。

1. ローカル環境の設定

1. のクローン `master` ローカル環境に分岐

1. の作成 `staging` ～から分岐する `master`

1. 開発用のブランチの作成元 `staging`

1. ビルドしてテスト用に環境にデプロイするコードを Git にプッシュします

ストアの開発、テスト、デプロイのための詳細な手順とウォークスルーについては、次の節を参照してください。

- [スターター開発およびデプロイワークフロー](starter-develop-deploy-workflow.md)

- [Docker の開発](../dev-tools/cloud-docker.md) (ローカル開発環境は Cloud Docker for Commerce で有効になっています )

- [分岐を管理](../project/console-branches.md)

- [ストアをデプロイ](../deploy/staging-production.md)

- [サイトの起動](../launch/overview.md)
