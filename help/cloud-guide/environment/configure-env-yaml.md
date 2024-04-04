---
title: 環境の設定
description: 環境変数を使用して、クラウドインフラストラクチャ環境（Pro Staging や Production など）のすべてのコマースにわたって、ビルドアクションとデプロイアクションを設定する方法について説明します。
feature: Cloud, Build, Configuration, Deploy, SCD
role: Developer
exl-id: 66e257e2-1eca-4af5-9b56-01348341400b
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '697'
ht-degree: 0%

---

# デプロイメント用の環境変数の設定

The `.magento.env.yaml` ファイルでは、環境変数を使用して、Pro Staging や Production を含むすべての環境にわたるビルドとデプロイアクションの管理を一元化します。 各環境で一意のアクションを設定するには、各環境でこのファイルを変更する必要があります。

>[!TIP]
>
>YAML ファイルでは大文字と小文字が区別され、タブは使用できません。 一貫したインデントを `.magento.env.yaml` ファイルまたは設定が期待どおりに動作しない可能性があります。 ドキュメントおよびサンプルファイルの例では、を使用します。 _2 スペース_ インデントです。 以下を使用します。 [ece-tools validate コマンド](#validate-configuration-file) 設定を確認します。

## ファイル構造

The `.magento.env.yaml` ファイルには 2 つのセクションが含まれます。 `stage` および `log`. The `stage` セクションは、 [クラウドのデプロイメントプロセス](../deploy/process.md).

- `stage` — ステージセクションを使用して、次のデプロイメントステージの特定のアクションを定義します。
   - `global` — ビルド、デプロイ、およびデプロイ後のフェーズの両方でアクションを制御します。 これらの設定は、ビルド、デプロイ、およびデプロイ後の各セクションで上書きできます。
   - `build` — ビルドフェーズでのアクションのみを制御します。 このセクションで設定を指定しない場合、ビルドフェーズではグローバルセクションの設定が使用されます。
   - `deploy` — デプロイフェーズでのアクションのみを制御します。 このセクションで設定を指定しない場合、デプロイフェーズではグローバルセクションの設定が使用されます。
   - `post-deploy` — アクションを制御します。 _次より後_ アプリケーションのデプロイと _次より後_ コンテナが接続の受け入れを開始します。
- `log` — ログセクションを使用して、 [通知](set-up-notifications.md)（通知のタイプや詳細レベルを含む）
   - `slack` — メッセージボットに送信するSlackを設定します。
   - `email`- 1 人または複数の電子メール受信者に送信する電子メールを設定します。
   - [ログハンドラー](log-handlers.md) — リモート・ログ・サーバに送信するハードウェアおよびソフトウェア・アプリケーション・メッセージを構成します。

### 環境変数

The `ece-tools` パッケージは、 `env.php` 値に基づくファイル [クラウド変数](variables-cloud.md)、変数を [!DNL Cloud Console]、および `.magento.env.yaml` 設定ファイル。 の環境変数 `.magento.env.yaml` ファイル既存のコマース設定を上書きして、クラウド環境をカスタマイズします。 デフォルト値が `Not Set`を、 `ece-tools` パッケージテイク **いいえ** アクションと [!DNL Commerce] デフォルト値、またはMAGENTO_CLOUD_RELATIONSHIPS 設定の値。 デフォルト値が設定されている場合、 `ece-tools` パッケージは、そのデフォルトを設定するために動作します。

次のトピックでは、デフォルト値を設定するかどうかなど、 `.magento.env.yaml` ファイル：

- [グローバル](variables-global.md) — 変数は、各フェーズでのアクション（ビルド、デプロイ、デプロイ後）を制御します。
- [ビルド](variables-build.md) — 変数はビルドアクションを制御します
- [デプロイ](variables-deploy.md) — 変数はデプロイアクションを制御します
- [デプロイ後](variables-post-deploy.md) — 変数がデプロイ後のアクションを制御します

### CLI からの設定ファイルの作成

次の項目を生成できます。 `.magento.env.yaml` 以下を使用したクラウド環境の設定ファイル `ece-tools` コマンド。

>設定ファイルを作成します

```bash
php ./vendor/bin/ece-tools cloud:config:create `<configuration-json>`
```

>設定ファイルの値を更新

```bash
php ./vendor/bin/ece-tools cloud:config:update `<configuration-json>`
```

両方のコマンドには 1 つの引数が必要です。1 つ以上のビルド、デプロイ、またはデプロイ後の変数の値を指定する JSON 形式の配列です。 たとえば、次のコマンドは、 `SCD_THREADS` および `CLEAN_STATIC_FILES` 変数：

```bash
php vendor/bin/ece-tools cloud:config:create '{"stage":{"build":{"SCD_THREADS":5}, "deploy":{"CLEAN_STATIC_FILES":false}}}'
```

およびは、 `.magento.env.yaml` ファイルに以下の設定が含まれます。

```yaml
stage:
  build:
    SCD_THREADS: 5
  deploy:
    CLEAN_STATIC_FILES: false
```

以下を使用すると、 `cloud:config:update` コマンドを使用して新しいファイルを更新します。 たとえば、次のコマンドは `SCD_THREADS` の値を指定し、 `SCD_COMPRESSION_TIMEOUT` 設定：

```bash
php vendor/bin/ece-tools cloud:config:update '{"stage":{"build":{"SCD_THREADS":3, "SCD_COMPRESSION_TIMEOUT":1000}}}'
```

更新されたファイルには、次の設定が含まれています。

```yaml
stage:
  build:
    SCD_THREADS: 3
    SCD_COMPRESSION_TIMEOUT: 1000
  deploy:
    CLEAN_STATIC_FILES: false
```

### 設定ファイルを検証

次を使用します。 `ece-tools` コマンドを使用して検証します。 `.magento.env.yaml` 変更をリモートクラウド環境にプッシュする前の設定ファイル。

```bash
php ./vendor/bin/ece-tools cloud:config:validate
```

以下のレスポンスのサンプルは、修正する項目のリストを示しています。

```terminal
Environment configuration is not valid. Correct the following items in your .magento.env.yaml file:
The SCD_THREADS variable contains an invalid value of type string. Use the following type: integer.
The SCD_STRATEGY variable contains an invalid value fast. Use one of the available value options: compact, quick, standard.
The NOT_EXIST_OPTION variable is not allowed in configuration.
```

## PHP 定数

PHP 定数は、 `.magento.env.yaml` ファイル定義を参照してください。 次の例では、 `driver_options` PHP 定数の使用：

```yaml
stage:
  deploy:
    DATABASE_CONFIGURATION:
      connection:
        default:
          driver_options:
            !php/const:\PDO::MYSQL_ATTR_LOCAL_INFILE : 1
        indexer:
          driver_options:
            !php/const:\PDO::MYSQL_ATTR_LOCAL_INFILE : 1
      _merge: true
```

>[!WARNING]
>
>定数解析は、 `symfony/yaml` 3.2 より前のパッケージバージョン。

## エラー処理

の予期しない値が原因でエラーが発生した場合 `.magento.env.yaml` 設定ファイルに保存されている場合は、エラーメッセージが表示されます。 例えば、次のエラーメッセージは、各項目に対して推奨される変更のリストと予期しない値を示し、有効なオプションが提供される場合があります。

```terminal
- Environment configuration is not valid. Please correct .magento.env.yaml file with next suggestions:
  Item CRON_CONSUMERS_RUNNER is not supposed to be in stage build. Please move it to one of possible stages: global, deploy
  Item SKIP_SCD has unexpected type string. Please use one of next types: boolean
  Item VERBOSE_COMMANDS has unexpected type boolean. Please use one of next types: string
  Item SKIP_HTML_MINIFICATION has unexpected type string. Please use one of next types: boolean
  Item CRON_CONSUMERS_RUNNER has unexpected type boolean. Please use one of next types: array
  Item VAR_WARM_UP_PAGES is not allowed in configuration.
  Item WARM_UP_PAGES has unexpected type string. Please use one of next types: array
```

修正、コミット、および変更のプッシュを行います。 エラーメッセージが表示されない場合は、設定ファイルに対する変更が検証に合格します。

## 設定管理の最適化

構成のダンプ後に構成管理を有効にした場合、SCD_*変数をデプロイからビルドステージに移動する必要があります。 詳しくは、 [静的コンテンツデプロイメント戦略](../deploy/static-content.md).

>設定管理の前：

```yaml
  deploy:
    CRON_CONSUMERS_RUNNER:
      cron_run: true
      consumers: []
    SCD_STRATEGY: compact
    SCD_MATRIX:
      ...
    REDIS_USE_SLAVE_CONNECTION: 1
```

>構成管理を有効にした後、SCD_*変数をビルド・ステージに移動します。

```yaml
  deploy:
    CRON_CONSUMERS_RUNNER:
      cron_run: true
      consumers: []
    REDIS_USE_SLAVE_CONNECTION: 1
  build:
    SCD_STRATEGY: compact
    SCD_MATRIX:
      ...
```
