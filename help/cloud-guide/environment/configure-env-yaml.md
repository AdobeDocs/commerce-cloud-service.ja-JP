---
title: 環境の設定
description: 環境変数を使用して、ステージング環境および実稼動環境を含む、クラウドインフラストラクチャ環境のすべてのCommerceでビルドおよびデプロイ操作を設定する方法を説明します。
feature: Cloud, Build, Configuration, Deploy, SCD
role: Developer
exl-id: 66e257e2-1eca-4af5-9b56-01348341400b
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '697'
ht-degree: 0%

---

# デプロイメント用の環境変数の設定

この `.magento.env.yaml` ファイルでは、環境変数を使用して、ステージング環境や実稼動環境を含むすべての環境で、ビルドおよびデプロイアクションの管理を一元化します。 各環境で一意のアクションを設定するには、各環境でこのファイルを変更する必要があります。

>[!TIP]
>
>YAML ファイルでは大文字と小文字が区別され、タブは使用できません。 全体で一貫したインデントを使用するように注意してください `.magento.env.yaml` ファイルまたは設定が期待どおりに動作しない可能性があります。 ドキュメントとサンプルファイルの例では、次のものを使用します _2 つのスペース_ インデント。 の使用 [ece-tools validate コマンド](#validate-configuration-file) をクリックして、設定を確認します。

## ファイル構造

この `.magento.env.yaml` ファイルには、次の 2 つのセクションがあります。 `stage` および `log`. この `stage` 「」セクションでは、 [クラウドのデプロイメントプロセス](../deploy/process.md).

- `stage`- ステージセクションを使用して、次のデプロイメントのステージに対する特定のアクションを定義します。
   - `global`- ビルド、デプロイ、およびデプロイ後の両方のフェーズでアクションを制御します。 これらの設定は、ビルド、デプロイ、デプロイ後のセクションで上書きできます。
   - `build` – 構築フェーズのアクションのみを制御します。 このセクションで設定を指定しない場合、ビルドフェーズではグローバルセクションの設定が使用されます。
   - `deploy`- デプロイフェーズのアクションのみを制御します。 このセクションで設定を指定しない場合、デプロイフェーズではグローバルセクションの設定が使用されます。
   - `post-deploy`- アクションを制御します。 _後_ アプリケーションのデプロイと _後_ コンテナが接続の受け入れを開始します。
- `log`— ログ セクションを使用して構成します。 [通知](set-up-notifications.md)通知のタイプや詳細レベルを含みます。
   - `slack`—Slackボットに送信するメッセージを設定します。
   - `email`- 1 人または複数のメール受信者に送信するメールを設定します。
   - [ログハンドラー](log-handlers.md) – リモート ログ サーバーに送信されるハードウェアおよびソフトウェア アプリケーション メッセージを構成します。

### 環境変数

この `ece-tools` パッケージはに値を設定します `env.php` の値に基づいたファイル [クラウド変数](variables-cloud.md)、で設定された変数 [!DNL Cloud Console]、および `.magento.env.yaml` 設定ファイル。 の環境変数 `.magento.env.yaml` ファイルは、既存のCommerce設定を上書きしてクラウド環境をカスタマイズします。 デフォルト値がの場合 `Not Set`を選択し、続いて `ece-tools` パッケージテイク **不可** アクションとの使用 [!DNL Commerce] デフォルト、またはMAGENTO設定の値。 デフォルト値が設定されている場合は、 `ece-tools` パッケージは、そのデフォルトを設定するように動作します。

次のトピックには、で使用できるすべての変数の詳細な定義（デフォルト値が設定されているかどうかなど）が含まれています `.magento.env.yaml` ファイル：

- [グローバル](variables-global.md) – 変数は、ビルド、デプロイ、およびデプロイ後の各フェーズでのアクションを制御します。
- [ビルド](variables-build.md) – 変数は構築アクションを制御する
- [デプロイ](variables-deploy.md) – 変数は展開アクションを制御する
- [デプロイ後](variables-post-deploy.md) – 変数はデプロイ後のアクションを制御する

### CLI からの構成ファイルの作成

を生成できます `.magento.env.yaml` 以下を使用したクラウド環境用の設定ファイル `ece-tools` コマンド。

>設定ファイルを作成します

```bash
php ./vendor/bin/ece-tools cloud:config:create `<configuration-json>`
```

>設定ファイルの値を更新

```bash
php ./vendor/bin/ece-tools cloud:config:update `<configuration-json>`
```

両方のコマンドには単一の引数が必要です。少なくとも 1 つの build、deploy または post-deploy 変数の値を指定する JSON 形式の配列です。 例えば、次のコマンドは、 `SCD_THREADS` および `CLEAN_STATIC_FILES` 変数：

```bash
php vendor/bin/ece-tools cloud:config:create '{"stage":{"build":{"SCD_THREADS":5}, "deploy":{"CLEAN_STATIC_FILES":false}}}'
```

また、が作成されます `.magento.env.yaml` 次の設定のファイル：

```yaml
stage:
  build:
    SCD_THREADS: 5
  deploy:
    CLEAN_STATIC_FILES: false
```

を使用できます `cloud:config:update` 新しいファイルを更新するコマンド。 たとえば、次のコマンドは `SCD_THREADS` に値を設定して、に `SCD_COMPRESSION_TIMEOUT` 設定：

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

以下を使用します `ece-tools` 検証するコマンド `.magento.env.yaml` 変更をリモートクラウド環境にプッシュする前の設定ファイル。

```bash
php ./vendor/bin/ece-tools cloud:config:validate
```

次の応答例では、修正する項目のリストを示します。

```terminal
Environment configuration is not valid. Correct the following items in your .magento.env.yaml file:
The SCD_THREADS variable contains an invalid value of type string. Use the following type: integer.
The SCD_STRATEGY variable contains an invalid value fast. Use one of the available value options: compact, quick, standard.
The NOT_EXIST_OPTION variable is not allowed in configuration.
```

## PHP 定数

PHP 定数は以下で使用できます。 `.magento.env.yaml` 値をハードコーディングする代わりに、ファイル定義を使用します。 次の例では、 `driver_options` php 定数を使用する場合：

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
>を使用している場合、定数解析が機能しない。 `symfony/yaml` 3.2 より前のパッケージバージョン。

## エラー処理

で予期しない値が原因でエラーが発生した場合 `.magento.env.yaml` 設定ファイルにエラーメッセージが表示されます。 例えば、次のエラーメッセージは、各項目に対して提案された変更のリストを予期しない値で表示し、場合によっては有効なオプションを提供します。

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

修正を加え、コミットし、変更をプッシュします。 エラーメッセージが表示されない場合は、設定ファイルに対する変更が検証をパスします。

## 構成管理の最適化

設定のダンプ後に Configuration Management を有効にした場合は、SCD_*変数をデプロイからビルドステージに移動する必要があります。 参照： [静的コンテンツのデプロイメント戦略](../deploy/static-content.md).

>構成管理前：

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

>設定管理を有効にした後、SCD_*変数をビルドステージに移動します。

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
