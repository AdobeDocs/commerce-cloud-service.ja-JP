---
title: '[!DNL ECE-Tools] パッケージ'
description: について説明します [!DNL ECE-Tools] パッケージと、Adobe Commerceの管理とデプロイにどのように役立つか。
exl-id: 5583a685-29c5-4de5-8d2e-94cff5ff37ab
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '431'
ht-degree: 0%

---

# ECE-Tools パッケージ

この [!DNL ECE-Tools] パッケージは、を管理およびデプロイするために設計された一連のスクリプトとツールです [!DNL Commerce] アプリケーション。 この `ece-tools` パッケージを使用すると、cron ジョブの管理、プロジェクト設定の検証、Adobeパッチやホットフィックスの適用など、多くのプロセスを簡単に実行できます。 を表示して、に投稿できます。 [open-source [!DNL ECE-Tools] github のコードリポジトリー][ece-repo].

{{ece-tools-package}}

この `ece-tools` パッケージはAdobe Commerce（バージョン 2.1.4 以降）と互換性があり、コードの管理とプロジェクトの自動ビルドおよびデプロイに役立つ、クラウドインフラストラクチャコマンド上のスクリプトおよびAdobe Commerceが含まれています。

使用可能なリストは次のとおりです `ece-tools` コマンド：

```bash
php ./vendor/bin/ece-tools list
```

## ビルドとデプロイ

この `ece-tools` パッケージには、クラウドインフラストラクチャアプリケーション上でAdobe Commerceを起動する際の、ビルド、デプロイ、およびデプロイ後の段階で操作を実行するコマンドが含まれています。 例： `php ./vendor/bin/ece-tools build` コマンドは、アプリケーションのビルド プロセスを開始します。

デフォルトでは、次のようになります `ece-tools` コマンドは [hooks プロパティ](../application/hooks-property.md) の `.magento.app.yaml` 設定ファイル。

## Docker 設定ジェネレーター

この `ece-tools` パッケージには、の依存関係が含まれています [magento/magento-cloud-docker] パッケージ。Docker イメージの機能ファイルと設定ファイルを提供して、クラウドインフラストラクチャ上でAdobe Commerce用の Docker 開発環境を起動します。 また、Cloud Docker for Commerceをスタンドアロンパッケージとして実行することもできます。 参照： [Docker 開発](../dev-tools/cloud-docker.md).

## サービス、ルート、変数

を使用できます `ece-tools` base64 でエンコードされたに関する詳細情報を表示するパッケージ [クラウド変数](../environment/variables-cloud.md) 任意のクラウド環境で使用します。 次のコマンドは、すべてのサービス、ルート、変数を表示します。

```bash
php ./vendor/bin/ece-tools env:config:show
```

特定の情報セットを表示するには、次の形式を使用します。

```bash
php ./vendor/bin/ece-tools env:config:show <option>
```

- `services` – から関係データを表示します。 `MAGENTO_CLOUD_RELATIONSHIPS` 環境変数（で定義） `services.yaml` ファイル。
- `routes` – を使用して、プロジェクトに設定されているルートを表示します。 `MAGENTO_CLOUD_ROUTES` 環境変数。
- `variables` – を使用して、プロジェクトに設定済みの変数を表示します。 `MAGENTO_CLOUD_VARIABLES` 環境変数。

のサンプル出力 `services` オプション：

```terminal
Magento Cloud Services:
+-----------------------------------+----------------------------------+
| Service Configuration             | Value                            |
+-----------------------------------+----------------------------------+
| database:                                                            |
+-----------------------------------+----------------------------------+
| host                              | 127.0.0.1                        |
| password                          | <password>                       |
| port                              | 3306                             |
+-----------------------------------+----------------------------------+
| opensearch:                                                          |
+-----------------------------------+----------------------------------+
| host                              | 127.0.0.1                        |
| port                              | 9200                             |
...
```

## 環境設定の確認

プロジェクトの設定を評価するのに役立つ一連の検証コマンドを使用できます。 参照： [スマート・ウィザード](../deploy/smart-wizards.md) が含まれる _デプロイメントの最適化_ 各ウィザードコマンドの詳細については、を参照してください。 この `wizard:ideal-state` コマンドは、ビルドフェーズで自動的に実行されます。 プロジェクトの理想的な状態を確認するには：

```bash
php ./vendor/bin/ece-tools wizard:ideal-state
```

>[!NOTE]
>
>を実行する必要があります `wizard:ideal-state` コマンドをリモートクラウド環境で使用する。 このコマンドは常にパラメーターを `The configured state is not ideal` ローカル開発環境で実行するとエラーが発生する。

サンプル出力：

```terminal
Ideal state is configured
```

参照： [ece-tools のリリースノート](../release-notes/cloud-tools-suite.md).

## Adobeパッチとカスタムパッチ

この `ece-tools` パッケージには、の依存関係が含まれています [magento/magento-cloud-patches] Adobe Commerceのすべてのバージョンとクラウド環境のAdobeを向上させる統合パッチやホットフィックスを提供し、重要な修正を迅速に配信できるパッケージです。 「」には、クラウドインフラストラクチャプロジェクト上のAdobe Commerceに追加するカスタムパッチも提供されます。 参照： [パッチの適用](../development/apply-patches.md).

<!-- link definitions -->

[ece-repo]: https://github.com/magento/ece-tools
[magento/magento-cloud-docker]: https://github.com/magento/magento-cloud-docker
[magento/magento-cloud-patches]: https://github.com/magento/magento-cloud-patches
