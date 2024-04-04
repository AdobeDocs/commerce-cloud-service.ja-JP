---
title: MySQL サービスを設定する
description: クラウドインフラストラクチャ上のAdobe Commerceを使用して、永続的なデータストレージの MySQL サービスを管理する方法を説明します。
feature: Cloud, Services, Storage
exl-id: 70820d00-8b82-4b60-87e4-ea98fd7ffcb2
source-git-commit: 9be8d1e062ab3dba86bc4d9c9ee8b8ece33d5b75
workflow-type: tm+mt
source-wordcount: '773'
ht-degree: 1%

---

# MySQL サービスを設定する

The `mysql` サービスは、次の基準で永続的なデータストレージを提供します。 [MariaDB](https://mariadb.com/) バージョン 10.2～10.4（をサポート） [XtraDB](https://docs.percona.com/percona-xtradb-cluster/8.0/index.html) ストレージエンジンと、MySQL 5.6 および 5.7 の機能が再実装されました。

MariaDB 10.4 でのインデックス再作成は、他の MariaDB または MySQL バージョンと比べて時間がかかります。 詳しくは、 [インデクサー](https://experienceleague.adobe.com/docs/commerce-operations/performance-best-practices/configuration.html#indexers) （内） _パフォーマンスのベストプラクティス_ ガイド。

>[!WARNING]
>
>MariaDB をバージョン 10.1 から 10.2 にアップグレードする際は注意が必要です。MariaDB 10.1 はをサポートする最後のバージョンです。 _XtraDB_ ストレージエンジンとして。 MariaDB 10.2 はを使用します。 _InnoDB_ ストレージエンジン用。 10.1 から 10.2 にアップグレードした後は、変更をロールバックできません。 Adobe Commerceは両方のストレージエンジンをサポートしていますが、MariaDB 10.2 との互換性を確認するには、プロジェクトで使用する拡張機能や他のシステムを確認する必要があります。詳しくは、 [10.1 と 10.2 の間の互換性のない変更](https://mariadb.com/kb/en/upgrading-from-mariadb-101-to-mariadb-102/#incompatible-changes-between-101-and-102).

{{service-instruction}}

**MySQL を有効にするには**:

1. 必要な名前、タイプ、およびディスク値（MB 単位）を `.magento/services.yaml` ファイル。

   ```yaml
   mysql:
       type: mysql:<version>
       disk: 5120
   ```

   >[!TIP]
   >
   >MySQL エラー（例： ） `PDO Exception: MySQL server has gone away`は、ディスク容量が不足した結果として発生する可能性があります。 サービスに十分なディスク領域が割り当てられていることを [`.magento/services.yaml`](services-yaml.md#disk) ファイル。

1. 関係を `.magento.app.yaml` ファイル。

   ```yaml
   relationships:
       database: "mysql:mysql"
   ```

1. コードの変更を追加、コミット、およびプッシュします。

   ```bash
   git add .magento/services.yaml .magento.app.yaml && git commit -m "Enable mysql service" && git push origin <branch-name>
   ```

1. [サービスの関係を確認します](services-yaml.md#service-relationships).

{{service-change-tip}}

## MySQL データベースを設定

MySQL データベースを設定する際には、次のオプションを使用できます。

- **`schemas`** — スキーマはデータベースを定義します。 デフォルトのスキーマは、 `main` データベース。
- **`endpoints`** — 各エンドポイントは、特定の権限を持つ秘密鍵証明書を表します。 デフォルトのエンドポイントは、 `mysql`( ) `admin` へのアクセス `main` データベース。
- **`properties`** — プロパティは、追加のデータベース設定を定義するために使用されます。

次に、 `.magento/services.yaml` ファイル：

```yaml
mysql:
    type: mysql:10.4
    disk: 5120
    configuration:
        properties:
            optimizer_switch: "rowid_filter=off"
            optimizer_use_condition_selectivity: 1
```

The `properties` 上記の例では、デフォルトの `optimizer` 設定： [パフォーマンスのベストプラクティスガイドで推奨](https://experienceleague.adobe.com/docs/commerce-operations/performance-best-practices/configuration.html#indexers).

**MariaDB 設定オプション**:

| オプション | 説明 | デフォルト値 |
| -------------------- | --------------------------------------------------- | ------------------ |
| `default_charset` | デフォルトの文字セット。 | utf8mb4 |
| `default_collation` | デフォルトの照合順序。 | utf8mb4_unicode_ci |
| `max_allowed_packet` | パケットの最大サイズ (MB)。 範囲 `1` から `100`. | 16 |
| `optimizer_switch` | クエリオプティマイザーの値を設定します。 詳しくは、 [MariaDB ドキュメント](https://mariadb.com/kb/en/server-system-variables/#optimizer_switch). | |
| `optimizer_use_condition_selectivity` | オプティマイザが使用する統計を選択します。 範囲 `1` から `5`. 詳しくは、 [MariaDB ドキュメント](https://mariadb.com/kb/en/server-system-variables/#optimizer_use_condition_selectivity). | 4（10.4 以降） |

### 複数のデータベースユーザーの設定

必要に応じて、 `main` データベース。

デフォルトでは、 `mysql` データベースに対する管理者アクセス権を持つ 複数のデータベースユーザーを設定するには、 `services.yaml` ファイルを作成し、 `.magento.app.yaml` ファイル。 Pro ステージング環境および実稼動環境の場合、 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) をクリックして、追加のユーザーをリクエストします。

ネストされた配列を使用して、特定のユーザーアクセスのエンドポイントを定義します。 各エンドポイントは、1 つ以上のスキーマ（データベース）へのアクセスと、それぞれに対する異なるレベルの権限を指定できます。

有効な権限レベルは次のとおりです。

- `ro`:SELECT クエリのみを使用できます。
- `rw`:SELECT クエリと、INSERT、UPDATE、およびDELETEクエリを使用できます。
- `admin`:DDL クエリ（CREATE TABLE、DROP TABLE など）を含むすべてのクエリを使用できます。

例：

```yaml
mysql:
    type: mysql:10.4
    disk: 5120
    configuration:
        schemas:
            - main
        endpoints:
            admin:
                default_schema: main
                privileges:
                    main: admin
            reporter:
                privileges:
                    main: ro
            importer:
                privileges:
                    main: rw
        properties:
            optimizer_switch: "rowid_filter=off"
            optimizer_use_condition_selectivity: 1
```

前の例では、 `admin` エンドポイントは、管理者レベルでへのアクセスを提供します。 `main` データベース、 `reporter` エンドポイントは読み取り専用アクセスを提供し、 `importer` endpoint は読み取り/書き込みアクセスを提供します。つまり、

- The `admin` ユーザーは、データベースを完全に制御できます。
- The `reporter` ユーザーは SELECT 権限のみを持っています。
- The `importer` ユーザーに SELECT、INSERT、UPDATE およびDELETE権限がある。

上記の例で定義したエンドポイントを `relationships` のプロパティ `.magento.app.yaml` ファイル。 例：

```yaml
relationships:
    database: "mysql:admin"
    databasereporter: "mysql:reporter"
    databaseimporter: "mysql:importer"
```

>[!NOTE]
>
>1 人の MySQL ユーザーを設定した場合、 [`DEFINER`](https://dev.mysql.com/doc/refman/8.0/en/show-grants.html) ストアド・プロシージャおよびビューのアクセス制御メカニズム

## データベースに接続

MariaDB データベースに直接アクセスするには、SSH を使用してリモートクラウド環境にログインし、データベースに接続する必要があります。

1. SSH を使用してリモート環境にログインします。

   ```bash
   magento-cloud ssh
   ```

1. から MySQL ログイン資格情報を取得します。 `database` および `type` プロパティ [$MAGENTO_CLOUD_RELATIONSHIPS](../application/properties.md#relationships) 変数を使用します。

   ```bash
   echo $MAGENTO_CLOUD_RELATIONSHIPS | base64 -d | json_pp
   ```

   または

   ```bash
   php -r 'print_r(json_decode(base64_decode($_ENV["MAGENTO_CLOUD_RELATIONSHIPS"])));'
   ```

   応答で、MySQL 情報を見つけます。 例：

   ```json
   "database" : [
      {
         "password" : "",
         "rel" : "mysql",
         "hostname" : "nnnnnnnn.mysql.service._.magentosite.cloud",
         "service" : "mysql",
         "host" : "database.internal",
         "ip" : "###.###.###.###",
         "port" : 3306,
         "path" : "main",
         "cluster" : "projectid-integration-id",
         "query" : {
            "is_master" : true
         },
         "type" : "mysql:10.3",
         "username" : "user",
         "scheme" : "mysql"
      }
   ],
   ```

1. データベースに接続します。

   - スターターの場合は、次のコマンドを使用します。

     ```bash
     mysql -h database.internal -u <username>
     ```

   - Pro の場合、ホスト名、ポート番号、ユーザー名、パスワードを指定して、次のコマンドを使用します。 `$MAGENTO_CLOUD_RELATIONSHIPS` 変数を使用します。

     ```bash
     mysql -h <hostname> -P <number> -u <username> -p'<password>'
     ```

>[!TIP]
>
>以下を使用すると、 `magento-cloud db:sql` コマンドを使用してリモート・データベースに接続し、SQL コマンドを実行します。

## セカンダリデータベースに接続

>[!IMPORTANT]
>
>この機能は、実稼動およびステージング用クラスターでのみ使用できます。

データベースのパフォーマンスを向上させたり、データベースのロックの問題を解決するために、セカンダリデータベースに接続する必要が生じる場合があります。 この設定が必要な場合は、 `"port" : 3304` 接続を確立するために。 詳しくは、 [MySQL スレーブ接続を設定するためのベストプラクティス](https://experienceleague.adobe.com/docs/commerce-operations/implementation-playbook/best-practices/planning/mysql-configuration.html) トピック _実装のベストプラクティス_ ガイド。

## トラブルシューティング

MySQL の問題のトラブルシューティングについては、次のAdobe Commerceサポート記事を参照してください。

- [処理に時間のかかるクエリの確認と MySQL の処理](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/database/checking-slow-queries-and-processes-mysql.html)
- [クラウド上にデータベースダンプを作成する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/how-to/create-database-dump-on-cloud.html)
- [データ移行ツールのトラブルシューティング](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/data-migration-tool-troubleshooting.html)
- [Adobe Commerceのアップグレード：コンパクトから動的テーブル 2.2.x、2.3.x から 2.4.x へ](https://experienceleague.adobe.com/docs/commerce-operations/implementation-playbook/best-practices/maintenance/commerce-235-upgrade-prerequisites-mariadb.html)
