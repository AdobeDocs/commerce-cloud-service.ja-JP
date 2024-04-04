---
title: グローバル変数
description: クラウドインフラストラクチャのデプロイメントプロセスに関するAdobe Commerceのアクションを制御する環境変数の一覧を参照してください。
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

グローバル変数は、 [!DNL Commerce] デプロイメントプロセス：ビルド、デプロイ、デプロイ後。 グローバル変数は各フェーズに影響を与えるので、 `global` 段階 `.magento.env.yaml` ファイル：

```yaml
stage:
  global:
    GLOBAL_VARIABLE_NAME: value
```

ビルドおよびデプロイプロセスのカスタマイズについて詳しくは、次の手順を参照してください。

- [デプロイメント設定](configure-env-yaml.md)
- [デプロイメントプロセス](../deploy/process.md)

## `ENABLE_EVENTING`

- **デフォルト**-_未設定_
- **バージョン**—Adobe Commerce 2.4.5 以降

に設定する場合 `true`を使用すると、cron はメッセージキューのコンシューマーを実行できます。 Adobe CommerceのAdobe I/Oイベントでは、重要なイベントの配信を迅速におこなうためにメッセージキューを使用します。

Adobeでは、 [`CRON_CONSUMERS_RUNNER`](./variables-deploy.md#cron_consumers_runner) 変数を `deploy` 段階 `.magento.env.yaml` ～をファイルで囲む `cron_run` に設定 `true`.

次の例は、完全に設定された `ENABLE_EVENTING` 変数を使用します。

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

## ENABLE_WEBHOOKS

- **デフォルト**-_未設定_
- **バージョン**—Adobe Commerce 2.4.4 以降

に設定する場合 `true`は、Commerce の Web フックを有効にします。 Webhook は、App Builder ランタイムアクションやサードパーティの在庫管理システムなど、外部のエンドポイントで実行されます。 The [_ウェブフックガイド_](https://developer.adobe.com/commerce/extensibility/webhooks) では、この機能の詳細について説明します。

```yaml
stage:
  global:
    ENABLE_WEBHOOKS: true
```

## `MIN_LOGGING_LEVEL`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

コードを変更せずに、すべての出力ストリームの最小ログレベルを上書きします。これは、デプロイメントの問題のトラブルシューティングに役立ちます。 例えば、デプロイメントが失敗した場合、この変数を使用して、グローバルにログの精度を高めることができます。 詳しくは、 [ログレベル](log-handlers.md#log-levels). The `min_level` の値は、この設定を上書きします。

```yaml
stage:
  global:
    MIN_LOGGING_LEVEL: debug
```

>[!WARNING]
>
>の設定 `MIN_LOGGING_LEVEL` 変数は、ファイルハンドラーのログレベル設定を変更しません。この設定は、 `debug` デフォルトでは。

## `SCD_ON_DEMAND`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

ユーザー (SCD) の要求に応じて、静的コンテンツの生成を有効にします。 静的コンテンツオンデマンドは、デプロイメント時間を短縮するので、開発およびテストワークフローに最適です。

を使用したキャッシュのプリロード [`post_deploy` フック](../application/hooks-property.md) サイトのダウンタイムを短縮します。 キャッシュウォーミングは、 [!DNL Cloud Console] スタータープロジェクト用。 次を追加： `SCD_ON_DEMAND` 環境変数を `global` ステージの `.magento.env.yaml` ファイル：

```yaml
stage:
  global:
    SCD_ON_DEMAND: true
```

The `SCD_ON_DEMAND` 変数は、両方のフェーズで SCD をスキップ（ビルドとデプロイ）し、 `pub/static` および `var/view_preprocessed` フォルダに書き込み、以下を `app/etc/env.php` ファイル：

```php?start_inline=1
return array(
   ...
   'static_content_on_demand_in_production' => 1,
   ...
);
```

## `SCD_MAX_EXECUTION_TIME`

- **デフォルト**—_未設定_
- **バージョン**— Adobe Commerce 2.2.0 以降

静的コンテンツデプロイメントの予想される最大実行時間を増やすことができます。

デフォルトでは、Adobe Commerceでは予想される最大実行時間が 900 秒に設定されていますが、場合によっては、クラウドプロジェクトの静的コンテンツのデプロイメントを完了するのに、より多くの時間が必要になることがあります。

```yaml
stage:
  global:
    SCD_MAX_EXECUTION_TIME: 3600
```

{{scd-timing-warning}}

## `SCD_NO_PARENT`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.4.2 以降

をに設定します。 `true` ビルドおよびデプロイメントフェーズ中に親テーマ用の静的コンテンツが生成されるのを防ぐ。 このオプションが `true`静的でないコンテンツを生成すると、生成される静的性が低くなり、ビルドとデプロイメントに要する時間が全体的に短縮されます。

```yaml
stage:
  global:
    SCD_NO_PARENT: true
```

## `SCD_USE_BALER`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.3.0 以降

[Baler](https://github.com/magento/baler) は、生成された JavaScript コードをスキャンし、最適化された JavaScript バンドルを作成するモジュールです。 最適化されたバンドルをサイトにデプロイすると、サイトの読み込み時のネットワークリクエストの数を減らし、ページの読み込み時間を短縮できます。

をに設定します。 `true` 静的コンテンツのデプロイメントを実行した後に Baler を実行する。

```yaml
stage:
  build:
    SCD_USE_BALER: true
```

>[!NOTE]
>
>この機能を使用する前に、Baler モジュールをインストールして設定します。 Baler はアルファリリースなので、このオプションはステージング環境でのみ有効にします。

## `SKIP_HTML_MINIFICATION`

- **デフォルト**:
   - `true` — というのは `ece-tools` 2002.0.13 以降
   - `false` — 以前のバージョンの `ece-tools`
- **バージョン**—Adobe Commerce 2.1.4 以降

静的ビューファイルを `<magento_root>/init/` ディレクトリをビルドステージの最後に配置します。 に設定した場合、 `true`、ファイルはコピーされず、HTML縮小はリクエストで使用できます。 この値をに設定します。 `true` を参照してください。

- **`false`**- `view_preprocessed` ディレクトリの `<magento_root>/init/` ディレクトリをビルドフェーズの最後に配置し、 `<magento_root>/var` ディレクトリがデプロイフェーズの最初に表示されます。
- **`true`** — オンデマンドHTMLの縮小を有効にします。 _not_ コピー `<magento_root>var/view_preprocessed` から `<magento_root>/init/` ディレクトリに格納されます。

次を追加： `SKIP_HTML_MINIFICATION` 環境変数を `global` ステージの `.magento.env.yaml` ファイル：

```yaml
stage:
  global:
    SKIP_HTML_MINIFICATION: true
```

## `X_FRAME_CONFIGURATION`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

以下を使用します。 `X_FRAME_CONFIGURATION` 変数を使用して [`X-Frame-Options`](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/security/xframe-options.html) Adobe Commerceサイトのヘッダー設定。 この設定は、ブラウザーが `<frame>`, `<iframe>`または `<object>`. 次のいずれかのオプションを使用します。

- `DENY` — ページはフレームに表示できません。
- `SAMEORIGIN`—( デフォルトのAdobe Commerce設定 ) ページは、ページ自体と同じ接触チャネル上のフレームにのみ表示できます。

>[!WARNING]
>
>The `ALLOW-FROM <uri>` Adobe Commerceがサポートしているブラウザーはサポートしなくなったので、オプションは非推奨（廃止予定）になりました。 詳しくは、 [ブラウザーの互換性](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options#Browser_compatibility).

次を追加： `X_FRAME_CONFIGURATION` 環境変数を `global` ステージの `.magento.env.yaml` ファイル：

```yaml
stage:
  global:
    X_FRAME_CONFIGURATION: SAMEORIGIN
```
