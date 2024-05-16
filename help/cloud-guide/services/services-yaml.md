---
title: サービスの設定
description: クラウドインフラストラクチャー上でAdobe Commerceが使用するサービスを設定する方法について説明します。
feature: Cloud, Configuration, Services
exl-id: 48091c10-c53f-4aad-afbe-b4516653bda1
source-git-commit: 4d790bff2ba5d02ef10de5c36a2f0d140e31a407
workflow-type: tm+mt
source-wordcount: '1007'
ht-degree: 0%

---

# サービスの設定

この `services.yaml` ファイルは、MySQL、Redis、Elasticsearchまたは OpenSearch など、クラウドインフラストラクチャー上のAdobe Commerceでサポートされ、使用されるサービスを定義します。 外部サービスプロバイダーに登録する必要はありません。 このファイルの保存先 `.magento` プロジェクトのディレクトリ。

デプロイスクリプトでは、の設定ファイルを使用します。 `.magento` 設定済みのサービスを環境にプロビジョニングするディレクトリ。 アプリケーションにサービスが含まれていると、そのサービスを使用できるようになります [`relationships`](../application/properties.md#relationships) のプロパティ `.magento.app.yaml` ファイル。 この `services.yaml` ファイルには次が含まれています _タイプ_ および _ディスク_ 値。 サービスタイプはサービスを定義します。 _名前_ および _version_.

サービス設定を変更すると、デプロイメントによって更新されたサービスを環境にプロビジョニングされます。これにより、次の環境が影響を受けます。

- 実稼動を含むすべてのスターター環境 `master`
- Pro 統合環境

{{pro-update-service}}

## デフォルトおよびサポートされているサービス

クラウドインフラストラクチャは、次のサービスをサポートおよびデプロイします。

- [MySQL](mysql.md)
- [Redis](redis.md)
- [RabbitMQ](rabbitmq.md)
- [Elasticsearch](elasticsearch.md)
- [OpenSearch](opensearch.md)

現在ののデフォルトのバージョンとディスクの値を表示できます。 [default `services.yaml` ファイル](https://github.com/magento/magento-cloud/blob/master/.magento/services.yaml). 次の例に、 `mysql`, `redis`, `opensearch` または `elasticsearch`、および `rabbitmq` で定義されているサービス `services.yaml` 設定ファイル：

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

サービス ID とサービスタイプを設定する必要があります `type: <name>:<version>`. サービスが永続的なストレージを使用する場合は、ディスク値を指定する必要があります。

次の形式を使用します。

```yaml
<service-id>:
    type: <name>:<version>
    disk: <value-MB>
```

### `service-id`

この `service-id` 値は、プロジェクト内のサービスを識別します。 使用できるのは小文字の英数字のみです。 `a` 対象： `z` および `0` 対象： `9`（例：） `redis`.

この _service-id_ 値は [`relationships`](../application/properties.md#relationships) のプロパティ `.magento.app.yaml` 設定ファイル：

```yaml
relationships:
    redis: "<name>:redis"
```

各サービスタイプの複数のインスタンスに名前を付けることができます。 例えば、複数の Redis インスタンスを使用できます。1 つはセッション用、もう 1 つはキャッシュ用です。

```yaml
redis:
    type: redis:<version>

redis2:
    type: redis:<version>
```

でのサービス名の変更 `services.yaml` ファイル **完全に削除** 以下を実行します。

- 指定した新しい名前でサービスを作成する前の既存のサービス。
- サービスの既存のデータがすべて削除されます。 Adobeでは、次のことを強くお勧めします [スターター環境のバックアップ](../storage/snapshots.md) 既存のサービスの名前を変更する前に、

### `type`

この `type` 値は、サービス名とバージョンを指定します。 例：

```yaml
mysql:
    type: mysql:10.4
```

### `disk`

この `disk` この値は、サービスに割り当てる永続的なディスク記憶域のサイズ （MB 単位）を指定します。 MySQL などの永続ストレージを使用するサービスでは、ディスク値を指定する必要があります。 Redis などの永続的なストレージの代わりにメモリを使用するサービスでは、ディスク値は必要ありません。

```yaml
mysql:
    type: mysql:10.4
    disk: 5120
```

プロジェクトごとの現在のデフォルトのストレージ量は 5 GB （512 0 MB）です。 この金額は、アプリケーションと各サービスの間で配分できます。

## サービスの関係

クラウドインフラストラクチャプロジェクトのAdobe Commerceでのサービス [の関係](../application/properties.md#relationships) で設定 `.magento.app.yaml` ファイルは、アプリケーションで使用可能なサービスを決定します。

すべてのサービス関係の設定データは、 [`$MAGENTO_CLOUD_RELATIONSHIPS`](../environment/variables-cloud.md) 環境変数。 設定データには、サービス名、タイプ、バージョンのほか、ポート番号やログイン資格情報など、必要な接続の詳細が含まれます。

**ローカル環境で関係を検証するには**:

1. ローカル環境で、アクティブな環境の関係を表示します。

   ```bash
   magento-cloud relationships
   ```

1. 「」を確認します `service` および `type` 応答から。 応答では、IP アドレスやポート番号などの接続情報が提供されます。

   >短縮されたサンプル応答

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

1. 環境で設定されたすべてのサービスの関係設定データをリストします。

   ```bash
   echo $MAGENTO_CLOUD_RELATIONSHIPS | base64 -d | json_pp
   ```

   または、次を使用します `ece-tools` 関係を表示するコマンド：

   ```bash
   php ./vendor/bin/ece-tools env:config:show services
   ```

1. 「」を確認します `service` および `type` 応答から。 応答では、IP アドレスやポート番号、必要なユーザー名やパスワードの認証情報などの接続情報が提供されます。

## サービスのバージョン

クラウドインフラストラクチャにおけるAdobe Commerceのサービスバージョンと互換性のサポートは、クラウドインフラストラクチャにデプロイされテストされたバージョンによって決まり、Adobe Commerceのオンプレミスデプロイメントでサポートされているバージョンとは異なる場合があります。 参照： [必要システム構成](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/system-requirements.html) が含まれる _インストール_ Adobe CommerceとMagento Open Sourceの特定のリリースでAdobeがテストした、サードパーティ製ソフトウェアの依存関係のリストを示すガイド。

### ソフトウェアの EOL チェック

デプロイメントプロセス中、 `ece-tools` パッケージは、インストールされているサービスバージョンを各サービスの提供終了（EOL）日と照合して確認します。

- サービスのバージョンが提供終了（EOL）日から 3 か月以内の場合、デプロイログに通知が表示されます。
- EOL 日が過去の場合は、警告通知が表示されます。

ストアのセキュリティを維持するには、インストールされているソフトウェアのバージョンを EOL になる前に更新してください。 の EOL 日を確認できます。 [ece-tools&#39; `eol.yaml` ファイル](https://github.com/magento/ece-tools/blob/develop/config/eol.yaml).

### OpenSearch への移行

{{elasticsearch-support}}

Adobe Commerce バージョン 2.4.4 以降については、を参照してください。 [OpenSearch サービスの設定](opensearch.md).

## サービスバージョンの変更

インストールしたサービスバージョンは、クラウド環境にデプロイされたAdobe Commerceのバージョンと互換性を持つようにアップグレードできます。

インストールされているサービスのバージョンを直接ダウングレードすることはできません。 ただし、必要なバージョンを持つサービスを作成できます。 参照： [サービス バージョンのダウングレード](#downgrade-version).

### インストールされているサービスのバージョンのアップグレード

インストールされているサービスのバージョンは、でサービス設定を更新することでアップグレードできます。 `services.yaml` ファイル。

1. 変更： [`type`](#type) のサービスに対する値 `.magento/services.yaml` ファイル：

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

1. コードの変更を追加、コミット、プッシュします。

   ```bash
   git add .magento/services.yaml
   ```

   ```bash
   git commit -m "Upgrade MySQL from MariaDB 10.3 to 10.4."
   ```

   ```bash
   git push origin <branch-name>
   ```

### ダウングレード版

インストールされているサービスを直接ダウングレードすることはできません。 次の 2 つのオプションがあります。

1. 既存のサービスの名前を新しいバージョンに変更します。これにより、既存のサービスとデータが削除され、新しいサービスが追加されます。

1. サービスを作成し、既存のサービスからデータを保存します。

サービスのバージョンを変更する場合は、でサービス設定を更新する必要があります `services.yaml` でファイルを作成し、関係を更新します。 `.magento.app.yaml` ファイル。

**既存のサービスの名前を変更してサービスバージョンをダウングレードするには**:

1. の既存のサービスの名前を変更する `.magento/services.yaml` ファイルに入力して、バージョンを変更します。

   >[!WARNING]
   >
   >既存のサービスの名前を変更すると、そのサービスが置き換えられ、すべてのデータが削除されます。 データを保持する必要がある場合は、既存のサービスの名前を変更する代わりに、サービスを作成します。

   例えば、の MariaDB バージョンをダウングレードするには _mysql_ サービスをバージョン 10.4 から 10.3 に変更し、既存のを _service-id_ および _タイプ_ 設定。

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

1. での関係の更新 `.magento.app.yaml` ファイル。

   > オリジナル `.magento.app.yaml` 設定

   ```yaml
   relationships:
       database: "mysql:mysql"
   ```

   > 更新日 `.magento.app.yaml` 設定

   ```yaml
   relationships:
       database: "mysql2:mysql"
   ```

1. コードの変更を追加、コミット、プッシュします。

**サービスを作成してサービスをダウングレードするには**:

1. にサービス定義を追加する `services.yaml` ダウングレードされたバージョンの仕様を含むプロジェクトのファイル。 参照： _mysql2_ 次の例では、

   > services.yaml

   ```yaml
   mysql:
       type: mysql:10.4
       disk: 5120
   mysql2:
       type: mysql:10.3
       disk: 5120
   ```

1. での関係設定の変更 `.magento.app.yaml` 新しいサービスを使用するファイル。

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

1. コードの変更を追加、コミット、プッシュします。
