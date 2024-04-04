---
title: '[!DNL ECE-Tools] パッケージ'
description: 詳しくは、 [!DNL ECE-Tools] パッケージと、Adobe Commerceの管理とデプロイに役立つ情報を提供します。
exl-id: 5583a685-29c5-4de5-8d2e-94cff5ff37ab
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '431'
ht-degree: 0%

---

# ECE-Tools パッケージ

The [!DNL ECE-Tools] パッケージは、 [!DNL Commerce] アプリケーション。 The `ece-tools` パッケージは、cron ジョブの管理、プロジェクト設定の検証、Adobeパッチとホットフィックスの適用など、多くのプロセスを簡素化します。 以下を表示し、投稿して、 [open-source [!DNL ECE-Tools] GitHub のコードリポジトリ][ece-repo].

{{ece-tools-package}}

The `ece-tools` パッケージは、バージョン 2.1.4 以降のAdobe Commerceと互換性があり、コードの管理とプロジェクトの自動構築およびデプロイを支援するスクリプトおよびAdobe Commerce on cloud infrastructure コマンドが含まれます。

以下に、使用可能な `ece-tools` コマンド：

```bash
php ./vendor/bin/ece-tools list
```

## ビルドとデプロイ

The `ece-tools` パッケージには、クラウドインフラストラクチャアプリケーション上でAdobe Commerceを起動する際の、ビルド、デプロイ、デプロイ後の段階で操作を実行するコマンドが含まれています。 例えば、 `php ./vendor/bin/ece-tools build` コマンドは、アプリケーションビルドプロセスを開始します。

デフォルトでは、次の `ece-tools` コマンドは、 [フックプロパティ](../application/hooks-property.md) の `.magento.app.yaml` 設定ファイル。

## Docker 設定ジェネレーター

The `ece-tools` パッケージには、 [magento/magento-cloud-docker] パッケージ。クラウドインフラストラクチャ上のAdobe Commerceの Docker 開発環境を起動するための、Docker イメージの機能と設定ファイルを提供します。 Cloud Docker for Commerce をスタンドアロンパッケージとして実行することもできます。 詳しくは、 [Docker の開発](../dev-tools/cloud-docker.md).

## サービス、ルート、変数

以下を使用すると、 `ece-tools` Base64 エンコード済みに関する詳細情報を表示するパッケージ [クラウド変数](../environment/variables-cloud.md) 任意のクラウド環境で使用されます。 次のコマンドは、すべてのサービス、ルート、および変数を表示します。

```bash
php ./vendor/bin/ece-tools env:config:show
```

特定の情報セットを表示するには、次の形式を使用します。

```bash
php ./vendor/bin/ece-tools env:config:show <option>
```

- `services` — 次の関係データを表示します： `MAGENTO_CLOUD_RELATIONSHIPS` 環境変数（内で定義） `services.yaml` ファイル。
- `routes` — を使用して、プロジェクトの設定済みルートを表示します。 `MAGENTO_CLOUD_ROUTES` 環境変数。
- `variables` — を使用して、プロジェクトの設定済み変数を表示します。 `MAGENTO_CLOUD_VARIABLES` 環境変数。

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

## 環境設定の検証

プロジェクトの設定を評価するのに役立つ、一連の検証コマンドが用意されています。 詳しくは、 [スマートウィザード](../deploy/smart-wizards.md) （内） _デプロイメントの最適化_ 各ウィザードコマンドの詳細な説明は、「 」を参照してください。 The `wizard:ideal-state` コマンドは、ビルドフェーズ中に自動的に実行されます。 プロジェクトの理想的な状態を検証するには：

```bash
php ./vendor/bin/ece-tools wizard:ideal-state
```

>[!NOTE]
>
>次を実行する必要があります： `wizard:ideal-state` コマンドを使用して、リモートクラウド環境で設定できます。 このコマンドは常に `The configured state is not ideal` エラーが発生しました。ローカル開発環境で実行します。

サンプル出力：

```terminal
Ideal state is configured
```

詳しくは、 [ece-tools のリリースノート](../release-notes/cloud-tools-suite.md).

## Adobeパッチとカスタムパッチ

The `ece-tools` パッケージには、 [magento/magento-cloud-patches] パッケージは、すべてのAdobe CommerceAdobeのクラウド環境との統合を改善し、重要な修正の迅速な配信をサポートするクラウドパッチとホットフィックスを提供します。 また、「 」は、クラウドインフラストラクチャプロジェクト上のAdobe Commerceに追加したカスタムパッチも提供します。 詳しくは、 [パッチの適用](../development/apply-patches.md).

<!-- link definitions -->

[ece-repo]: https://github.com/magento/ece-tools
[magento/magento-cloud-docker]: https://github.com/magento/magento-cloud-docker
[magento/magento-cloud-patches]: https://github.com/magento/magento-cloud-patches
