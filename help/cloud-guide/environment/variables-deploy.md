---
title: 変数のデプロイ
description: クラウドインフラストラクチャー上でのAdobe Commerceのデプロイフェーズでアクションを制御する環境変数のリストを参照してください。
feature: Cloud, Configuration, Cache, Deploy, SCD, Storage, Search
recommendations: noDisplay, catalog
role: Developer
exl-id: 673880b5-830b-4837-ac0c-5fa5643ae34c
source-git-commit: b7307faf046884c13cba852df69d4fa9977e9a17
workflow-type: tm+mt
source-wordcount: '2185'
ht-degree: 0%

---

# 変数のデプロイ

次の _deploy_ 変数は、デプロイフェーズでのアクションを制御し、の値を継承および上書きできます。 [グローバル変数](variables-global.md). これらの変数を `deploy` ステージ `.magento.env.yaml` ファイル：

```yaml
stage:
  deploy:
    DEPLOY_VARIABLE_NAME: value
```

ビルドおよびデプロイプロセスのカスタマイズに関する詳細情報：

- [デプロイメント設定](configure-env-yaml.md)
- [デプロイメントプロセス](../deploy/process.md)

## `CACHE_CONFIGURATION`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

Redis ページとデフォルトのキャッシュを設定します。 を設定するとき `cm_cache_backend_redis` パラメーターが見つかりました `server`, `port`、および `database` オプション。

```yaml
stage:
  deploy:
    CACHE_CONFIGURATION:
      frontend:
        default:
          backend: file
        page_cache:
          backend: file
```

{{merge-options}}

次の例では、新しい値を既存の設定に結合します。

```yaml
stage:
  deploy:
    CACHE_CONFIGURATION:
      _merge: true
      frontend:
        default:
          backend_options:
            database: 10
        page_cache:
          backend_options:
            database: 11
```

次の例では、 [Redis プリロード機能](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cache/redis/redis-pg-cache.html#redis-preload-feature) で定義されているように _設定ガイド_:

```yaml
stage:
  deploy:
    CACHE_CONFIGURATION:
      _merge: true
      frontend:
        default:
          id_prefix: '061_'
          backend_options:
            preload_keys:
              - '061_EAV_ENTITY_TYPES:hash'
              - '061_GLOBAL_PLUGIN_LIST:hash'
              - '061_DB_IS_UP_TO_DATE:hash'
              - '061_SYSTEM_DEFAULT:hash'
```

カスタムを使用するには [REDIS_BACKEND](#redis_backend) モデル（許可リストからだけでなく）を設定し、 `_custom_redis_backend` 対するオプション `true` 次の例のように、正しい検証を有効にします。

```yaml
stage:
  deploy:
    CACHE_CONFIGURATION:
      frontend:
        default:
          _custom_redis_backend: true
          backend: '\CustomRedisModel'
```

## `CLEAN_STATIC_FILES`

- **デフォルト**—`true`
- **バージョン**—Adobe Commerce 2.1.4 以降

クリーニングを有効または無効にします [静的コンテンツファイル](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cli/static-view/static-view-file-deployment.html) ビルドまたはデプロイフェーズで生成されます。 デフォルト値を使用 _true_ ベストプラクティスとして開発する。

- **`true`** – 更新された静的コンテンツをデプロイする前に、既存の静的コンテンツをすべて削除します。
- **`false`** – 生成されたコンテンツに新しいバージョンが含まれている場合、デプロイメントでは既存の静的コンテンツファイルのみが上書きされます。

静的コンテンツを別のプロセスで変更する場合、値をに設定します _偽_.

```yaml
stage:
  deploy:
    CLEAN_STATIC_FILES: false
```

デプロイ前に静的ビューファイルをクリーンアップしないと、以前のバージョンを削除せずに既存のファイルに更新をデプロイすると、問題が発生する可能性があります。 理由： [静的ファイルフォールバック](https://developer.adobe.com/commerce/frontend-core/guide/caching/#clean-static-files-cache) ディレクトリに同じファイルの複数のバージョンが含まれている場合、ルールやフォールバック操作で誤ったファイルが表示される可能性があります。

## `CRON_CONSUMERS_RUNNER`

- **デフォルト**—`cron_run = false`, `max_messages = 1000`
- **バージョン**—Adobe Commerce 2.2.0 以降

この環境変数を使用して、メッセージキューがデプロイメント後に実行されていることを確認します。

- `cron_run`- パラメータの有効/無効を切り替えるブール値 `consumers_runner` cron ジョブ（デフォルト = `false`）に設定します。
- `max_messages` – 各消費者が終了するまでに処理する必要があるメッセージの最大数を指定する数値（デフォルト = `1000`）に設定します。 この値は、に設定することができます。 `0` 消費者の終了を防止する。
- `consumers` – 実行するコンシューマーを指定する文字列の配列。 空の配列が実行される _all_ 消費者。

- `multiple_processes` – 各消費者に対して生成するプロセスの数を指定する数値。 Commerceでサポート **2.4.4** 以上。

>[!NOTE]
>
>メッセージキューのリストを返す `consumers`、を実行します `./bin/magento queue:consumers:list` コマンドをリモート環境で実行します。

特定のを実行する配列の例 `consumers` および `multiple_processes` 各消費者に対してスポーンするには：

```yaml
stage:
  deploy:
    CRON_CONSUMERS_RUNNER:
      cron_run: true
      max_messages: 1000
      consumers:
        - example_consumer_1
        - example_consumer_2
-     multiple_processes:
        example_consumer_1: 4
        example_consumer_2: 3
```

すべてを実行する空の配列の例 `consumers`:

```yaml
stage:
  deploy:
    CRON_CONSUMERS_RUNNER:
      cron_run: true
      max_messages: 1000
      consumers: []
```

デフォルトでは、デプロイメントプロセスはのすべての設定を `env.php` ファイル。 参照： [メッセージキューの管理](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/message-queues/manage-message-queues.html) が含まれる _Commerce設定ガイド_ オンプレミスのAdobe Commerceの場合。

## `CONSUMERS_WAIT_FOR_MAX_MESSAGES`

- **デフォルト**—`false`
- **バージョン**—Adobe Commerce 2.2.0 以降

方法を設定 `consumers` 次のいずれかのオプションを選択して、メッセージキューからのメッセージを処理します。

- `false`—`Consumers` キュー内の使用可能なメッセージを処理し、TCP 接続を閉じて終了します。 `Consumers` 処理されたメッセージの数がよりも少ない場合でも、追加のメッセージがキューに入るのを待たないでください `max_messages` で指定された値 `CRON_CONSUMERS_RUNNER` 変数をデプロイします。

- `true`—`Consumers` メッセージの最大数（）に達するまで、メッセージキューからのメッセージの処理を続行します`max_messages`）で指定されます `CRON_CONSUMERS_RUNNER` tcp 接続を閉じてコンシューマープロセスを終了する前に、変数をデプロイします。 に達する前にキューが空になった場合 `max_messages`の場合、消費者はさらに多くのメッセージが届くのを待ちます。

>[!WARNING]
>
>ワーカーを使用して実行する場合 `consumers` cron ジョブを使用する代わりに、この変数を true に設定します。

```yaml
stage:
  deploy:
    CONSUMERS_WAIT_FOR_MAX_MESSAGES: false
```

## `CRYPT_KEY`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

>[!WARNING]
>
>を `CRYPT_KEY` を通じた価値 [!DNL Cloud Console] の代わりに `.magento.env.yaml` お使いの環境のソースコードリポジトリでキーが公開されるのを回避するためのファイル。 参照： [環境変数とプロジェクト変数の設定](https://experienceleague.adobe.com/docs/commerce-cloud-service/user-guide/project/overview.html#configure-environment).

インストール処理を行わずに、ある環境から別の環境にデータベースを移動する場合は、対応する暗号化情報が必要です。 Adobe Commerceは、 [!DNL Cloud Console] as the `crypt/key` の値 `env.php` ファイル。

## `DATABASE_CONFIGURATION`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

でデータベースを定義した場合 [relationships プロパティ](../application/properties.md#relationships) の `.magento.app.yaml` ファイルに保存されています。データベース接続を配置にカスタマイズできます。

```yaml
stage:
  deploy:
    DATABASE_CONFIGURATION:
      some_config: 'some_value'
```

{{merge-options}}

次の例では、新しい値を既存の設定に結合します。

```yaml
stage:
  deploy:
    DATABASE_CONFIGURATION:
      some_config: 'some_new_value'
      _merge: true
```

また、テーブルプレフィックスを設定することもできます。

>[!WARNING]
>
>テーブル接頭辞で結合オプションを使用しない場合は、デフォルトの接続設定を指定する必要があります。指定しないと、配備の検証が失敗します。

次の例では、 `ece_` を使用する代わりに、デフォルトの接続設定を使用してテーブルのプレフィックスを指定する `_merge` オプション：

```yaml
stage:
  deploy:
    DATABASE_CONFIGURATION:
      connection:
        default:
          username: user
          host: host
          dbname: magento
          password: password
      table_prefix: 'ece_'
```

サンプル出力：

```terminal
MariaDB [main]> SHOW TABLES;
+-------------------------------------+
| Tables_in_main                      |
+-------------------------------------+
| ece_admin_passwords                 |
| ece_admin_system_messages           |
| ece_admin_user                      |
| ece_admin_user_session              |
| ece_adminnotification_inbox         |
| ece_amazon_customer                 |
| ece_authorization_rule              |
| ece_cache                           |
| ece_cache_tag                       |
| ece_captcha_log                     |
...
```

## `ELASTICSUITE_CONFIGURATION`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.2.0 以降

カスタマイズ内容を保持 [!DNL Elastic Suite] デプロイメント間のサービス設定で、メインのの「system/default/smile_elasticsuite_core_base_settings」セクションでそれを使用します [!DNL Elastic Suite] 設定。 次の場合 [!DNL Elastic Suite] composer パッケージがインストールされ、自動的に設定されます。

```yaml
stage:
  deploy:
    ELASTICSUITE_CONFIGURATION:
      es_client:
        servers: 'remote-host:9200'
      indices_settings:
        number_of_shards: 1
        number_of_replicas: 0
```

{{merge-options}}

次の例では、新しい値を既存の設定に結合します。

```yaml
stage:
  deploy:
    ELASTICSUITE_CONFIGURATION:
      indices_settings:
        number_of_shards: 3
        number_of_replicas: 2
      _merge: true
```

**既知の制限事項**:

- 検索エンジンを以外のタイプに変更する `elasticsuite` 適切な検証エラーを伴うデプロイ失敗の原因
- Elasticsearchサービスを削除すると、デプロイが失敗し、適切な検証エラーが表示されます

>[!NOTE]
>
>の使用またはトラブルシューティングの詳細 [!DNL Elastic Suite] Adobe Commerceとプラグインします。を参照してください。 [[!DNL Elastic Suite] 詳細を見る](https://github.com/Smile-SA/elasticsuite).

## `ENABLE_GOOGLE_ANALYTICS`

- **デフォルト**—`false`
- **バージョン**—Adobe Commerce 2.1.4 以降

ステージング環境および統合環境にデプロイする場合に、Google Analyticsを有効または無効にします。 デフォルトでは、Google Analyticsは実稼動環境でのみ true になります。 この値をに設定 `true` ステージング環境および統合環境でGoogle Analyticsを有効にする場合。

- **`true`**- ステージング環境と統合環境でGoogle Analyticsを有効にします。
- **`false`**- ステージング環境と統合環境でのGoogle Analyticsを無効にします。

を追加 `ENABLE_GOOGLE_ANALYTICS` 環境変数をに設定 `deploy` のステージ `.magento.env.yaml` ファイル：

```yaml
stage:
  deploy:
    ENABLE_GOOGLE_ANALYTICS: true
```

>[!NOTE]
>
>デプロイプロセスでは、常に実稼動環境でGoogle Analyticsを有効にします。

## `FORCE_UPDATE_URLS`

- **デフォルト**—`true`
- **バージョン**—Adobe Commerce 2.1.4 以降

Pro または Starter のステージング環境および実稼動環境にデプロイメントすると、この変数は、データベース内のAdobe Commerceのベース URL を、で指定されたプロジェクト URL に置き換えます。 [`MAGENTO_CLOUD_ROUTES`](variables-cloud.md) 変数。 この設定を使用して、 [UPDATE_URLS](#update_urls) 変数をデプロイします。ステージング環境または実稼動環境にデプロイする場合は無視されます。

```yaml
stage:
  deploy:
    FORCE_UPDATE_URLS: true
```

## `LOCK_PROVIDER`

- **デフォルト**—`file`
- **バージョン**—Adobe Commerce 2.2.5 以降

ロックプロバイダーは、重複した cron ジョブや cron グループの起動を防ぎます。 の使用 `file` 実稼動環境でプロバイダーをロックします。 スターター環境と Pro 統合環境は、 [MAGENTO_CLOUD_LOCKS_DIR](variables-cloud.md) 変数、など `ece-tools` が以下を適用します `db` プロバイダを自動的にロックします。

```yaml
stage:
  deploy:
    LOCK_PROVIDER: "db"
```

参照： [ロックの設定](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/tutorials/lock-provider.html) が含まれる _インストールガイド_.

## `MYSQL_USE_SLAVE_CONNECTION`

- **デフォルト**—`false`
- **バージョン**—Adobe Commerce 2.1.4 以降

>[!TIP]
>
>この `MYSQL_USE_SLAVE_CONNECTION` 変数は、クラウドインフラストラクチャー上のAdobe Commerce ステージング環境および実稼動 Pro クラスター環境でのみサポートされ、スタータープロジェクトではサポートされません。

Adobe Commerceは、複数のデータベースを非同期で読み取ることができます。 をに設定 `true` を自動的に使用するには _読み取り専用_ 非マスターノードで読み取り専用トラフィックを受信するデータベースへの接続。 この接続では、読み取り/書き込みトラフィックを処理するノードが 1 つだけなので、ロード・バランシングによってパフォーマンスが向上します。 をに設定 `false` 既存の読み取り専用接続配列を `env.php` ファイル。

```yaml
stage:
  deploy:
    MYSQL_USE_SLAVE_CONNECTION: true
```

いつ `MYSQL_USE_SLAVE_CONNECTION` 変数はに設定されています。 `true`, `synchronous_replication` パラメーターはに設定されています。 `true` デフォルトでは、 `env.php` ステージング環境および実稼動環境でのファイル。 いつ `MYSQL_USE_SLAVE_CONNECTION` はに設定されています。 `false`, `synchronous_replication` パラメーターが設定されていません。

## `QUEUE_CONFIGURATION`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

この環境変数を使用して、カスタマイズされた AMQP サービス設定をデプロイメント間で保持します。 例えば、クラウドインフラストラクチャを利用して作成するのではなく、既存のメッセージキューサービスを使用する場合は、を使用します。 `QUEUE_CONFIGURATION` サイトに接続するための環境変数：

```yaml
stage:
  deploy:
    QUEUE_CONFIGURATION:
      amqp:
        host: test.host
        port: 1234
      amqp2:
        host: test.host2
        port: 12345
      mq:
        host: mq.host
        port: 1234
```

{{merge-options}}

次の例では、新しい値を既存の設定に結合します。

```yaml
stage:
  deploy:
    QUEUE_CONFIGURATION:
      _merge: true
      amqp:
        host: changed1.host
        port: 5672
      amqp2:
        host: changed2.host2
        port: 12345
      mq:
        host: changedmq.host
        port: 1234
```

## `REDIS_BACKEND`

- **デフォルト**—`Cm_Cache_Backend_Redis`
- **バージョン**—Adobe Commerce 2.3.0 以降

Redis キャッシュのバックエンド モデル構成を指定します。

Adobe Commerce バージョン 2.3.0 以降には、次のバックエンドモデルが含まれています。

- `Cm_Cache_Backend_Redis`
- `\Magento\Framework\Cache\Backend\Redis`
- `\Magento\Framework\Cache\Backend\RemoteSynchronizedCache`

の設定方法の例 `REDIS_BACKEND`

```yaml
stage:
  deploy:
    REDIS_BACKEND: '\Magento\Framework\Cache\Backend\RemoteSynchronizedCache'
```

>[!NOTE]
>
>を指定する場合 `\Magento\Framework\Cache\Backend\RemoteSynchronizedCache` 有効にする Redis バックエンドモデルとして [L2 キャッシュ](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cache/level-two-cache.html), `ece-tools` キャッシュ設定を自動的に生成します。 例を参照 [設定ファイル](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cache/level-two-cache.html#configuration-example) が含まれる _Adobe Commerce設定ガイド_. 生成されたキャッシュ設定を上書きするには、を使用します [CACHE_CONFIGURATION](#cache_configuration) 変数をデプロイします。

## `REDIS_USE_SLAVE_CONNECTION`

- **デフォルト**—`false`
- **バージョン**—Adobe Commerce 2.1.16 以降

>[!WARNING]
>
>実行 _ではない_ で、この変数を有効にする [拡張アーキテクチャ](../architecture/scaled-architecture.md) プロジェクト。 Redis 接続エラーが発生します。 Redis スレーブはアクティブですが、Redis 読み取りには使用されません。 別の方法として、AdobeはAdobe Commerce 2.3.5 以降を使用し、新しい Redis バックエンド設定を実装し、Redis 用の L2 キャッシングを実装することをお勧めします。

>[!TIP]
>
>この `REDIS_USE_SLAVE_CONNECTION` 変数は、クラウドインフラストラクチャー上のAdobe Commerce ステージング環境および実稼動 Pro クラスター環境でのみサポートされ、スタータープロジェクトではサポートされません。

Adobe Commerceは、複数の Redis インスタンスを非同期で読み取ることができます。 をに設定 `true` を自動的に使用するには _読み取り専用_ 非マスターノードで読み取り専用トラフィックを受信する Redis インスタンスへの接続。 この接続では、読み取り/書き込みトラフィックを処理するノードが 1 つだけなので、ロード・バランシングによってパフォーマンスが向上します。 をに設定 `false` 既存の読み取り専用接続配列を `env.php` ファイル。

```yaml
stage:
  deploy:
    REDIS_USE_SLAVE_CONNECTION: true
```

で Redis サービスが設定されている必要があります。 `.magento.app.yaml` ファイルおよびを `services.yaml` ファイル。

[ECE-Tools バージョン 2002.0.18](../release-notes/cloud-release-archive.md#v2002018) 後で、よりフォールトトレラントな設定を使用します。 Adobe Commerceが Redis からデータを読み取れない場合 _奴隷_ その後、Redis からデータを読み取ります。 _master_ インスタンス。

読み取り専用接続は、統合環境では使用できません。また、 [`CACHE_CONFIGURATION` 変数](#cache_configuration).

## `RESOURCE_CONFIGURATION`

- **デフォルト** – 設定されていません
- **バージョン**—Adobe Commerce 2.1.4 以降

リソース名をデータベース接続にマップします。 この設定は、 `resource` の節 `env.php` ファイル。

{{merge-options}}

次の例では、新しい値を既存の設定に結合します。

```yaml
stage:
  deploy:
    RESOURCE_CONFIGURATION:
      _merge: true
      default_setup:
        connection: default
```

## `SCD_COMPRESSION_LEVEL`

- **デフォルト**—`4`
- **バージョン**—Adobe Commerce 2.1.4 以降

を指定します [gzip](https://www.gnu.org/software/gzip) 圧縮レベル （`0` 対象： `9`）を選択して、静的コンテンツの圧縮時に使用します。 `0` 圧縮を無効にします。

```yaml
stage:
  deploy:
    SCD_COMPRESSION_LEVEL: 5
```

## `SCD_COMPRESSION_TIMEOUT`

- **デフォルト**—`600`
- **バージョン**—Adobe Commerce 2.1.4 以降

静的アセットの圧縮に要する時間が圧縮タイムアウトの制限を超えると、デプロイメントプロセスが中断されます。 静的コンテンツ圧縮コマンドの最大実行時間を秒単位で設定します。

```yaml
stage:
  deploy:
    SCD_COMPRESSION_TIMEOUT: 800
```

## `SCD_MATRIX`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

テーマごとに複数のロケールを設定できます。 このカスタマイズにより、不要なテーマファイルの数が減るので、デプロイメントプロセスが迅速化されます。 例えば、 _magento/バックエンド_ 英語のテーマおよび他の言語のカスタムテーマ。

次の例では、 `Magento/backend` 3 つのロケールを持つテーマ：

```yaml
stage:
  deploy:
    SCD_MATRIX:
      "magento/backend":
        language:
          - en_US
          - fr_FR
          - af_ZA
```

また、以下を選択できます _ではない_ テーマをデプロイします。

```yaml
stage:
  deploy:
    SCD_MATRIX:
      "magento/backend": [ ]
```

## `SCD_MAX_EXECUTION_TIME`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.2.0 以降

静的コンテンツのデプロイメントの予想最大実行時間を増やすことができます。

デフォルトでは、Adobe Commerceは想定される最大実行時間を 900 秒に設定しますが、場合によっては、Cloud プロジェクトの静的コンテンツのデプロイメントを完了するためにより多くの時間が必要になることがあります。

```yaml
stage:
  deploy:
    SCD_MAX_EXECUTION_TIME: 3600
```

{{scd-timing-warning}}

## `SCD_NO_PARENT`

- **デフォルト**—`false`
- **バージョン**—Adobe Commerce 2.4.2 以降

デプロイフェーズで、を設定します `SCD_NO_PARENT: true` そのため、親テーマの静的コンテンツの生成は、デプロイフェーズでは行われません。 この設定により、デプロイメント時間が最小限に抑えられ、デプロイメント中に静的コンテンツのビルドが失敗した場合に発生する可能性のあるサイトのダウンタイムが回避されます。 参照： [静的コンテンツデプロイメント](../deploy/static-content.md).

```yaml
stage:
  deploy:
    SCD_NO_PARENT: true
```

## `SCD_STRATEGY`

- **デフォルト**—`quick`
- **バージョン**—Adobe Commerce 2.2.0 以降

をカスタマイズできます [デプロイメント戦略](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cli/static-view/static-view-file-strategy.html) 静的コンテンツの場合。 参照： [静的表示ファイルのデプロイ](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cli/static-view/static-view-file-deployment.html).

次のオプションを使用 _のみ_ 複数のロケールがある場合：

- `standard` – すべてのパッケージのすべての静的ビューファイルをデプロイします。
- `quick` – （_default_）を使用すると、デプロイメント時間を最小限に抑えることができます。
- `compact`- サーバー上のディスク領域を節約します。 Adobe Commerce バージョン 2.2.4 以前では、この設定はの値よりも優先されます `scd_threads` 値： `1`.

```yaml
stage:
  deploy:
    SCD_STRATEGY: "compact"
```

## `SCD_THREADS`

- **デフォルト** – 自動
- **バージョン**—Adobe Commerce 2.1.4 以降

静的コンテンツのデプロイメントのスレッド数を設定します。 デフォルト値は検出された CPU スレッド数に基づいて設定され、4 を超えることはありません。 スレッド数を増やすと、静的コンテンツのデプロイメントが高速化されます。スレッド数を減らすと、速度が低下します。 スレッドの値は、次のように設定できます。

```yaml
stage:
  deploy:
    SCD_THREADS: 2
```

デプロイメント時間をさらに短縮するには、を使用します [設定の管理](../store/store-settings.md) （を使用） `scd-dump` 静的デプロイメントをビルドフェーズに移動するコマンド。

## `SEARCH_CONFIGURATION`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

この環境変数を使用して、カスタマイズされた検索サービス設定をデプロイメント間で保持します。 例：

Elasticsearch設定：

```yaml
stage:
  deploy:
    SEARCH_CONFIGURATION:
      engine: elasticsearch
      elasticsearch_server_hostname: http://elasticsearch.internal
      elasticsearch_server_port: '9200'
      elasticsearch_index_prefix: magento2
      elasticsearch_server_timeout: '15'
```

OpenSearch 設定（Commerce 2.4.6 以降）:

```yaml
stage:
  deploy:
    SEARCH_CONFIGURATION:
      engine: opensearch
      opensearch_server_hostname: 'http://opensearch.internal'
      opensearch_server_port: '9200'
      opensearch_index_prefix: 'magento2'
      opensearch_server_timeout: '15'
```

{{merge-options}}

次の例では、新しい値を既存の設定に結合します。

```yaml
stage:
  deploy:
    SEARCH_CONFIGURATION:
      engine: elasticsearch
      elasticsearch_server_port: '9200'
      _merge: true
```

## `SESSION_CONFIGURATION`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

Redis セッションストレージの設定 には次が必要です `save`, `redis`, `host`, `port`、および `database` セッションストレージ変数のオプション。 例：

```yaml
stage:
  deploy:
    SESSION_CONFIGURATION:
      redis:
        bot_first_lifetime: 100
        bot_lifetime: 10001
        database: 0
        disable_locking: 1
        host: redis.internal
        max_concurrency: 10
        max_lifetime: 10001
        min_lifetime: 100
        port: 6379
      save: redis
```

{{merge-options}}

次の例では、新しい値を既存の設定に結合します。

```yaml
stage:
  deploy:
    SESSION_CONFIGURATION:
      _merge: true
      redis:
        max_concurrency: 10
```

## `SKIP_SCD`

- **デフォルト**— _未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

をに設定 `true` を使用して、デプロイフェーズでの静的コンテンツのデプロイメントをスキップできます。

デプロイフェーズで、を設定します `SKIP_SCD: true` そのため、静的コンテンツのビルドは、デプロイフェーズでは行われません。 この設定により、デプロイメント時間が最小限に抑えられ、デプロイメント中に静的コンテンツのビルドが失敗した場合に発生する可能性のあるサイトのダウンタイムが回避されます。 参照： [静的コンテンツデプロイメント](../deploy/static-content.md).

```yaml
stage:
  deploy:
    SKIP_SCD: true
```

## `UPDATE_URLS`

- **デフォルト**—`true`
- **バージョン**—Adobe Commerce 2.1.4 以降

デプロイメントで、データベースのAdobe Commerceのベース URL を、で指定されたプロジェクト URL に置き換えます [`MAGENTO_CLOUD_ROUTES`](variables-cloud.md) 変数。 この設定はローカル開発で役に立ちます。ローカル環境用にベース URL が設定されている場合です。 クラウド環境にデプロイすると、URL が更新され、プロジェクトの URL を使用してストアフロントと管理者にアクセスできるようになります。

Pro または Starter のステージング環境および実稼動環境にデプロイする際に URL を更新する必要がある場合は、 [`FORCE_UPDATE_URLS`](#force_update_urls) 変数。

```yaml
stage:
  deploy:
    UPDATE_URLS: false
```

## `VERBOSE_COMMANDS`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

を有効または無効にする [交感](https://symfony.com/doc/current/console/verbosity.html) デバッグの詳細レベル： `bin/magento` デプロイメント段階で実行される CLI コマンド。

>[!NOTE]
>
>VERBOSE_COMMANDS 設定を使用して、成功と失敗の両方のコマンド出力の詳細を制御するには `bin/magento` CLI コマンド、以下を設定する必要があります [MIN_LOGGING_LEVEL](variables-global.md#minlogginglevel) `debug`.

ログに表示される詳細レベルを選択します。

- `-v`=標準出力
- `-vv`=より詳細な出力
- `-vvv` =デバッグに最適な詳細出力

```yaml
stage:
  deploy:
    VERBOSE_COMMANDS: "-vv"
```
