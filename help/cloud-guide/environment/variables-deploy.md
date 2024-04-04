---
title: 変数をデプロイ
description: クラウドインフラストラクチャデプロイフェーズでのAdobe Commerceのアクションを制御する環境変数の一覧を参照してください。
feature: Cloud, Configuration, Cache, Deploy, SCD, Storage, Search
recommendations: noDisplay, catalog
role: Developer
exl-id: 673880b5-830b-4837-ac0c-5fa5643ae34c
source-git-commit: 8a0523f1714b6ea41887e99b5c31294cf5e5255e
workflow-type: tm+mt
source-wordcount: '2185'
ht-degree: 0%

---

# 変数をデプロイ

次の _デプロイ_ 変数は、デプロイフェーズのアクションを制御し、 [グローバル変数](variables-global.md). 次の変数を `deploy` 段階 `.magento.env.yaml` ファイル：

```yaml
stage:
  deploy:
    DEPLOY_VARIABLE_NAME: value
```

ビルドおよびデプロイプロセスのカスタマイズについて詳しくは、次の手順を参照してください。

- [デプロイメント設定](configure-env-yaml.md)
- [デプロイメントプロセス](../deploy/process.md)

## `CACHE_CONFIGURATION`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

Redis ページとデフォルトのキャッシュを設定します。 設定時に、 `cm_cache_backend_redis` パラメーターを指定するには、 `server`, `port`、および `database` オプション。

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

次の例では、 [Redis のプリロード機能](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cache/redis/redis-pg-cache.html#redis-preload-feature) 例： _設定ガイド_:

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

カスタムを使用するには [REDIS_BACKEND](#redis_backend) モデル (許可リストのみでなく )、 `_custom_redis_backend` 選択肢 `true` をクリックして、次の例のように正しい検証を有効にします。

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

クリーニングを有効または無効にします [静的コンテンツファイル](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cli/static-view/static-view-file-deployment.html) ビルドまたはデプロイフェーズ中に生成されます。 デフォルト値を使用 _true_ 開発のベストプラクティスとして。

- **`true`** — 更新された静的コンテンツを展開する前に、既存のすべての静的コンテンツを削除します。
- **`false`** — 生成されたコンテンツに新しいバージョンが含まれている場合にのみ、既存の静的コンテンツファイルが上書きされます。

別のプロセスで静的コンテンツを変更する場合は、値をに設定します。 _false_.

```yaml
stage:
  deploy:
    CLEAN_STATIC_FILES: false
```

展開前に静的表示ファイルを消去しないと、以前のバージョンを削除せずに既存のファイルに更新を展開した場合に問題が発生する可能性があります。 理由： [静的ファイルフォールバック](https://developer.adobe.com/commerce/frontend-core/guide/caching/#clean-static-files-cache) ルール、フォールバック操作で、ディレクトリに同じファイルの複数のバージョンが含まれている場合、誤ったファイルが表示されることがあります。

## `CRON_CONSUMERS_RUNNER`

- **デフォルト**—`cron_run = false`, `max_messages = 1000`
- **バージョン**— Adobe Commerce 2.2.0 以降

この環境変数を使用して、デプロイメント後にメッセージキューが実行されていることを確認します。

- `cron_run` — ブール値で、 `consumers_runner` cron ジョブ ( デフォルト= `false`) をクリックします。
- `max_messages` — 各コンシューマーが処理する必要がある最大メッセージ数を指定する数値 ( デフォルト= `1000`) をクリックします。 値は `0` 消費者が終了するのを防ぐために。
- `consumers` — 実行するコンシューマーを指定する文字列の配列。 空の配列が実行されます _すべて_ 消費者

- `multiple_processes` — 各コンシューマーに対して生成するプロセスの数を指定する数値。 コマースでサポート **2.4.4** 以上

>[!NOTE]
>
>メッセージキューのリストを返すには `consumers`を実行し、 `./bin/magento queue:consumers:list` コマンドを使用して、リモート環境で実行できます。

特定の `consumers` そして `multiple_processes` 各コンシューマー用にスポーンするには：

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

デフォルトでは、デプロイメントプロセスは、 `env.php` ファイル。 詳しくは、 [メッセージキューの管理](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/message-queues/manage-message-queues.html) （内） _コマース設定ガイド_ オンプレミスのAdobe Commerceの場合。

## `CONSUMERS_WAIT_FOR_MAX_MESSAGES`

- **デフォルト**—`false`
- **バージョン**— Adobe Commerce 2.2.0 以降

方法の設定 `consumers` 次のいずれかのオプションを選択して、メッセージキューからメッセージを処理します。

- `false`—`Consumers` キュー内の使用可能なメッセージを処理し、TCP 接続を閉じて、終了します。 `Consumers` 処理されたメッセージの数が `max_messages` 指定された値 `CRON_CONSUMERS_RUNNER` 変数をデプロイします。

- `true`—`Consumers` 最大メッセージ数 (`max_messages`) が `CRON_CONSUMERS_RUNNER` TCP 接続を閉じてコンシューマープロセスを終了する前に、変数をデプロイします。 到達前にキューが空の場合 `max_messages`を指定しない場合、コンシューマーはより多くのメッセージが届くのを待ちます。

>[!WARNING]
>
>作業者を使用してを実行する場合 `consumers` cron ジョブを使用する代わりに、この変数を true に設定します。

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
>を設定します。 `CRYPT_KEY` 価値を [!DNL Cloud Console] の代わりに、 `.magento.env.yaml` ファイルを使用して、環境のソースコードリポジトリでキーが公開されないようにします。 詳しくは、 [環境変数とプロジェクト変数の設定](https://experienceleague.adobe.com/docs/commerce-cloud-service/user-guide/project/overview.html#configure-environment).

インストールプロセスを行わずにデータベースを別の環境に移動する場合は、対応する暗号化情報が必要です。 Adobe Commerceは、 [!DNL Cloud Console] として `crypt/key` 値を `env.php` ファイル。

## `DATABASE_CONFIGURATION`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

データベースを [relationships プロパティ](../application/properties.md#relationships) の `.magento.app.yaml` ファイルを使用すると、データベース接続をデプロイ用にカスタマイズできます。

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

また、テーブルのプレフィックスを設定できます。

>[!WARNING]
>
>結合オプションをテーブルのプレフィックスと共に使用しない場合は、デフォルトの接続設定を指定する必要があります。指定しないと、デプロイが検証に失敗します。

次の例では、 `ece_` デフォルトの接続設定を使用する代わりに、テーブルのプレフィックスを使用する `_merge` オプション：

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
- **バージョン**— Adobe Commerce 2.2.0 以降

カスタマイズされた状態を保持 [!DNL Elastic Suite] 配置間のサービス設定を行い、メインの「system/default/smile_elasticsuite_core_base_settings」セクションでその設定を使用します。 [!DNL Elastic Suite] 設定。 次の場合、 [!DNL Elastic Suite] composer パッケージがインストールされている場合は、自動的に設定されます。

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

- 検索エンジンを以外のタイプに変更する `elasticsuite` により、適切な検証エラーを伴うデプロイエラーが発生します
- Elasticsearchサービスを削除すると、適切な検証エラーが発生し、デプロイエラーが発生します

>[!NOTE]
>
>の使用またはトラブルシューティングの詳細については、 [!DNL Elastic Suite] Adobe Commerceのプラグインについては、 [[!DNL Elastic Suite] ドキュメント](https://github.com/Smile-SA/elasticsuite).

## `ENABLE_GOOGLE_ANALYTICS`

- **デフォルト**—`false`
- **バージョン**—Adobe Commerce 2.1.4 以降

ステージング環境および統合環境にGoogle Analyticsをデプロイする際のデプロイを有効または無効にします。 デフォルトでは、Google Analyticsは実稼動環境でのみ true になります。 この値をに設定します。 `true` を使用して、ステージングGoogle Analyticsと統合環境で環境を有効にします。

- **`true`** — ステージング環境と統合環境のGoogle Analyticsを有効にします。
- **`false`** — ステージング環境および統合環境のGoogle Analyticsを無効にします。

次を追加： `ENABLE_GOOGLE_ANALYTICS` 環境変数を `deploy` ステージの `.magento.env.yaml` ファイル：

```yaml
stage:
  deploy:
    ENABLE_GOOGLE_ANALYTICS: true
```

>[!NOTE]
>
>デプロイプロセスでは、実稼動環境でのGoogle Analyticsが常に有効になります。

## `FORCE_UPDATE_URLS`

- **デフォルト**—`true`
- **バージョン**—Adobe Commerce 2.1.4 以降

Pro または Starter Staging および実稼動環境にデプロイすると、この変数は、データベース内のAdobe Commerceベース URL を、 [`MAGENTO_CLOUD_ROUTES`](variables-cloud.md) 変数を使用します。 この設定を使用して、 [UPDATE_URLS](#update_urls) 変数をデプロイします。この変数は、ステージング環境または実稼動環境にデプロイする際に無視されます。

```yaml
stage:
  deploy:
    FORCE_UPDATE_URLS: true
```

## `LOCK_PROVIDER`

- **デフォルト**—`file`
- **バージョン**— Adobe Commerce 2.2.5 以降

ロックプロバイダーは、重複する cron ジョブや cron グループの起動を防ぎます。 以下を使用します。 `file` 実稼動環境でプロバイダーをロックします。 スターター環境と Pro 統合環境では、 [MAGENTO_CLOUD_LOCKS_DIR](variables-cloud.md) 変数です。 `ece-tools` が適用されます。 `db` プロバイダを自動的にロックします。

```yaml
stage:
  deploy:
    LOCK_PROVIDER: "db"
```

詳しくは、 [ロックの設定](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/tutorials/lock-provider.html) （内） _インストールガイド_.

## `MYSQL_USE_SLAVE_CONNECTION`

- **デフォルト**—`false`
- **バージョン**—Adobe Commerce 2.1.4 以降

>[!TIP]
>
>The `MYSQL_USE_SLAVE_CONNECTION` 変数は、クラウドインフラストラクチャのAdobe Commerce Staging および Production Pro クラスター環境でのみサポートされ、スタータープロジェクトではサポートされません。

Adobe Commerceは、複数のデータベースを非同期で読み取ることができます。 をに設定します。 `true` 自動的に _読み取り専用_ 非マスターノード上の読み取り専用トラフィックを受け取るためのデータベースへの接続。 この接続は、読み取り/書き込みトラフィックを処理するノードが 1 つだけなので、ロードバランシングを通じてパフォーマンスを向上させます。 をに設定します。 `false` 既存の読み取り専用接続配列を `env.php` ファイル。

```yaml
stage:
  deploy:
    MYSQL_USE_SLAVE_CONNECTION: true
```

次の場合に `MYSQL_USE_SLAVE_CONNECTION` 変数が `true`、 `synchronous_replication` パラメーターがに設定されている `true` デフォルトでは `env.php` ファイルを作成します。 次の場合に `MYSQL_USE_SLAVE_CONNECTION` が `false`、 `synchronous_replication` パラメーターが設定されていません。

## `QUEUE_CONFIGURATION`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

この環境変数を使用して、配置間でカスタマイズされた AMQP サービス設定を保持します。 例えば、クラウドインフラストラクチャを利用して作成する代わりに、既存のメッセージキューサービスを使用する場合は、 `QUEUE_CONFIGURATION` 環境変数を使用して、サイトに接続します。

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

Redis キャッシュのバックエンドモデル設定を指定します。

Adobe Commerceバージョン 2.3.0 以降には、次のバックエンドモデルが含まれています。

- `Cm_Cache_Backend_Redis`
- `\Magento\Framework\Cache\Backend\Redis`
- `\Magento\Framework\Cache\Backend\RemoteSynchronizedCache`

設定方法の例 `REDIS_BACKEND`

```yaml
stage:
  deploy:
    REDIS_BACKEND: '\Magento\Framework\Cache\Backend\RemoteSynchronizedCache'
```

>[!NOTE]
>
>次を指定した場合： `\Magento\Framework\Cache\Backend\RemoteSynchronizedCache` を有効にする Redis バックエンドモデルとして [L2 キャッシュ](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cache/level-two-cache.html), `ece-tools` はキャッシュ設定を自動的に生成します。 例を見る [設定ファイル](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cache/level-two-cache.html#configuration-example) （内） _Adobe Commerce設定ガイド_. 生成されたキャッシュ設定を上書きするには、 [CACHE_CONFIGURATION](#cache_configuration) 変数をデプロイします。

## `REDIS_USE_SLAVE_CONNECTION`

- **デフォルト**—`false`
- **バージョン**—Adobe Commerce 2.1.16 以降

>[!WARNING]
>
>実行 _not_ この変数を [規模の大きい建築](../architecture/scaled-architecture.md) プロジェクト。 Redis 接続エラーを引き起こします。 Redis のスレーブは、まだアクティブですが、Redis の読み取りには使用されません。 別の方法として、Adobeは、Adobe Commerce 2.3.5 以降の使用、新しい Redis バックエンド設定の実装、Redis 用の L2 キャッシュの実装をお勧めします。

>[!TIP]
>
>The `REDIS_USE_SLAVE_CONNECTION` 変数は、クラウドインフラストラクチャのAdobe Commerce Staging および Production Pro クラスター環境でのみサポートされ、スタータープロジェクトではサポートされません。

Adobe Commerceは、複数の Redis インスタンスを非同期で読み取ることができます。 をに設定します。 `true` 自動的に _読み取り専用_ Redis インスタンスへの接続で、非マスターノード上の読み取り専用トラフィックを受け取ります。 この接続は、読み取り/書き込みトラフィックを処理するノードが 1 つだけなので、ロードバランシングを通じてパフォーマンスを向上させます。 をに設定します。 `false` 既存の読み取り専用接続配列を `env.php` ファイル。

```yaml
stage:
  deploy:
    REDIS_USE_SLAVE_CONNECTION: true
```

Redis サービスを `.magento.app.yaml` ファイルと `services.yaml` ファイル。

[ECE-Tools バージョン 2002.0.18](../release-notes/cloud-release-archive.md#v2002018) そして、後で、より多くのフォールトトレラントな設定を使用します。 Adobe Commerceが Redis からデータを読み取れない場合 _セカンダリ_ インスタンスが Redis からデータを読み込む _プライマリ_ インスタンス。

読み取り専用の接続は、統合環境では使用できません。また、 [`CACHE_CONFIGURATION` 変数](#cache_configuration).

## `RESOURCE_CONFIGURATION`

- **デフォルト** — 未設定
- **バージョン**—Adobe Commerce 2.1.4 以降

リソース名をデータベース接続にマッピングします。 この設定は、 `resource` のセクション `env.php` ファイル。

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

どれを指定するかを指定します [gzip](https://www.gnu.org/software/gzip) 圧縮レベル (`0` から `9`) を使用して、静的コンテンツを圧縮する場合に使用します。 `0` 圧縮を無効にします。

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

テーマごとに複数のロケールを設定できます。 このカスタマイズにより、不要なテーマファイルの数を減らすことで、デプロイメントプロセスを迅速に実行できます。 例えば、 _magento/backend_ 英語のテーマと他の言語のカスタムテーマ。

次の例では、 `Magento/backend` 3 つのロケールを持つテーマ

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

また、 _not_ テーマのデプロイ：

```yaml
stage:
  deploy:
    SCD_MATRIX:
      "magento/backend": [ ]
```

## `SCD_MAX_EXECUTION_TIME`

- **デフォルト**—_未設定_
- **バージョン**— Adobe Commerce 2.2.0 以降

静的コンテンツデプロイメントの予想される最大実行時間を増やすことができます。

デフォルトでは、Adobe Commerceでは予想される最大実行時間が 900 秒に設定されていますが、場合によっては、クラウドプロジェクトの静的コンテンツのデプロイメントを完了するのに、より多くの時間が必要になることがあります。

```yaml
stage:
  deploy:
    SCD_MAX_EXECUTION_TIME: 3600
```

{{scd-timing-warning}}

## `SCD_NO_PARENT`

- **デフォルト**—`false`
- **バージョン**—Adobe Commerce 2.4.2 以降

デプロイフェーズで、 `SCD_NO_PARENT: true` そのため、デプロイフェーズで親テーマの静的コンテンツが生成されることはありません。 この設定により、デプロイメント時間を最小限に抑え、デプロイメント中に静的コンテンツのビルドに失敗した場合に発生する可能性のあるサイトのダウンタイムを防ぐことができます。 詳しくは、 [静的コンテンツのデプロイメント](../deploy/static-content.md).

```yaml
stage:
  deploy:
    SCD_NO_PARENT: true
```

## `SCD_STRATEGY`

- **デフォルト**—`quick`
- **バージョン**— Adobe Commerce 2.2.0 以降

次の項目をカスタマイズできます： [デプロイメント戦略](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cli/static-view/static-view-file-strategy.html) 静的コンテンツ用。 詳しくは、 [静的ビューファイルのデプロイ](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cli/static-view/static-view-file-deployment.html).

次のオプションを使用 _のみ_ 複数のロケールがある場合：

- `standard` — すべてのパッケージのすべての静的ビューファイルをデプロイします。
- `quick`—(_デフォルト_) により、デプロイメント時間を最小限に抑えることができます。
- `compact` — サーバ上のディスク領域を節約します。 Adobe Commerceバージョン 2.2.4 以前では、この設定がの値よりも優先されます。 `scd_threads` 値を持つ `1`.

```yaml
stage:
  deploy:
    SCD_STRATEGY: "compact"
```

## `SCD_THREADS`

- **デフォルト** — 自動
- **バージョン**—Adobe Commerce 2.1.4 以降

静的コンテンツデプロイメントのスレッド数を設定します。 デフォルト値は、検出された CPU スレッド数に基づいて設定され、4 を超えない値です。 スレッド数を増やすと、静的コンテンツのデプロイメントが高速化します。スレッド数を減らすと、デプロイメントの速度が低下します。 スレッド値は次のように設定できます。

```yaml
stage:
  deploy:
    SCD_THREADS: 2
```

デプロイメント時間をさらに短縮するには、 [設定管理](../store/store-settings.md) と `scd-dump` コマンドを使用して、静的デプロイメントをビルドフェーズに移動します。

## `SEARCH_CONFIGURATION`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

この環境変数を使用して、デプロイメント間でカスタマイズされた検索サービス設定を保持します。 例：

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

Redis セッションストレージを設定します。 が必要です。 `save`, `redis`, `host`, `port`、および `database` セッションストレージ変数のオプション。 例：

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

をに設定します。 `true` を使用して、デプロイフェーズ中に静的コンテンツのデプロイメントをスキップできます。

デプロイフェーズで、 `SKIP_SCD: true` そのため、デプロイフェーズで静的コンテンツのビルドが発生しません。 この設定により、デプロイメント時間を最小限に抑え、デプロイメント中に静的コンテンツのビルドに失敗した場合に発生する可能性のあるサイトのダウンタイムを防ぐことができます。 詳しくは、 [静的コンテンツのデプロイメント](../deploy/static-content.md).

```yaml
stage:
  deploy:
    SKIP_SCD: true
```

## `UPDATE_URLS`

- **デフォルト**—`true`
- **バージョン**—Adobe Commerce 2.1.4 以降

デプロイメント時に、データベースのAdobe Commerceベース URL を、 [`MAGENTO_CLOUD_ROUTES`](variables-cloud.md) 変数を使用します。 この設定は、ローカル開発でベース URL がローカル環境に対して設定される場合に役立ちます。 クラウド環境にデプロイすると、URL が更新され、プロジェクトの URL を使用してストアフロントおよび管理者にアクセスできるようになります。

Pro または Starter のステージング環境および実稼動環境にデプロイする際に URL を更新する必要がある場合は、 [`FORCE_UPDATE_URLS`](#force_update_urls) 変数を使用します。

```yaml
stage:
  deploy:
    UPDATE_URLS: false
```

## `VERBOSE_COMMANDS`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

を有効または無効にする [Symfony](https://symfony.com/doc/current/console/verbosity.html) の詳細レベルをデバッグ `bin/magento` 導入フェーズ中に実行される CLI コマンド。

>[!NOTE]
>
>VERBOSE_COMMANDS 設定を使用して、成功したコマンドと失敗したコマンドの出力の詳細を制御するには `bin/magento` CLI コマンドを使用する場合は、 [MIN_LOGGING_LEVEL](variables-global.md#minlogginglevel) `debug`.

ログに表示する詳細レベルを選択します。

- `-v`=通常の出力
- `-vv`=より詳細な出力
- `-vvv` =デバッグに最適な詳細出力

```yaml
stage:
  deploy:
    VERBOSE_COMMANDS: "-vv"
```
