---
title: プロパティ
description: を設定する際の参照としてのプロパティリストの使用 [!DNL Commerce] ビルドしてクラウドインフラストラクチャにデプロイするためのアプリケーション。
feature: Cloud, Configuration, Build, Deploy, Roles/Permissions, Storage
exl-id: 58a86136-a9f9-4519-af27-2f8fa4018038
source-git-commit: 99272d08a11f850a79e8e24857b7072d1946f374
workflow-type: tm+mt
source-wordcount: '797'
ht-degree: 0%

---

# アプリケーション設定のプロパティ

この `.magento.app.yaml` ファイルではプロパティを使用して、の環境サポートを管理します [!DNL Commerce] アプリケーション。

| 名前 | 説明 | デフォルト | 必須 |
| ------ | --------------------------------- | ------- | -------- |
| [`access`](#access) | ユーザーの役割のカスタマイズ | — | 不可 |
| [`crons`](crons-property.md) | 仕様の更新と cron ジョブのスケジュール | — | 不可 |
| [`dependencies`](#dependencies) | 追加依存関係を有効にする | `php:composer/composer: '2.2.4'` | 不可 |
| [`disk`](#disk) | 永続ディスクサイズの定義 | `5120` | はい |
| [`firewall`](firewall-property.md) | （スターターのみ）送信トラフィックの制御 | — | 不可 |
| [`hooks`](hooks-property.md) | ビルド、デプロイ、デプロイ後の各フェーズ用のシェルコマンドのカスタマイズ | — | 不可 |
| [`mounts`](#mounts) | パスを設定 | パス：<ul><li>`"var": "shared:files/var"`</li><li>`"app/etc": "shared:files/etc"`</li><li>`"pub/media": "shared:files/media"`</li><li>`"pub/static": "shared:files/static"`</li></ul> | 不可 |
| [`name`](#name) | アプリケーション名を定義 | `mymagento` | はい |
| [`relationships`](#relationships) | マップサービス | サービス：<ul><li>`database: "mysql:mysql"`</li><li>`redis: "redis:redis"`</li><li>`opensearch: "opensearch:opensearch"`</li></ul> | 不可 |
| [`runtime`](#runtime) | Runtime プロパティには、が必要とする拡張機能が含まれています。 [!DNL Commerce] アプリケーション。 | 拡張機能：<ul><li>`xsl`</li><li>`newrelic`</li><li>`sodium`</li></ul> | はい |
| [`type`](#type-and-build) | ベースコンテナ画像の設定 | `php:8.3` | はい |
| [`variables`](variables-property.md) | 特定の Commerce バージョンに環境変数を適用する | — | 不可 |
| [`web`](web-property.md) | 外部リクエストの処理 | — | はい |
| [`workers`](workers-property.md) | 外部リクエストの処理 | — | はい（web プロパティを使用していない場合） |

{style="table-layout:auto"}

## `name`

この `name` プロパティは、で使用されるアプリケーション名を提供します [`routes.yaml`](../routes/routes-yaml.md) http アップストリームを定義するファイル （デフォルトでは `mymagento:http`）に設定します。 例えば、の値が `name` 等しい `app`を使用する必要があります。 `app:http` 「上流」フィールドに移動します。

>[!WARNING]
>
>デプロイ後にアプリケーションの名前を変更しないでください。 これにより、データが失われます。

## `type` および `build`

この `type`  および `build` プロパティは、プロジェクトをビルドおよび実行するためのベースコンテナイメージに関する情報を提供します。

サポートされるの `type` 言語は PHP です。 PHP のバージョンを次のように指定します。

```yaml
type: php:<version>
```

この `build` プロパティは、プロジェクトをビルドする際のデフォルトの動作を決定します。 この `flavor` 実行するビルド タスクの既定のセットを指定します。 次の例は、のデフォルト設定を示しています `type` および `build` から `magento-cloud/.magento.app.yaml`:

```yaml
# The toolstack used to build the application.
type: php:8.3
build:
    flavor: none

dependencies:
    php:
        composer/composer: '2.7.2'
```

### Composer 2 のインストールと使用

この `build: flavor:` プロパティは Composer 2.x では使用されないので、ビルド フェーズ中に Composer を手動でインストールする必要があります。 Composer 2.x を Starter プロジェクトおよび Pro プロジェクトにインストールして使用するには、3 つの変更を加える必要があります `.magento.app.yaml` 設定：

1. 削除 `composer` as the `build: flavor:` 追加： `none`. この変更により、Cloud ではデフォルトの 1.x バージョンの Composer を使用してビルド タスクを実行できなくなります。
1. 追加 `composer/composer: '^2.0'` as a `php` composer 2.x をインストールするための依存関係
1. を追加 `composer` へのタスクのビルド `build` フックを使用して Composer 2.x を使用してビルド タスクを実行する

次の設定フラグメントを独自に使用します `.magento.app.yaml` 設定：

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

参照： [必須パッケージ](../development/overview.md#required-packages) composer の詳細については、を参照してください。

## `dependencies`

ビルドプロセス中にアプリケーションで必要になる可能性のある依存関係を指定します。

Adobe Commerceは、次の言語の依存関係をサポートしています。

- PHP
- Ruby
- Node.js

これらの依存関係は、アプリケーションの最終的な依存関係には依存せず、 `PATH`を使用します。使用するアプリケーションのビルドプロセス中およびランタイム環境で使用します。

これらの依存関係は、次のように指定できます。

```yaml
ruby:
   sass: "~3.4"
nodejs:
   grunt-cli: "~0.3"
```

## `runtime`

を使用して、拡張機能の有効化など、実行時に PHP 設定を変更します。 次の拡張機能が必要です。

```yaml
runtime:
    extensions:
        - xsl
        - newrelic
        - sodium
```

参照： [PHP 設定](php-settings.md) 拡張機能の有効化について詳しくは、を参照してください。

## `disk`

アプリケーションの永続ディスクサイズを MB 単位で定義します。

```yaml
disk: 5120
```

推奨される最小ディスクサイズは 256 MB です。 次のエラーが表示される場合： `UserError: Error building the project: Disk size may not be smaller than 128MB`の場合は、サイズを 256 MB に増やします。

>[!NOTE]
>
>ステージング環境および実稼動環境の場合は、次の操作が必要です [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) を更新します `mounts` および `disk` アプリケーションの設定。 チケットを送信する際に、必要な設定変更を指定し、の更新バージョンを含めます `.magento.app.yaml` ファイル。

## `relationships`

アプリケーションでサービスマッピングを定義します。

関係 `name` は、内のアプリケーションで使用できます。 `MAGENTO_CLOUD_RELATIONSHIPS` 環境変数。 この `<service-name>:<endpoint-name>` 関係は、で定義された名前とタイプの値にマッピングされます。 `.magento/services.yaml` ファイル。

```yaml
relationships:
    <name>: "<service-name>:<endpoint-name>"
```

デフォルトの関係の例を次に示します。

```yaml
relationships:
    database: "mysql:mysql"
    redis: "redis:redis"
    opensearch: "opensearch:opensearch"
    rabbitmq: "rabbitmq:rabbitmq"
```

参照： [サービス](../services/services-yaml.md) 現在サポートされているサービスタイプとエンドポイントの完全なリストについては、を参照してください。

## `mounts`

キーがアプリケーションのルートに対する相対パスであるオブジェクト。 マウントは、ファイルのディスク上の書き込み可能な領域です。 に設定されたマウントのデフォルトのリストは次のとおりです。 `magento.app.yaml` を使用したファイル `volume_id[/subpath]` 構文：

```yaml
 # The mounts that will be performed when the package is deployed.
mounts:
    "var": "shared:files/var"
    "app/etc": "shared:files/etc"
    "pub/media": "shared:files/media"
    "pub/static": "shared:files/static"
```

このリストにマウントを追加する形式は、次のとおりです。

```bash
"/public/sites/default/files": "shared:files/files"
```

- `shared` – 環境内のアプリケーション間でボリュームを共有します。
- `disk` – 共有ボリュームに使用できるサイズを定義します。

>[!NOTE]
>
>ステージング環境および実稼動環境の場合は、次の操作が必要です [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) を更新します `mounts` および `disk` アプリケーションの設定。 チケットを送信する際に、必要な設定変更を指定し、の更新バージョンを含めます `.magento.app.yaml` ファイル。

マウント web をに追加すると、その web にアクセスできるようになります。 [`web`](web-property.md) 場所のブロック。

>[!WARNING]
>
>サイトにデータが取り込まれたら、 `subpath` マウント名の一部。 この値は、の一意の識別子です `files` 領域。 この名前を変更すると、古い場所に保存されているすべてのサイト データが失われます。

## `access`

この `access` プロパティは、環境への SSH アクセスが許可されている最小ユーザー役割レベルを示します。 使用できるユーザーの役割は次のとおりです。

- `admin` – 設定を変更し、環境内でアクションを実行できます。 _投稿者_ および _ビューア_ 権限。
- `contributor` – この環境にコードをプッシュし、環境から分岐できます。 _ビューア_ 権限。
- `viewer` – 環境のみを表示できます。

デフォルトのユーザーの役割はです。 `contributor`（のユーザーのみからの SSH アクセスを制限します） _ビューア_ 権限。 ユーザーの役割は、次のように変更できます。 `viewer` を持つユーザーだけに SSH アクセスを許可するには _ビューア_ 権限：

```yaml
access:
    ssh: viewer
```
