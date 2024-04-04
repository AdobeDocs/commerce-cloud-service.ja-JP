---
title: プロパティ
description: プロパティリストを [!DNL Commerce] クラウドインフラストラクチャにビルドおよびデプロイするためのアプリケーション。
feature: Cloud, Configuration, Build, Deploy, Roles/Permissions, Storage
exl-id: 58a86136-a9f9-4519-af27-2f8fa4018038
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '797'
ht-degree: 0%

---

# アプリケーション設定のプロパティ

The `.magento.app.yaml` ファイルでは、プロパティを使用して、 [!DNL Commerce] アプリケーション。

| 名前 | 説明 | デフォルト | 必須 |
| ------ | --------------------------------- | ------- | -------- |
| [`access`](#access) | ユーザーの役割のカスタマイズ | — | いいえ |
| [`crons`](crons-property.md) | 仕様を更新し、Cron ジョブをスケジュールします。 | — | いいえ |
| [`dependencies`](#dependencies) | 追加の依存関係を有効にする | `php:composer/composer: '2.2.4'` | いいえ |
| [`disk`](#disk) | 永続的なディスクサイズを定義する | `5120` | はい |
| [`firewall`](firewall-property.md) | （スターターのみ）アウトバウンドトラフィックの制御 | — | いいえ |
| [`hooks`](hooks-property.md) | ビルド、デプロイ、およびデプロイ後のフェーズ用のシェルコマンドのカスタマイズ | — | いいえ |
| [`mounts`](#mounts) | パスを設定 | パス：<ul><li>`"var": "shared:files/var"`</li><li>`"app/etc": "shared:files/etc"`</li><li>`"pub/media": "shared:files/media"`</li><li>`"pub/static": "shared:files/static"`</li></ul> | いいえ |
| [`name`](#name) | アプリケーション名を定義 | `mymagento` | はい |
| [`relationships`](#relationships) | サービスをマッピング | サービス：<ul><li>`database: "mysql:mysql"`</li><li>`redis: "redis:redis"`</li><li>`opensearch: "opensearch:opensearch"`</li></ul> | いいえ |
| [`runtime`](#runtime) | Runtime プロパティには、 [!DNL Commerce] アプリケーション。 | 拡張機能：<ul><li>`xsl`</li><li>`newrelic`</li><li>`sodium`</li></ul> | はい |
| [`type`](#type-and-build) | ベースコンテナ画像を設定 | `php:8.1` | はい |
| [`variables`](variables-property.md) | 特定のコマースバージョンに対する環境変数の適用 | — | いいえ |
| [`web`](web-property.md) | 外部リクエストの処理 | — | はい |
| [`workers`](workers-property.md) | 外部リクエストの処理 | — | はい（Web プロパティを使用しない場合） |

{style="table-layout:auto"}

## `name`

The `name` プロパティは、 [`routes.yaml`](../routes/routes-yaml.md) HTTP upstream を定義するファイル ( デフォルトでは `mymagento:http`) をクリックします。 例えば、 `name` 次に該当 `app`を使用する場合、 `app:http` 「upstream」フィールドに入力します。

>[!WARNING]
>
>デプロイ後にアプリケーションの名前を変更しないでください。 その結果、データが失われます。

## `type` および `build`

The `type`  および `build` プロパティは、プロジェクトを構築して実行するための基本コンテナイメージに関する情報を提供します。

サポート対象 `type` 言語は PHP です。 PHP のバージョンを次のように指定します。

```yaml
type: php:<version>
```

The `build` プロパティは、プロジェクトの構築時にデフォルトで何が起こるかを決定します。 The `flavor` 実行するビルドタスクの既定のセットを指定します。 次の例は、 `type` および `build` から `magento-cloud/.magento.app.yaml`:

```yaml
# The toolstack used to build the application.
type: php:8.1
build:
    flavor: none

dependencies:
    php:
        composer/composer: '2.2.4'
```

### Composer 2 のインストールと使用

The `build: flavor:` プロパティは Composer 2.x では使用されないので、ビルドフェーズで Composer を手動でインストールする必要があります。 Starter プロジェクトと Pro プロジェクトで Composer 2.x をインストールして使用するには、 `.magento.app.yaml` 設定：

1. 削除 `composer` として `build: flavor:` とを追加します。 `none`. この変更により、Composer のデフォルト 1.x バージョンを使用してビルドタスクを実行できなくなります。
1. 追加 `composer/composer: '^2.0'` as a `php` Composer 2.x のインストールの依存関係。
1. 次を追加： `composer` タスクの作成先 `build` フックを使用して、Composer 2.x を使用してビルドタスクを実行します。

次の設定フラグメントを独自で使用します。 `.magento.app.yaml` 設定：

```yaml
# 1. Change flavor to none.
build:
    flavor: none

# 2. Add Composer ^2.0 as a php dependency.
dependencies:
    php:
        composer/composer: '^2.0'

# 3. Add a build hook to run the build tasks using Composer 2.x.
hooks:
    build: |
        set -e
        composer --no-ansi --no-interaction install --no-progress --prefer-dist --optimize-autoloader
```

詳しくは、 [必要なパッケージ](../development/overview.md#required-packages) を参照してください。

## `dependencies`

ビルドプロセス中にアプリケーションが必要とする可能性のある依存関係を指定します。

Adobe Commerceは、次の言語への依存をサポートしています。

- PHP
- Ruby
- Node.js

これらの依存関係は、アプリケーションの最終的な依存関係とは独立し、 `PATH`（ビルドプロセス中、およびアプリケーションのランタイム環境で）

これらの依存関係は、次のように指定できます。

```yaml
ruby:
   sass: "~3.4"
nodejs:
   grunt-cli: "~0.3"
```

## `runtime`

実行時に PHP の設定を変更する場合に使用します。例えば、拡張機能を有効にします。 次の拡張機能が必要です。

```yaml
runtime:
    extensions:
        - xsl
        - newrelic
        - sodium
```

詳しくは、 [PHP 設定](php-settings.md) を参照してください。

## `disk`

アプリケーションの永続的なディスクサイズを MB 単位で定義します。

```yaml
disk: 5120
```

最小推奨ディスクサイズは 256 MB です。 エラーが表示された場合 `UserError: Error building the project: Disk size may not be smaller than 128MB`を使用する場合は、サイズを 256 MB に増やします。

>[!NOTE]
>
>プロステージング環境と実稼動環境の場合は、次の操作を行う必要があります。 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) を更新するには、 `mounts` および `disk` 設定を使用します。 チケットを送信する際に、必要な設定変更を指定し、 `.magento.app.yaml` ファイル。

## `relationships`

アプリケーションでサービスマッピングを定義します。

関係 `name` は、 `MAGENTO_CLOUD_RELATIONSHIPS` 環境変数。 The `<service-name>:<endpoint-name>` 関係は、 `.magento/services.yaml` ファイル。

```yaml
relationships:
    <name>: "<service-name>:<endpoint-name>"
```

次に、デフォルトの関係の例を示します。

```yaml
relationships:
    database: "mysql:mysql"
    redis: "redis:redis"
    opensearch: "opensearch:opensearch"
    rabbitmq: "rabbitmq:rabbitmq"
```

詳しくは、 [サービス](../services/services-yaml.md) ：現在サポートされているサービスタイプとエンドポイントの完全なリスト。

## `mounts`

キーがアプリケーションのルートを基準とした相対パスであるオブジェクト。 マウントは、ファイルのディスク上の書き込み可能な領域です。 以下は、 `magento.app.yaml` ファイルを `volume_id[/subpath]` 構文：

```yaml
 # The mounts that will be performed when the package is deployed.
mounts:
    "var": "shared:files/var"
    "app/etc": "shared:files/etc"
    "pub/media": "shared:files/media"
    "pub/static": "shared:files/static"
```

このリストにマウントを追加する形式は次のとおりです。

```bash
"/public/sites/default/files": "shared:files/files"
```

- `shared` — 環境内のアプリケーション間でボリュームを共有します。
- `disk` — 共有ボリュームに使用できるサイズを定義します。

>[!NOTE]
>
>プロステージング環境と実稼動環境の場合は、次の操作を行う必要があります。 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) を更新するには、 `mounts` および `disk` 設定を使用します。 チケットを送信する際に、必要な設定変更を指定し、 `.magento.app.yaml` ファイル。

マウント Web を [`web`](web-property.md) 場所のブロック。

>[!WARNING]
>
>サイトにデータが取り込まれたら、 `subpath` マウント名の一部。 この値は、 `files` 領域。 この名前を変更すると、古い場所に保存されているすべてのサイトデータが失われます。

## `access`

The `access` プロパティは、環境への SSH アクセスが許可される最小のユーザーロールレベルを示します。 使用可能なユーザーの役割は次のとおりです。

- `admin` — 環境内で設定を変更し、アクションを実行できます。 _投稿者_ および _閲覧者_ 権限。
- `contributor` — コードをこの環境にプッシュし、環境から分岐できます。はを持ちます。 _閲覧者_ 権限。
- `viewer` — 環境のみを表示できます。

デフォルトのユーザーの役割は次のとおりです。 `contributor`：を持つユーザーの SSH アクセスを制限します。 _閲覧者_ 権限。 ユーザーの役割は、 `viewer` を持つユーザーのみに SSH アクセスを許可するには _閲覧者_ 権限：

```yaml
access:
    ssh: viewer
```
