---
title: RabbitMQ サービスの設定
description: RabbitMQ サービスを有効にして、クラウドインフラストラクチャー上のAdobe Commerceのメッセージキューを管理する方法について説明します。
feature: Cloud, Services
exl-id: 85794b8f-2260-4a6e-b5a6-a1b4c356594e
source-git-commit: adcfbb7217c70122a4003a66d1bec1a623fbf11a
workflow-type: tm+mt
source-wordcount: '399'
ht-degree: 0%

---

# の設定 [!DNL RabbitMQ] サービス

この [メッセージキューフレームワーク（MQF）](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/message-queues/message-queue-framework.html) は、Adobe Commerce内のシステムで、 [モジュール](https://glossary.magento.com/module) メッセージをキューに公開します。 また、メッセージを非同期で受信するコンシューマーも定義します。

この MQF は、 [RabbitMQ](https://www.rabbitmq.com/) メッセージを送受信するためのスケーラブルなプラットフォームを提供するメッセージングブローカーとして。 また、未配信メッセージを保存するメカニズムも含まれています。 [!DNL RabbitMQ] は、Advanced Message Queuing Protocol （AMQP） 0.9.1 仕様に基づいています。

>[!WARNING]
>
>次のような既存の AMQP ベースのサービスを使用する場合： [!DNL RabbitMQ]を使用すれば、Adobe Commerceのクラウドインフラストラクチャーに依存してテンプレートを作成する代わりに、 [`QUEUE_CONFIGURATION`](../environment/variables-deploy.md#queue_configuration) サイトに接続するための環境変数。

{{service-instruction}}

**RabbitMQを有効にするには**:

1. 必要な名前、タイプ、ディスク値（MB 単位）をに追加します `.magento/services.yaml` ファイルと、インストールされているRabbitMQのバージョン。

   ```yaml
   rabbitmq:
       type: rabbitmq:<version>
       disk: 1024
   ```

1. での関係の設定 `.magento.app.yaml` ファイル。

   ```yaml
   relationships:
       rabbitmq: "rabbitmq:rabbitmq"
   ```

1. コードの変更を追加、コミット、プッシュします。

   ```bash
   git add .magento/services.yaml .magento.app.yaml
   ```

   ```bash
   git commit -m "Enable RabbitMQ service"
   ```

   ```bash
   git push origin <branch-name>
   ```

1. [サービス関係の検証](services-yaml.md#service-relationships).

{{service-change-tip}}

## デバッグ用にRabbitMQに接続

デバッグの目的では、次のいずれかの方法でサービスインスタンスに直接接続すると便利です。

- ローカル開発環境から接続する
- アプリケーションからの接続
- PHP アプリケーションからの接続

### ローカル開発環境から接続する

1. にログインします `magento-cloud` CLI とプロジェクト：

   ```bash
   magento-cloud login
   ```

1. RabbitMQがインストールおよび設定された環境をチェックアウトします。

   ```bash
   magento-cloud environment:checkout <environment-id>
   ```

1. SSH を使用してクラウド環境に接続します。

   ```bash
   magento-cloud ssh
   ```

1. からRabbitMQ接続の詳細とログイン資格情報を取得します。 [$CLOUD_RELATIONSHIPS$MAGENTO](../application/properties.md#relationships) 変数：

   ```bash
   echo $MAGENTO_CLOUD_RELATIONSHIPS | base64 -d | json_pp
   ```

   または

   ```bash
   php -r 'print_r(json_decode(base64_decode($_ENV["MAGENTO_CLOUD_RELATIONSHIPS"])));'
   ```

   応答で、RabbitMQ情報を確認します。例：

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

1. RabbitMQへのローカルポート転送を有効にする（プロジェクトが US-3、EU-5、AP-3 リージョンなど別のリージョンにある場合は、 ``us-3``/``eu-5``/``ap-3`` （用） ``us``）

   ```bash
   ssh -L <port-number>:rabbitmq.internal:<port-number> <project-ID>-<branch-ID>@ssh.us.magentosite.cloud
   ```

   でRabbitMQ管理 web インターフェイスにアクセスする例 `http://localhost:15672` は：

   ```bash
   ssh -L 15672:rabbitmq.internal:15672 <project-ID>-<branch-ID>@ssh.us.magentosite.cloud
   ```

1. セッションが開いている間は、ローカルワークステーションから選択したRabbitMQ クライアントを起動し、に接続するように設定できます `localhost:<portnumber>` Magento_CLOUD_RELATIONSHIPS 変数のポート番号、ユーザー名およびパスワード情報を使用します。

### アプリケーションからの接続

アプリケーションで動作しているRabbitMQに接続するには、次のようなクライアントをインストールします [amqp-utils](https://github.com/dougbarth/amqp-utils)を、内のプロジェクト依存関係として `.magento.app.yaml` ファイル。

以下に例を挙げます。

```yaml
dependencies:
    ruby:
        amqp-utils: "0.5.1"
```

PHP コンテナにログインする場合は、 `amqp-` キューを管理するために使用できるコマンド。

### PHP アプリケーションからの接続

PHP アプリケーションを使用してRabbitMQに接続するには、PHP を [ライブラリ](https://glossary.magento.com/library) をソースツリーに追加します。
