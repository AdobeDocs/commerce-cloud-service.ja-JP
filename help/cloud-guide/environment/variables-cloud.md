---
title: クラウド固有の変数
description: クラウドインフラストラクチャ上のAdobe Commerceに固有の環境変数の一覧を参照してください。
feature: Cloud, Configuration
recommendations: noDisplay, catalog
role: Developer
exl-id: 84b7c0fc-f0b0-4ff5-9f33-9d17180a9306
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '272'
ht-degree: 0%

---

# クラウド固有の変数

クラウドインフラストラクチャ上のAdobe Commerceに固有の環境変数は、 `MAGENTO_CLOUD_*` プレフィックス：

| 変数 | 説明 |
| -------- | --------------- |
| `MAGENTO_CLOUD_APP_DIR` | アプリケーションディレクトリの絶対パス。 |
| `MAGENTO_CLOUD_APPLICATION` | アプリケーションを表す base64 エンコードされた JSON オブジェクト。 これは、 `.magento.app.yaml` ファイルコンテンツで、サブキーを持つ。 |
| `MAGENTO_CLOUD_APPLICATION_NAME` | で設定されたアプリケーションの名前 `.magento.app.yaml` ファイル。 |
| `MAGENTO_CLOUD_DOCUMENT_ROOT` | Web ドキュメントのルートへの絶対パス（該当する場合）。 |
| `MAGENTO_CLOUD_ENVIRONMENT` | 環境ブランチの名前。 |
| `MAGENTO_CLOUD_PROJECT` | プロジェクト ID。 |
| `MAGENTO_CLOUD_RELATIONSHIPS` | キー（関係名）および値（関係のペアの配列）エンドポイント定義を表す base64 でエンコードされた JSON オブジェクト。 各関係エンドポイント定義は、URL の分解された形式です。 これには、 `scheme`, a `host`, a `port`、および _任意_ a `username`, `password`, `path`、およびいくつかの追加情報 `query`. |
| `MAGENTO_CLOUD_ROUTES` | 環境で定義されたルートの説明 `.magento/routes.yaml` ファイル。 |
| `MAGENTO_CLOUD_TREE_ID` | アプリケーションのツリー ID（Git のツリーの SHA に対応）。 |
| `MAGENTO_CLOUD_VARIABLES` | キーと値のペアを持つ、base64 でエンコードされた JSON オブジェクト ( 例： `"key":"value"`. |
| `MAGENTO_CLOUD_LOCKS_DIR` | クラウドインフラストラクチャ上のロックプロバイダーのマウントポイントへのパスを提供します。 ロックプロバイダーは、重複する cron ジョブや cron グループの起動を防ぎます。 |

>[!WARNING]
>
>環境変数をに追加するには、以下を実行します。 [構成設定を上書き](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/paths/override-config-settings.html) の使用 [[!DNL Cloud Console]](../project/overview.md)を使用する場合は、変数名の前にを付ける必要があります。 `env:` 次の例のようになります。
>
>![環境変数の例](../../assets/set-env-variable-ui.png)

値は時間の経過と共に変化する可能性があるので、実行時に変数を調べ、それを使用してアプリケーションを設定するのが最適です。 例えば、 `MAGENTO_CLOUD_RELATIONSHIPS` 変数を使用して、環境関連の関係を次のように取得します。

```php
<?php
/**
  * Get relationships information from cloud environment variable.
  *
  * @return mixed
  */
    protected function getRelationships()
    {
        return json_decode(base64_decode($_ENV["MAGENTO_CLOUD_RELATIONSHIPS"]), true);
    }
```

## 環境変数の表示

以下を使用すると、 `env:config:show` 命令 [の `ece-tools` パッケージ](../dev-tools/package-overview.md) 現在の環境の変数のリストを表示します。

```bash
php ./vendor/bin/ece-tools env:config:show variables
```

のサンプル出力 `variables` オプション：

```terminal
Magento Cloud Environment Variables:
+-----------------------------------+----------------------------------+
| Variable name                     | Value                            |
+-----------------------------------+----------------------------------+
| ADMIN_EMAIL                       | commerceadmin@company.com        |
| ADMIN_PASSWORD                    | 123123q                          |
+-----------------------------------+----------------------------------+
```
