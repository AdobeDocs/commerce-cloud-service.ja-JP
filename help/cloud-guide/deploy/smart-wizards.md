---
title: スマートウィザード
description: スマートウィザードを使用して、クラウドインフラストラクチャ上のAdobe Commerceプロジェクトがデプロイメントのベストプラクティスに従っているかどうかを評価する方法を説明します。
feature: Cloud, Build, Deploy, SCD
exl-id: eb79431c-8835-4ae4-b453-9c4932c5d5ac
source-git-commit: 225fba1acfd8b3ce4d7ce989c7851e7b0b218680
workflow-type: tm+mt
source-wordcount: '322'
ht-degree: 0%

---

# スマートウィザード

スマートウィザードを使用すると、クラウド設定がベストプラクティスに従っているかどうかを判断できます。 利用可能なウィザードは、次の設定に役立ちます。

- デプロイメントのダウンタイムを最小限に抑える理想的な状態
- データベースと Redis のロードバランシング設定
- オンデマンド、ビルドステージまたはデプロイステージ用の静的コンテンツデプロイメント (SCD)

各スマートウィザードコマンドは、検証応答を提供し、必要に応じて適切な設定を推奨します。

| コマンド | 説明 |
| ------- | ------------|
| `wizard:ideal-state` | SCD が _ビルド_ ステージ、 `SKIP_HTML_MINIFICATION` 変数 `true`、およびクラウド環境で設定された post_deploy フック。 ローカル開発環境では使用しません。 |
| `wizard:master-slave` | 以下を確認します。 `REDIS_USE_SLAVE_CONNECTION` 変数と `MYSQL_USE_SLAVE_CONNECTION` 変数 `true`. |
| `wizard:scd-on-demand` | 以下を確認します。 `SCD_ON_DEMAND` グローバル環境変数は次のとおりです。 `true`. |
| `wizard:scd-on-build` | 以下を確認します。 `SCD_ON_DEMAND` グローバル環境変数は次のとおりです。 `false` そして `SKIP_SCD` 環境変数： `false` （の） _ビルド_ ステージ。 を検証します。 `config.php` ファイルには、ストア、ストアグループ、および web サイトに関する情報が含まれます。 |
| `wizard:scd-on-deploy` | 以下を確認します。 `SCD_ON_DEMAND` グローバル環境変数は次のとおりです。 `false` そして `SKIP_SCD` 環境変数： `false` （の） _デプロイ_ ステージ。 を検証します。 `config.php` ファイル _NOT_ には、ストア、ストアグループ、Web サイトのリストと関連情報が含まれます。 |

例えば、設定で SCD オンデマンド機能が正しく有効になっていることを確認できます。

```bash
./vendor/bin/ece-tools wizard:scd-on-demand
```

設定が成功すると、次の値が返されます。

```terminal
SCD on-demand is enabled
```

失敗した設定は次のように返されます。

```terminal
SCD on-demand is disabled
```

## 理想的な設定の確認

The _理想的な_ クラウドプロジェクトを設定すると、キャッシュをウォーミングし、ユーザーの要求に応じて静的コンテンツを生成することで、デプロイメントのダウンタイムを最小限に抑えることができます。 このウィザードは、デプロイメントプロセス中に自動的に実行されます。 クラウドがこのために設定されていない場合 _理想状態_&#x200B;をクリックすると、次のようなメッセージが表示されます。

```terminal
- SCD on build is not configured
- Post-deploy hook is not configured
- Skip HTML minification is disabled

Ideal state is not configured
```

出力に基づいて、設定を次のように修正する必要があります。

1. 「スキップHTML縮小」変数を有効にします。

   > .magento.env.yaml

   ```yaml
   stage:
     global:
       SKIP_HTML_MINIFICATION: true
   ```

1. デプロイ後のフックを設定します。

   > .magento.app.yaml

   ```yaml
       post_deploy: |
           php ./vendor/bin/ece-tools post-deploy
   ```

1. コードの変更をプッシュして、もう一度テストを実行します。 設定が _理想的な_&#x200B;に設定すると、次のメッセージが表示されます。

   ```terminal
   Ideal state is configured
   ```
