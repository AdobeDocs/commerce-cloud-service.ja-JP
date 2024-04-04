---
title: 設定ファイルの概要
description: カスタマイズされたAdobe Commerceストアのデプロイと管理をサポートするクラウドインフラストラクチャ環境の設定について説明します。
feature: Cloud, Configuration, Services, Iaas, Paas
exl-id: f469a0ec-e459-413f-9725-66a0fbf34f01
source-git-commit: 47b66d0d2bbff14e76ce49182a68d5e6c9fb13a7
workflow-type: tm+mt
source-wordcount: '263'
ht-degree: 0%

---

# 設定ファイルの概要

クラウドインフラストラクチャ上のAdobe Commerceの環境には、アプリケーション、サービス、およびAdobe Commerceアプリケーションコードベースとファイルの完全なシステムを提供するデータベースを含むコンテナが含まれます。

次の設定ファイルを使用して、プロジェクト環境をサポートするためのアプリケーション設定、ルート、ビルドおよびデプロイアクション、および通知を構成できます。

| 設定 | ファイル名 | 説明 |
| ------------- | -------- | ----------- |
| [アプリ](../application/configure-app-yaml.md) | `.magento.app.yaml` | サービス、フック、cron ジョブなど、Adobe Commerceの構築とデプロイの方法を定義します。 |
| [環境](configure-env-yaml.md) | `.magento.env.yaml` | 環境変数を使用して、Pro Staging や Production を含むすべての環境にわたるビルドとデプロイのアクションの管理を一元化します。 |
| [ルート](../routes/routes-yaml.md) | `.magento/routes.yaml` | キャッシュ、リダイレクト、およびサーバーサイドインクルードを設定します。 |
| [サービス](../services/services-yaml.md) | `.magento/services.yaml` | Adobe Commerceが使用するサービスを名前とバージョンで定義します。 例えば、このファイルには、MariaDB、PHP 拡張機能、Redis、RabbitMQ、Elasticsearch、OpenSearch のバージョンが含まれる場合があります。 これらの変更を Pro プランのステージング環境および実稼動環境にプッシュするには、サポートチケットを開く必要があります。 |
| [PHP 設定](../application/php-settings.md#configure-php) | `php.ini` | プロジェクトに追加できるオプションのファイルです。 このファイルに含まれる設定は、クラウドインフラストラクチャで管理される設定に追加されます。 |

{style="table-layout:auto"}

## Pro 環境の設定を更新しました。

クラウドインフラストラクチャ Pro ステージング環境および実稼動環境のAdobe Commerceでは、ローカル開発環境で多くの設定オプションを更新し、変更をコミットしてこれらの環境に適用できます。 ただし、 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) 次の設定オプションを更新するには：

- のサービスをインストールまたは更新します。 `.magento/services.yaml` ファイル。
- の設定を変更します。 `mounts` および `disk` プロパティ `.magento.app.yaml` ファイル。

{{pro-self-service-warning}}
