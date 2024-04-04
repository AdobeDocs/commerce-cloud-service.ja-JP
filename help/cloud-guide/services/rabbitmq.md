---
title: RabbitMQサービスの設定
description: RabbitMQサービスを有効にして、クラウドインフラストラクチャ上のAdobe Commerceのメッセージキューを管理する方法を説明します。
feature: Cloud, Services
exl-id: 85794b8f-2260-4a6e-b5a6-a1b4c356594e
source-git-commit: d4c36b084094846cfad69adc2bffd567a58fab26
workflow-type: tm+mt
source-wordcount: '378'
ht-degree: 0%

---

# 設定 [!DNL RabbitMQ] サービス

The [メッセージキューフレームワーク (MQF)](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/message-queues/message-queue-framework.html) は、Adobe Commerce内で [モジュール](https://glossary.magento.com/module) メッセージをキューに公開する場合。 また、非同期でメッセージを受信するコンシューマーも定義します。

MQF は [RabbitMQ](https://www.rabbitmq.com/) メッセージを送受信するためのスケーラブルなプラットフォームを提供するメッセージングブローカーとして。 また、配信されなかったメッセージを保存するメカニズムも含まれています。 [!DNL RabbitMQ] は、AMQP(Advanced Message Queuing Protocol) 0.9.1 仕様に基づいています。

>[!WARNING]
>
>既存の AMQP ベースのサービスを使用する場合は、次のようにします。 [!DNL RabbitMQ]を使用すると、クラウドインフラストラクチャでAdobe Commerceを利用して作成する代わりに、 [`QUEUE_CONFIGURATION`](../environment/variables-deploy.md#queue_configuration) 環境変数を使用して、サイトに接続します。

{{service-instruction}}

**RabbitMQを有効にするには**:

1. 必要な名前、タイプ、およびディスク値（MB 単位）を `.magento/services.yaml` ファイルには、インストールされているRabbitMQのバージョンも含まれています。

   ```yaml
   rabbitmq:
       type: rabbitmq:<version>
       disk: 1024
   ```

1. 関係を `.magento.app.yaml` ファイル。

   ```yaml
   relationships:
       rabbitmq: "rabbitmq:rabbitmq"
   ```

1. コードの変更を追加、コミット、およびプッシュします。

   ```bash
   git add .magento/services.yaml .magento.app.yaml
   ```

   ```bash
   git commit -m "Enable RabbitMQ service"
   ```

   ```bash
   git push origin <branch-name>
   ```

1. [サービスの関係を確認します](services-yaml.md#service-relationships).

{{service-change-tip}}

## デバッグ用にRabbitMQに接続

デバッグの目的で、次のいずれかの方法でサービスインスタンスに直接接続すると便利です。

- 接続環境からローカル開発
- アプリケーションから接続
- PHP アプリケーションから接続する

### 接続環境からローカル開発

1. にログインします。 `magento-cloud` CLI とプロジェクト：

   ```bash
   magento-cloud login
   ```

1. RabbitMQがインストールおよび設定された環境を確認します。

   ```bash
   magento-cloud environment:checkout <environment-id>
   ```

1. SSH を使用してクラウド環境に接続します。

   ```bash
   magento-cloud ssh
   ```

1. からRabbitMQ接続の詳細とログイン資格情報を取得します。 [$MAGENTO_CLOUD_RELATIONSHIPS](../application/properties.md#relationships) 変数：

   ```bash
   echo $MAGENTO_CLOUD_RELATIONSHIPS | base64 -d | json_pp
   ```

   または

   ```bash
   php -r 'print_r(json_decode(base64_decode($_ENV["MAGENTO_CLOUD_RELATIONSHIPS"])));'
   ```

   応答で、RabbitMQ情報を検索します。次に例を示します。

   ```json
   {
      "rabbitmq" : [
         {
            "password" : "guest",
            "ip" : "246.0.129.2",
            "scheme" : "amqp",
            "port" : 5672,
            "host" : "rabbitmq.internal",
            "username" : "guest"
         }
      ]
   }
   ```

1. RabbitMQへのローカルポート転送を有効にします。

   ```bash
   ssh -L <port-number>:rabbitmq.internal:<port-number> <project-ID>-<branch-ID>@ssh.us.magentosite.cloud
   ```

   次の URL でのRabbitMQ Management Web インターフェイスへのアクセス例 `http://localhost:15672` 次に該当：

   ```bash
   ssh -L 15672:rabbitmq.internal:15672 <project-ID>-<branch-ID>@ssh.us.magentosite.cloud
   ```

1. セッションが開いている間に、ローカルのワークステーションから任意のRabbitMQクライアントを起動し、 `localhost:<portnumber>` Magento_CLOUD_RELATIONSHIPS 変数のポート番号、ユーザー名、パスワード情報を使用します。

### アプリケーションから接続

アプリケーションで実行しているRabbitMQに接続するには、次のようなクライアントをインストールします。 [amqp-utils](https://github.com/dougbarth/amqp-utils)を、 `.magento.app.yaml` ファイル。

以下に例を挙げます。

```yaml
dependencies:
    ruby:
        amqp-utils: "0.5.1"
```

PHP コンテナにログインする際に、 `amqp-` コマンドを使用して、キューを管理できます。

### PHP アプリケーションから接続する

PHP アプリケーションを使用してRabbitMQに接続するには、PHP を追加します。 [ライブラリ](https://glossary.magento.com/library) をソースツリーに追加します。
