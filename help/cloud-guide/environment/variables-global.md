---
title: グローバル変数
description: クラウドインフラストラクチャデプロイメントプロセス上のAdobe Commerceのアクションを制御する環境変数のリストを参照してください。
feature: Cloud, Configuration, Build, Deploy, Eventing, Logs, SCD
recommendations: noDisplay, catalog
role: Developer
exl-id: 04c2861d-746d-42d4-a678-f6c7b464eb51
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '752'
ht-degree: 0%

---

# グローバル変数

グローバル変数は、 [!DNL Commerce] デプロイメントプロセス：ビルド、デプロイ、デプロイ後。 グローバル変数は各フェーズに影響するので、 `global` ステージ `.magento.env.yaml` ファイル：

```yaml
stage:
  global:
    GLOBAL_VARIABLE_NAME: value
```

ビルドおよびデプロイプロセスのカスタマイズに関する詳細情報：

- [デプロイメント設定](configure-env-yaml.md)
- [デプロイメントプロセス](../deploy/process.md)

## `ENABLE_EVENTING`

- **デフォルト**-_未設定_
- **バージョン**—Adobe Commerce 2.4.5 以降

に設定されている場合 `true`を使用すると、cron でメッセージキューコンシューマーを実行できます。 Adobe CommerceのAdobe I/Oイベントは、メッセージキューを使用して重要なイベントの配信を迅速に行います。

Adobeでは、以下も追加することをお勧めします [`CRON_CONSUMERS_RUNNER`](./variables-deploy.md#cron_consumers_runner) 変数を `deploy` ステージ `.magento.env.yaml` を含むファイル `cron_run` をに設定 `true`.

次の例は、完全に設定されたを示しています `ENABLE_EVENTING` 変数。

```yaml
stage:
  global:
    ENABLE_EVENTING: true
  deploy:
    CRON_CONSUMERS_RUNNER:
      cron_run: true
      max_messages: 0
      consumers: []
```

## ENABLE_WEBHOOK

- **デフォルト**-_未設定_
- **バージョン**—Adobe Commerce 2.4.4 以降

に設定されている場合 `true`、Commerce Webhook を有効にします。 Webhook は、App Builder ランタイムアクションやサードパーティのインベントリ管理システムなどの外部エンドポイントで実行されます。 この [_Webhook ガイド_](https://developer.adobe.com/commerce/extensibility/webhooks) では、この機能について詳しく説明します。

```yaml
stage:
  global:
    ENABLE_WEBHOOKS: true
```

## `MIN_LOGGING_LEVEL`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

コードを変更せずに、すべての出力ストリームの最小ログレベルを上書きします。これは、デプロイメントに関する問題のトラブルシューティングに役立ちます。 例えば、デプロイメントが失敗した場合、この変数を使用して、ログの精度をグローバルに高めることができます。 参照： [ログレベル](log-handlers.md#log-levels). この `min_level` ログハンドラーの値によってこの設定が上書きされます。

```yaml
stage:
  global:
    MIN_LOGGING_LEVEL: debug
```

>[!WARNING]
>
>設定： `MIN_LOGGING_LEVEL` 変数は、に設定されているファイルハンドラーのログレベル設定を変更しません。 `debug` デフォルトでは。

## `SCD_ON_DEMAND`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

ユーザー（SCD）から要求されたときに静的コンテンツを生成できるようにします。 静的コンテンツは、デプロイメント時間が短縮されるので、開発およびテストワークフローに最適です。

を使用したキャッシュのプリロード [`post_deploy` フック](../application/hooks-property.md) サイトのダウンタイムを短縮します。 キャッシュウォーミングは、にステージング環境と実稼動環境が含まれている Pro プロジェクトでのみ使用できます [!DNL Cloud Console] スタータープロジェクトの場合はです。 を追加 `SCD_ON_DEMAND` 環境変数をに設定 `global` のステージ `.magento.env.yaml` ファイル：

```yaml
stage:
  global:
    SCD_ON_DEMAND: true
```

この `SCD_ON_DEMAND` 変数は、両方のフェーズ（ビルドとデプロイ）で SCD をスキップし、 `pub/static` および `var/view_preprocessed` フォルダーを作成し、次の内容をに書き込みます `app/etc/env.php` ファイル：

```php?start_inline=1
return array(
   ...
   'static_content_on_demand_in_production' => 1,
   ...
);
```

## `SCD_MAX_EXECUTION_TIME`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.2.0 以降

静的コンテンツのデプロイメントの予想最大実行時間を増やすことができます。

デフォルトでは、Adobe Commerceは想定される最大実行時間を 900 秒に設定しますが、場合によっては、Cloud プロジェクトの静的コンテンツのデプロイメントを完了するためにより多くの時間が必要になることがあります。

```yaml
stage:
  global:
    SCD_MAX_EXECUTION_TIME: 3600
```

{{scd-timing-warning}}

## `SCD_NO_PARENT`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.4.2 以降

をに設定 `true` ビルドおよびデプロイメントのフェーズ中に親テーマの静的コンテンツが生成されないようにする。 このオプションを `true`の場合は、生成される静的コンテンツの量が少なくなるので、ビルドとデプロイメントの全体的な時間が短縮されます。

```yaml
stage:
  global:
    SCD_NO_PARENT: true
```

## `SCD_USE_BALER`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.3.0 以降

[ベーラー](https://github.com/magento/baler) は、生成された JavaScript コードをスキャンし、最適化された JavaScript バンドルを作成するモジュールです。 最適化されたバンドルをサイトにデプロイすると、サイトを読み込む際のネットワークリクエストの数を減らし、ページの読み込み時間を短縮できます。

をに設定 `true` 静的コンテンツデプロイメントの実行後に Baler を実行する場合。

```yaml
stage:
  build:
    SCD_USE_BALER: true
```

>[!NOTE]
>
>この機能を使用する前に、Baler モジュールをインストールして設定します。 Baler はアルファリリースなので、ステージング環境でのみ、このオプションを有効にします。

## `SKIP_HTML_MINIFICATION`

- **デフォルト**:
   - `true` – の場合 `ece-tools` 2002.0.13 以降
   - `false` – 以前のバージョンの `ece-tools`
- **バージョン**—Adobe Commerce 2.1.4 以降

静的ビューファイルのコピーを有効または無効にします `<magento_root>/init/` ビルドステージの最後のディレクトリ。 に設定されている場合 `true`を選択すると、ファイルはコピーされず、リクエストに応じてHTMLの縮小が利用できます。 この値をに設定 `true` ステージング環境および実稼動環境にデプロイする際のダウンタイムを短縮する。

- **`false`** – をコピーする `view_preprocessed` ディレクトリ `<magento_root>/init/` ビルドフェーズの最後にあるディレクトリ。でディレクトリを復元します。 `<magento_root>/var` デプロイフェーズの開始時のディレクトリ。
- **`true`**— オン・デマンドのHTML縮小を有効にします。 _ではない_ をコピー `<magento_root>var/view_preprocessed` に `<magento_root>/init/` ビルドフェーズの最後にあるディレクトリ。

を追加 `SKIP_HTML_MINIFICATION` 環境変数をに設定 `global` のステージ `.magento.env.yaml` ファイル：

```yaml
stage:
  global:
    SKIP_HTML_MINIFICATION: true
```

## `X_FRAME_CONFIGURATION`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

の使用 `X_FRAME_CONFIGURATION` を変更する変数 [`X-Frame-Options`](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/security/xframe-options.html) Adobe Commerce サイトのヘッダー設定。 この設定は、ブラウザーがページをレンダリングする方法を `<frame>`, `<iframe>`、または `<object>`. 次のいずれかのオプションを使用します。

- `DENY`- ページをフレーム内に表示できません。
- `SAMEORIGIN` – （デフォルトのAdobe Commerce設定。） ページは、ページ自体と同じ原点のフレーム内でのみ表示できます。

>[!WARNING]
>
>この `ALLOW-FROM <uri>` Adobe Commerceでサポートされているブラウザーがサポートしなくなったので、オプションは非推奨（廃止予定）になりました。 参照： [ブラウザーの互換性](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options#Browser_compatibility).

を追加 `X_FRAME_CONFIGURATION` 環境変数をに設定 `global` のステージ `.magento.env.yaml` ファイル：

```yaml
stage:
  global:
    X_FRAME_CONFIGURATION: SAMEORIGIN
```
