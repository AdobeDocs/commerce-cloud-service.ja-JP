---
title: クラウド固有の変数
description: クラウドインフラストラクチャー上のAdobe Commerceに固有の環境変数のリストを参照してください。
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

クラウドインフラストラクチャー上のAdobe Commerceに固有の環境変数では、 `MAGENTO_CLOUD_*` プレフィックス：

| 変数 | 説明 |
| -------- | --------------- |
| `MAGENTO_CLOUD_APP_DIR` | アプリケーションディレクトリの絶対パス。 |
| `MAGENTO_CLOUD_APPLICATION` | アプリケーションを記述する、base64 でエンコードされた JSON オブジェクト。 これは、 `.magento.app.yaml` ファイルの内容で、サブキーがあります。 |
| `MAGENTO_CLOUD_APPLICATION_NAME` | で設定されたアプリケーションの名前 `.magento.app.yaml` ファイル。 |
| `MAGENTO_CLOUD_DOCUMENT_ROOT` | Web ドキュメントルートの絶対パス（該当する場合）。 |
| `MAGENTO_CLOUD_ENVIRONMENT` | 環境ブランチの名前。 |
| `MAGENTO_CLOUD_PROJECT` | プロジェクト ID。 |
| `MAGENTO_CLOUD_RELATIONSHIPS` | キー（関係名）と値（関係ペアの配列）のエンドポイント定義を表す、base64 でエンコードされた JSON オブジェクト。 各関係エンドポイント定義は、URL を分解した形式です。 次の情報があります `scheme`, a `host`, a `port`、および _任意選択_ a `username`, `password`, `path`、およびいくつかの追加情報： `query`. |
| `MAGENTO_CLOUD_ROUTES` | 環境で定義されたルートの記述 `.magento/routes.yaml` ファイル。 |
| `MAGENTO_CLOUD_TREE_ID` | アプリケーションのツリー ID （Git のツリーの SHA に対応）。 |
| `MAGENTO_CLOUD_VARIABLES` | 次のようなキーと値のペアを持つ、base64 でエンコードされた JSON オブジェクト `"key":"value"`. |
| `MAGENTO_CLOUD_LOCKS_DIR` | クラウドインフラストラクチャ上のロックプロバイダーのマウントポイントへのパスを提供します。 ロックプロバイダーは、重複した cron ジョブや cron グループの起動を防ぎます。 |

>[!WARNING]
>
>環境変数をに追加するには [設定を上書き](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/paths/override-config-settings.html) の使用 [[!DNL Cloud Console]](../project/overview.md)は、変数名の前にを付ける必要があります `env:` 次の例のようになります。
>
>![環境変数の例](../../assets/set-env-variable-ui.png)

値は時間の経過と共に変化する可能性があるので、実行時に変数を調べて、それを使用してアプリケーションを設定することをお勧めします。 例えば、 `MAGENTO_CLOUD_RELATIONSHIPS` 環境関連の関係を取得する変数を次に示します。

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

を使用できます `env:config:show` コマンド元 [この `ece-tools` package](../dev-tools/package-overview.md) 現在の環境の変数のリストを表示します。

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
