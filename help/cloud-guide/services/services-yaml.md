---
title: サービスの設定
description: クラウドインフラストラクチャ上でAdobe Commerceが使用するサービスを設定する方法を説明します。
feature: Cloud, Configuration, Services
exl-id: 48091c10-c53f-4aad-afbe-b4516653bda1
source-git-commit: 4d790bff2ba5d02ef10de5c36a2f0d140e31a407
workflow-type: tm+mt
source-wordcount: '1007'
ht-degree: 0%

---

# サービスの設定

The `services.yaml` ファイルは、MySQL、Redis、Elasticsearch、OpenSearch など、クラウドインフラストラクチャ上でAdobe Commerceがサポートし、使用するサービスを定義します。 外部のサービスプロバイダーに登録する必要はありません。 このファイルは、 `.magento` プロジェクトのディレクトリ。

デプロイスクリプトは、 `.magento` 環境に設定済みのサービスをプロビジョニングするディレクトリ。 アプリケーションに含まれる場合、そのサービスはアプリケーションで使用可能になります。 [`relationships`](../application/properties.md#relationships) のプロパティ `.magento.app.yaml` ファイル。 The `services.yaml` ファイルに _type_ および _ディスク_ 値。 サービスタイプはサービスを定義します _名前_ および _version_.

サービス設定を変更すると、デプロイメントが環境に更新されたサービスをプロビジョニングします。これは、次の環境に影響を与えます。

- 実稼動環境を含むすべてのスターター環境 `master`
- Pro 統合環境

{{pro-update-service}}

## デフォルトおよびサポートされるサービス

クラウドインフラストラクチャは、次のサービスをサポートおよびデプロイします。

- [MySQL](mysql.md)
- [レディス](redis.md)
- [RabbitMQ](rabbitmq.md)
- [Elasticsearch](elasticsearch.md)
- [OpenSearch](opensearch.md)

現在の、デフォルトのバージョンとディスクの値を表示できます。 [デフォルト `services.yaml` ファイル](https://github.com/magento/magento-cloud/blob/master/.magento/services.yaml). 以下のサンプルは、 `mysql`, `redis`, `opensearch` または `elasticsearch`、および `rabbitmq` 定義されたサービス `services.yaml` 設定ファイル：

```yaml
mysql:
    type: mysql:10.4
    disk: 5120

redis:
    type: redis:6.2

opensearch:
    type: opensearch:2  # minor version not required; uses latest
    disk: 1024

rabbitmq:
    type: rabbitmq:3.9
    disk: 1024
```

## サービス値

サービス ID とサービスタイプの設定を指定する必要があります `type: <name>:<version>`. サービスが永続的なストレージを使用する場合は、ディスク値を指定する必要があります。

次の形式を使用します。

```yaml
<service-id>:
    type: <name>:<version>
    disk: <value-MB>
```

### `service-id`

The `service-id` 値は、プロジェクト内のサービスを識別します。 小文字の英数字のみを使用できます。 `a` から `z` および `0` から `9`、例： `redis`.

この _service-id_ 値が [`relationships`](../application/properties.md#relationships) のプロパティ `.magento.app.yaml` 設定ファイル：

```yaml
relationships:
    redis: "<name>:redis"
```

各サービスタイプの複数のインスタンスに名前を付けることができます。 例えば、複数の Redis インスタンス（1 つはセッション用、もう 1 つはキャッシュ用）を使用できます。

```yaml
redis:
    type: redis:<version>

redis2:
    type: redis:<version>
```

内のサービス名の変更 `services.yaml` ファイル **完全に削除** 以下をおこないます。

- 指定した新しい名前でサービスを作成する前の既存のサービス。
- サービスの既存のデータがすべて削除されます。 Adobeでは、 [スターター環境のバックアップ](../storage/snapshots.md) 既存のサービスの名前を変更する前に、

### `type`

The `type` 値は、サービス名とバージョンを指定します。 例：

```yaml
mysql:
    type: mysql:10.4
```

### `disk`

The `disk` 値は、サービスに割り当てる永続ディスクストレージのサイズ（MB 単位）を指定します。 MySQL などの永続的なストレージを使用するサービスは、ディスク値を提供する必要があります。 Redis などの永続的なストレージの代わりにメモリを使用するサービスは、ディスク値を必要としません。

```yaml
mysql:
    type: mysql:10.4
    disk: 5120
```

プロジェクトごとの現在のデフォルトのストレージ容量は 5 GB(512 0 MB) です。 この金額は、アプリケーションと各サービスの間で配分できます。

## サービスの関係

Adobe Commerce on cloud infrastructure プロジェクトでは、サービス [関係](../application/properties.md#relationships) 設定済み `.magento.app.yaml` ファイルは、アプリケーションで使用可能なサービスを決定します。

すべてのサービス関係の設定データは、 [`$MAGENTO_CLOUD_RELATIONSHIPS`](../environment/variables-cloud.md) 環境変数。 設定データには、サービス名、タイプ、バージョンのほか、必要な接続の詳細（ポート番号、ログイン資格情報など）が含まれます。

**ローカル環境で関係を検証するには**:

1. ローカル環境で、アクティブな環境の関係を表示します。

   ```bash
   magento-cloud relationships
   ```

1. を確認します。 `service` および `type` を返します。 応答は、IP アドレスやポート番号などの接続情報を提供します。

   >短縮されたレスポンスのサンプル

   ```terminal
   redis:
       -
   ...
           type: 'redis:7.0'
           port: 6379
   elasticsearch:
       -
   ...
           type: 'opensearch:2'
           port: 9200
   database:
       -
   ...
           type: 'mysql:10.6'
           port: 3306
   ```

**リモート環境で関係を検証するには**:

1. SSH を使用してリモート環境にログインします。

1. 環境で設定されているすべてのサービスの関係設定データをリストします。

   ```bash
   echo $MAGENTO_CLOUD_RELATIONSHIPS | base64 -d | json_pp
   ```

   または、次を使用します。 `ece-tools` 関係を表示するコマンド：

   ```bash
   php ./vendor/bin/ece-tools env:config:show services
   ```

1. を確認します。 `service` および `type` を返します。 この応答は、IP アドレスとポート番号、および必要なユーザー名とパスワードの資格情報など、接続情報を提供します。

## サービスバージョン

クラウドインフラストラクチャ上のAdobe Commerceのサービスバージョンと互換性のサポートは、クラウドインフラストラクチャ上でデプロイおよびテストされたバージョンによって決まり、Adobe Commerceオンプレミスデプロイメントでサポートされるバージョンとは異なる場合があります。 詳しくは、 [必要システム構成](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/system-requirements.html) （内） _インストール_ 特定のAdobe CommerceおよびMagento Open SourceリリースでテストしたサードパーティのAdobeの依存関係のリストに関するガイドです。

### ソフトウェアの EOL チェック

デプロイメントプロセス中に、 `ece-tools` パッケージは、各サービスの提供終了日 (EOL) に対して、インストールされたサービスのバージョンを確認します。

- サービスバージョンが EOL の日付から 3 ヶ月以内の場合、デプロイログに通知が表示されます。
- EOL の日付が過去の場合、警告通知が表示されます。

ストアのセキュリティを維持するには、EOL に到達する前に、インストールされているソフトウェアのバージョンを更新します。 EOL の日付は、 [ece-tools&#39; `eol.yaml` ファイル](https://github.com/magento/ece-tools/blob/develop/config/eol.yaml).

### OpenSearch に移行

{{elasticsearch-support}}

Adobe Commerceバージョン 2.4.4 以降の場合は、 [OpenSearch サービスを設定する](opensearch.md).

## サービスバージョンの変更

インストールされているサービスのバージョンをアップグレードして、クラウド環境にデプロイされているAdobe Commerceのバージョンとの互換性を確保することができます。

インストールされているサービスのサービスバージョンを直接ダウングレードすることはできません。 ただし、必要なバージョンのサービスを作成できます。 詳しくは、 [サービスバージョンのダウングレード](#downgrade-version).

### インストールされているサービスバージョンのアップグレード

インストールされているサービスのバージョンをアップグレードするには、 `services.yaml` ファイル。

1. 次を変更： [`type`](#type) の値 `.magento/services.yaml` ファイル：

   > 元のサービス定義

   ```yaml
   mysql:
       type: mysql:10.3
       disk: 2048
   ```

   > 更新されたサービス定義

   ```yaml
   mysql:
       type: mysql:10.4
       disk: 5120
   ```

1. コードの変更を追加、コミット、およびプッシュします。

   ```bash
   git add .magento/services.yaml
   ```

   ```bash
   git commit -m "Upgrade MySQL from MariaDB 10.3 to 10.4."
   ```

   ```bash
   git push origin <branch-name>
   ```

### バージョンをダウングレード

インストールされたサービスを直接ダウングレードすることはできません。 次の 2 つのオプションがあります。

1. 既存のサービスの名前を新しいバージョンに変更します。これにより、既存のサービスとデータが削除され、新しいサービスが追加されます。

1. サービスを作成し、既存のサービスからデータを保存します。

サービスのバージョンを変更する場合は、 `services.yaml` ファイルを作成し、 `.magento.app.yaml` ファイル。

**既存のサービスの名前を変更してサービスのバージョンをダウングレードするには**:

1. の既存のサービスの名前を変更 `.magento/services.yaml` ファイルを編集し、バージョンを変更します。

   >[!WARNING]
   >
   >既存のサービスの名前を変更すると、そのサービスは置き換えられ、すべてのデータが削除されます。 データを保持する必要がある場合、既存のデータの名前を変更する代わりにサービスを作成します。

   例えば、MariaDB のバージョンを _mysql_ バージョン 10.4 から 10.3 にサービスを移行するには、既存の _service-id_ および _type_ 設定。

   > オリジナル `services.yaml` 定義

   ```yaml
   mysql:
       type: mysql:10.4
       disk: 5120
   ```

   > 新規 `services.yaml` 定義

   ```yaml
   mysql2:
        type: mysql:10.3
        disk: 5120
   ```

1. の関係を更新します。 `.magento.app.yaml` ファイル。

   > オリジナル `.magento.app.yaml` 設定

   ```yaml
   relationships:
       database: "mysql:mysql"
   ```

   > 更新済み `.magento.app.yaml` 設定

   ```yaml
   relationships:
       database: "mysql2:mysql"
   ```

1. コードの変更を追加、コミット、およびプッシュします。

**サービスを作成してサービスをダウングレードするには**:

1. にサービス定義を追加する `services.yaml` ファイルをダウンロードし、バージョン仕様を含むプロジェクト用のファイルです。 詳しくは、 _mysql2_ 次の例では、

   > services.yaml

   ```yaml
   mysql:
       type: mysql:10.4
       disk: 5120
   mysql2:
       type: mysql:10.3
       disk: 5120
   ```

1. 関係の設定を `.magento.app.yaml` ファイルを使用して、新しいサービスを使用します。

   > オリジナル `.magento.app.yaml` 設定

   ```yaml
   relationships:
       database: "mysql:mysql"
   ```

   > 新規 `.magento.app.yaml` 設定

   ```yaml
   relationships:
       database: "mysql2:mysql"
   ```

1. コードの変更を追加、コミット、およびプッシュします。
