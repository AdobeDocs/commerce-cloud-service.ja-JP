---
title: ストアの設定管理
description: クラウドインフラストラクチャ環境上のすべてのAdobe Commerceでストア設定を管理および同期する方法について説明します。
feature: Cloud, Configuration, SCD
exl-id: f2dd876d-24ee-4d47-b9ac-44fcf77b61b5
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '1439'
ht-degree: 0%

---

# ストアの設定管理

ストアのデフォルトの設定は、 `config.xml` を設定します。 コマース管理または CLI で設定を変更する場合 `bin/magento config:set` コマンドを使用すると、変更がコアデータベース、特に `core_config_data` 表。 これらの設定により、 `config.xml` ファイル。

ストア設定（管理者の設定を参照） **ストア** > **設定** > **設定** セクションは、設定のタイプに基づいて、デプロイメント設定ファイルに保存されます。

- `app/etc/config.php` — 静的コンテンツのデプロイメントに関連するストア、Web サイト、モジュールまたは拡張機能、静的ファイルの最適化、システム値の設定。 詳しくは、 [config.php リファレンス](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/files/config-reference-configphp.html) （内） _設定ガイド_.
- `app/etc/env.php` — システム固有の上書きと機密設定の値。 _NOT_ ソース管理下に保存されます。 詳しくは、 [env.php リファレンス](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/files/config-reference-envphp.html) （内） _設定ガイド_.

>[!NOTE]
>
>クラウドインフラストラクチャ上のAdobe Commerceは実稼動モードとメンテナンスモードのみをサポートしているので、 **詳細** > **開発者** セクションには、管理でアクセスできません。 必要な機能は次のとおりです。 [環境の管理者権限](../project/user-access.md) をクリックして、設定管理タスクを完了します。 追加の設定は、 [環境変数](../environment/configure-env-yaml.md).

設定管理を使用すると、パイプラインのデプロイメントを使用して、ダウンタイムを最小限に抑えながら、環境全体に一貫したストア設定をデプロイできます。 Adobe Commerce on cloud infrastructure プロジェクトには、ビルドサーバー、ビルドとデプロイスクリプト、および [パイプラインデプロイメント戦略](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/deployment/technical-details.html) 心の中で

## 設定の上書きスキーム

すべてのシステム構成は、次のオーバーライドスキームに従って、ビルドおよびデプロイフェーズの間に設定されます。

1. 環境変数が存在する場合は、カスタム設定を使用し、デフォルト設定を無視します。
1. 環境変数が存在しない場合は、 `MAGENTO_CLOUD_RELATIONSHIPS` 名前と値のペア ( [`.magento.app.yaml` ファイル](../application/configure-app-yaml.md). デフォルトの設定を無視します。
1. 環境変数が存在せず、 `MAGENTO_CLOUD_RELATIONSHIPS` には名前と値のペアが含まれず、カスタマイズされた設定をすべて削除して、デフォルト設定の値を使用します。

要約すると、環境変数は他のすべての値よりも優先されます。

>[!TIP]
>
>詳しくは、 [設定の管理](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/deployment/technical-details.html) （内） _設定ガイド_ を参照してください。

同じ設定が複数の場所で設定されている場合、アプリケーションは次の設定階層に基づいて環境に適用する値を決定します。

| 優先度 | 設定<br>メソッド | 説明 |
| -------- | ------------------------ | ----------- |
| 1 | [!DNL Cloud Console]<br>環境変数 | から追加された値 _変数_ 」タブに追加します。 [!DNL Cloud Console]. 機密設定または環境固有の設定に対して、ここで値を指定します。 ここで指定した設定は、管理者からは編集できません。 詳しくは、 [環境設定変数](../project/overview.md#configure-environment). |
| 2 | `.magento.app.yaml` | 値が `variables` のセクション `.magento.app.yaml` ファイル。 すべての環境で一貫した設定をおこなうには、ここで値を指定します。 **機密値を `.magento.app.yaml` ファイル。** 詳しくは、 [アプリケーション設定](../application/configure-app-yaml.md). |
| 3 | `app/etc/env.php` | ここに保存される環境固有の設定値は、 `app:config:dump` コマンドを使用します。 環境変数または CLI を使用して、システム固有の機密値を設定します。 詳しくは、 [機密データ](#sensitive-data). The `env.php` ファイルは次のとおりです **not** ソース管理に含まれます。 |
| 4 | `app/etc/config.php` | ここに格納された値は、 `app:config:dump` コマンドを使用します。 共有設定値がに追加されます。 `config.php`. 管理者または CLI を使用して共有設定を行います。 The `config.php` ファイルがソース管理に含まれています。 |
| 5 | データベース | ここに保存される値は、管理で設定することで追加されます。 上記の方法のいずれかを使用して設定された設定は、ロック（灰色表示）され、管理者からは編集できません。 |
| 6 | `config.xml` | 多くの設定では、 `config.xml` ファイルを参照してください。 Adobe Commerceが上記のメソッドのいずれかで設定された値を見つけられない場合、設定されている場合はデフォルト値にフォールバックされます。 |

{style="table-layout:auto"}

## 設定ダンプ

以下を使用できます。 `ece-tools` コマンドを使用して `config.php` 現在のすべてのストア設定を含むファイル：

```bash
./vendor/bin/ece-tools config:dump
```

データが「ダンプ済み」 `app/etc/config.php` ファイルが _ロック済み_&#x200B;と呼ばれ、コマース管理者の対応するフィールドが **読み取り専用**. The `config.php` ファイルには、設定した設定のみが含まれます。 デフォルト値はロックされません。 更新した値のみをロックすることで、ステージング環境と実稼動環境で使用されるすべての拡張機能が、読み取り専用の設定（特に Fastly）によって壊れなくなります。

>[!WARNING]
>
>The `ece-tools config:dump` コマンドは、B2B などのモジュールの詳細な設定を取得しません。 包括的な設定ダンプが必要な場合は、 `app:config:dump` コマンドを使用しますが、このコマンドは読み取り専用状態の設定値をロックします。

### 機密データ

機密のある設定は、 `app/etc/env.php` ファイルを作成します。 `bin/magento app:config:dump` コマンドを使用します。 CLI コマンドを使用して、機密値を設定できます。 `bin/magento config:sensitive:set`. 詳しくは、  [機密性の高い環境固有の設定](https://developer.adobe.com/commerce/php/development/configuration/sensitive-environment-settings/) （内） _Commerce PHP 拡張_ 設定を機密性が高い、またはシステム固有として指定する方法を説明するガイドです。

次のリストを参照： [機密設定またはシステム固有の設定](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/paths/config-reference-sens.html) （内） _設定ガイド_.

### SCD のパフォーマンス

ストアのサイズによっては、デプロイする静的コンテンツファイルが多数存在する場合があります。 通常、静的コンテンツは、アプリケーションがメンテナンスモードの場合、デプロイフェーズでデプロイされます。 最も最適な設定は、ビルドフェーズで静的コンテンツを生成することです。 詳しくは、 [デプロイ戦略の選択](../deploy/static-content.md).

設定のダンプ後に構成管理を有効にした場合、SCD_*変数をデプロイステージからビルドステージに移動し、ビルドフェーズ中に静的コンテンツの生成を適切に有効にする必要があります。 詳しくは、 [環境変数](../environment/configure-env-yaml.md#environment-variables).

**設定管理の前**:

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

**設定管理を有効にした後**:

SCD_*変数をビルド・ステージに移動します。

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

>[!NOTE]
>
>静的ファイルをデプロイする前に、ビルドおよびデプロイフェーズでは、GZIP を使用して静的コンテンツを圧縮します。 静的ファイルを圧縮すると、サーバーの負荷が軽減され、サイトのパフォーマンスが向上します。 詳しくは、 [ビルドオプション](../environment/variables-build.md) ファイル圧縮のカスタマイズと無効化について説明します。

## 設定を管理する手順

次に、このプロセスの概要を示します。

![スターター構成管理の概要](../../assets/starter/configuration-management-flow.png)

**ストアを設定し、設定ファイルを生成するには**:

1. 次のいずれかの環境で、管理者のストアのすべての設定を実行します。

   - スターター：アクティブな開発ブランチ
   - Pro：統合環境のアクティブなブランチ

   この環境からステージングおよび実稼動環境にデータベースをダンプする予定がない限り、これらの設定には実際の製品は含まれません。 通常、開発データベースにはフルストアデータは含まれません。

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. リモート・データベースのローカル・ダンプを作成します。

   ```bash
   magento-cloud db:dump
   ```

1. コードの変更を追加、コミット、およびプッシュして、リモート環境を更新します。

   ```bash
   git add app/etc/config.php
   ```

   ```bash
   git commit -m "Add system-specific configuration"
   ```

   ```bash
   git push origin <branch-name>
   ```

デプロイメントが完了したら、更新した環境の管理者にログインし、設定を確認します。 必要に応じて、追加の設定をステージング環境と実稼動環境に引き続き結合します。

### 設定を更新

管理ツールを使用して環境を変更し、コマンドを再実行すると、新しい設定が `config.php` ファイル。

>[!WARNING]
>
>手動で `config.php` ステージング環境と実稼動環境のファイルは、 **not** 推奨。 このファイルを使用すると、すべての環境ですべての設定の一貫性を維持できます。 絶対に `config.php` ファイルを再構築します。 ファイルを削除すると、ビルドプロセスとデプロイプロセスに必要な特定の設定と設定を削除できます。

### 設定ファイルの復元

オリジナルのコピー `app/etc/env.php` および `app/etc/config.php` ファイルは、デプロイメントプロセス中に作成され、同じフォルダーに保存されました。 次に、同じ内の BAK（バックアップファイル）と PHP（元のファイル）を示します `app/etc` フォルダー：

```terminal
...
config.php.bak
di.xml
env.php.bak
vendor_path.php
config.php
db_schema.xml
env.php
...
```

以前の設定では、 `app/etc/config.local.php` ファイル。 詳しくは、 [古い設定を移行する](#migrate-older-configurations).

**設定ファイルを復元するには**:

1. ローカルワークステーションで、SSH を使用してリモートプロジェクトと環境にログインします。

   ```bash
   magento-cloud ssh
   ```

1. バックアップファイルの場所と可用性を確認します。

   ```bash
   ./vendor/bin/ece-tools backup:list
   ```

   レスポンスのサンプル：

   ```terminal
   The list of backup files:
   app/etc/env.php
   app/etc/config.php
   ```

1. バックアップファイルを復元します。

   ```bash
   ./vendor/bin/ece-tools backup:restore
   ```

### 古い設定を移行する

クラウドインフラストラクチャ上のAdobe Commerce 2.2 以降にアップグレードする場合は、 `config.local.php` 新しい `config.php` ファイル。 管理者の設定がファイルの内容と一致する場合は、指示に従って `config.php` ファイル。

異なる場合は、 `config.local.php` 新しい `config.php` ファイル：

1. 手順に従って、 `config.php` ファイル。

1. を開きます。 `config.php` ファイルを編集し、最後の行を削除します。

1. を開きます。 `config.local.php` ファイルを開き、内容をコピーします。

1. 内容を `config.php` ファイルを保存し、Git への追加を完了します。

1. 環境全体にをデプロイする。

この移行は 1 回だけ実行します。 移行後、 `config.php` ファイル。

### ロケールの変更

複雑な設定の読み込みおよび書き出しプロセスに従わずに、ストアのロケールを変更できます。 _if_ お持ちの [SCD_ON_DEMAND](../environment/variables-global.md#scd_on_demand) 有効。 ロケールは、Admin を使用して更新できます。

を有効にして、ステージング環境または実稼動環境に別のロケールを追加できます。 `SCD_ON_DEMAND` 統合ブランチで、更新された `config.php` ファイルに新しいロケール情報を入力し、設定ファイルをターゲット環境にコピーします。

>[!WARNING]
>
>このプロセス **上書き** ストアの設定。環境に同じストアが含まれる場合にのみ、次の操作をおこないます。

1. 統合環境で、 `SCD_ON_DEMAND` 変数を [`.magento.env.yaml` ファイル](../environment/configure-env-yaml.md).

1. 管理者を使用して、必要なロケールを追加します。

1. SSH を使用してリモート環境にログインし、 `/app/etc/config.php` すべてのロケールを含むファイル。

   ```bash
   ssh <SSH-URL> "./vendor/bin/ece-tools config:dump"
   ```

1. 新しい設定ファイルをリモート統合環境からローカル環境ディレクトリにコピーします。

   ```bash
   rsync <SSH-URL>:app/etc/config.php ./app/etc/config.php
   ```

1. コードの変更を追加、コミット、およびプッシュして、リモート環境を更新します。
