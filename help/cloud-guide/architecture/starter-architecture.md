---
title: スターターアーキテクチャ
description: スターターアーキテクチャがサポートする環境について説明します。
feature: Cloud, Paas
exl-id: 03365d32-4eb4-42d4-82a7-771df5e7b3da
source-git-commit: c61d711b1041ecf76ec6468cd225a34fd77c24b1
workflow-type: tm+mt
source-wordcount: '903'
ht-degree: 0%

---

# スターターアーキテクチャ

Adobe Commerce on cloud infrastructure スターターアーキテクチャは、最大をサポートします。 **四** 環境（を含む） `master` 初期プロジェクトコード、ステージング環境、最大 2 つの統合環境を含む環境です。

すべての環境は、PaaS （Platform as a service）コンテナ内にあります。 これらのコンテナは、サーバーのグリッド上にある、制約の厳しいコンテナ内にデプロイされます。 これらの環境は読み取り専用で、ローカルワークスペースからプッシュされたブランチからデプロイされたコードの変更を受け入れます。 各環境は、データベースと Web サーバーを提供します。

任意の開発手法やブランチ手法を使用できます。 プロジェクトへの初期アクセス権を取得したら、 `staging` からの環境 `master` 環境。 次に、を作成します `integration` ～から分岐して生じる環境 `staging`.

## スターター環境のアーキテクチャ

次の図は、スターター環境の階層関係を示しています。

![スタータープロジェクトの概要](../../assets/starter/architecture.png)

## 実稼動環境

実稼動環境は、Adobe Commerceをクラウドインフラストラクチャにデプロイするためのソースコードを提供します。このインフラストラクチャは、一般向けの単一およびマルチサイトのストアフロントを実行します。 実稼動環境はからのコードを使用します `master` web サーバー、データベース、設定済みサービス、アプリケーションコードを設定および有効にするためのブランチ。

なぜなら `production` 環境は読み取り専用です。を使用してください `integration` 環境コードを変更するには、からアーキテクチャ全体にデプロイします `integration` 対象： `staging`最後に、です。 `production` 環境。 参照： [ストアのデプロイ](../deploy/staging-production.md) および [サイトの起動](../launch/overview.md).

Adobeでは、で完全にテストすることをお勧めします `staging` にプッシュする前のブランチ `master` ブランチ。にデプロイされます。 `production` 環境。

## ステージング環境

Adobeでは、というブランチを作成することをお勧めします。 `staging` から `master`. この `staging` ブランチはコードをステージング環境にデプロイして、コード、モジュール、拡張機能、支払いゲートウェイ、配送、製品データなどをテストする実稼動前環境を提供します。 この環境は、Fastly、New Relic APM、検索を含む、実稼動環境に一致するすべてのサービスの設定を提供します。

このガイドの追加セクションでは、安全なステージング環境での最終コードデプロイメントと実稼動レベルのインタラクションのテストに関する手順を説明します。 最高のパフォーマンスと機能のテストを実現するには、データベースをステージング環境にレプリケートします。

>[!WARNING]
>
>Adobeでは、実稼動環境にデプロイする前に、ステージング環境でマーチャントと顧客のインタラクションをすべてテストすることをお勧めします。 参照： [ストアのデプロイ](../deploy/staging-production.md) および [デプロイメントをテスト](../test/staging-and-production.md).

## 統合環境

開発者は、 `integration` 開発、デプロイ、テストする環境：

- Adobe Commerce アプリケーションコード

- カスタムコード

- 拡張機能

- サービス

**推奨されるユースケース：**

統合環境は、限定的なテストと開発のために設計されています。 例えば、統合環境を使用して、次のタスクを実行できます。

- 継続的統合（CI）プロセスへの変更がクラウドと互換性があることを確認します

- ホーム、カテゴリ、製品詳細ページ（PDP）、チェックアウト、管理者などの主要なページでの重要なワークフローのテスト

統合環境で最高のパフォーマンスを得るには、次のベストプラクティスに従います。

- カタログサイズを制限

- 使用を 1 人または 2 人の同時ユーザーに制限する

- cron ジョブを無効にし、必要に応じて手動で実行する

最大のは次のとおりです **two** アクティブな統合環境。 統合環境を作成するには、 `staging` 分岐。 統合環境を作成する場合、環境名はブランチ名と一致します。 統合環境は、ウェブサーバ及びデータベースを含む。 すべてのサービスが含まれるわけではありません（例：Fastly CDN とNew Relicは利用できません）。

コードストレージ用の非アクティブなブランチの数に制限はありません。 非アクティブなブランチにアクセス、表示、テストするには、ブランチをアクティベートする必要があります

{{enhanced-integration-envs}}

## 実稼動とステージングのテクノロジースタック

実稼動環境とステージング環境には、次のテクノロジーが含まれます。 これらのテクノロジーは、 [`.magento.app.yaml`](../application/configure-app-yaml.md) ファイル。

- Fastly （HTTP キャッシュおよび CDN の場合）
- 複数のワーカーを持つ 1 つのインスタンスである PHP-FPM と通信する Nginx web サーバー
- Redis サーバー
- Adobe Commerce 2.2 から 2.4.3-p2 へのカタログ検索のElasticsearch
- OpenSearch でAdobe Commerce 2.3.7-p3、2.4.3-p2、2.4.4 以降のカタログ検索
- エグレスフィルタリング（アウトバウンドファイアウォール）

### サービス

現在、クラウドインフラストラクチャー上のAdobe Commerceは、PHP、MySQL （MariaDB）、Elasticsearch（Adobe Commerce 2.2 から 2.4.3-p2）、OpenSearch （2.3.7-p3、2.4.3-p2、2.4.4 以降）、Redis および [!DNL RabbitMQ].

各サービスは、個別の安全なコンテナで実行されます。 コンテナは、プロジェクトで一緒に管理されます。 次のような、一部のサービスは標準です。

- HTTP ルーター（受信リクエストの処理だけでなく、キャッシュとリダイレクトも処理）

- PHP アプリケーションサーバー

- Git

- セキュアシェル（SSH）

### ソフトウェアバージョン

クラウドインフラストラクチャー上のAdobe Commerceは、Debian GNU/Linux オペレーティングシステムと NGINX web サーバーを使用します。 このソフトウェアはアップグレードできませんが、次のバージョンを構成できます。

- [PHP](../application/php-settings.md)

- [MySQL](../services/mysql.md)

- [Redis](../services/redis.md)

- [RabbitMQ](../services/rabbitmq.md)

- [Elasticsearch](../services/elasticsearch.md)

- [OpenSearch](../services/opensearch.md)

ステージング環境と実稼動環境では、CDN とキャッシュに Fastly を使用します。 最新バージョンの Fastly CDN 拡張機能は、プロジェクトの初期プロビジョニング時にインストールされます。 拡張機能をアップグレードすると、最新のバグ修正と改善を取得できます。 参照： [Magento 2 用 Fastly CDN モジュール](https://github.com/fastly/fastly-magento2). また、次へのアクセス権があります [New Relic](../monitor/account-management.md) （パフォーマンス監視）。

次のファイルを使用して、実装環境で使用するソフトウェアバージョンを設定します。

- [&#39;.magento.app.yaml&#39;](../application/configure-app-yaml.md)

- [&#39;routes.yaml&#39;](../routes/routes-yaml.md)

- [&#39;services.yaml&#39;](../services/services-yaml.md)

### バックアップと障害回復

データベースとファイルシステムのバックアップを作成するには、 [!DNL Cloud Console] または CLI 参照： [バックアップ管理](../storage/snapshots.md).

## 開発の準備

次のワークフローは、コードを分岐し、ストアを開発およびデプロイするプロセスの概要を示しています。

1. ローカル環境の設定

1. のクローン `master` ローカル環境への分岐

1. を作成 `staging` 分岐の基点 `master`

1. から開発用の分岐を作成 `staging`

1. テスト用の環境を構築およびデプロイするコードを Git にプッシュ

ストアを開発、テスト、デプロイするための詳細な手順と手順については、次の節を参照してください。

- [スターターの開発およびデプロイワークフロー](starter-develop-deploy-workflow.md)

- [Docker 開発](../dev-tools/cloud-docker.md) （Commerce用の Cloud Docker によるローカル開発環境の有効化）

- [ブランチの管理](../project/console-branches.md)

- [ストアのデプロイ](../deploy/staging-production.md)

- [サイトの起動](../launch/overview.md)
