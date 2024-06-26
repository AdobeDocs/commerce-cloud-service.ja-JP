---
title: スマート・ウィザード
description: スマートウィザードを使用して、クラウドインフラストラクチャプロジェクト上のAdobe Commerceがデプロイメントのベストプラクティスに従っているかどうかを評価する方法を説明します。
feature: Cloud, Build, Deploy, SCD
exl-id: eb79431c-8835-4ae4-b453-9c4932c5d5ac
source-git-commit: 225fba1acfd8b3ce4d7ce989c7851e7b0b218680
workflow-type: tm+mt
source-wordcount: '322'
ht-degree: 0%

---

# スマート・ウィザード

スマートウィザードを使用すると、クラウド設定がベストプラクティスに従っているかどうかを判断できます。 使用可能なウィザードは、次の構成に役立ちます。

- 導入のダウンタイムを最小限に抑える理想的な状態
- データベースと Redis のロードバランシング設定
- オンデマンド、ビルドステージ、デプロイメントステージの静的コンテンツデプロイメント（SCD）

各スマート・ウィザード・コマンドは、検証応答を提供し、該当する場合は、適切な構成を推奨します。

| コマンド | 説明 |
| ------- | ------------|
| `wizard:ideal-state` | SCD がオンになっていることを確認します _ビルド_ ステージ、 `SKIP_HTML_MINIFICATION` 変数は `true`、およびクラウド環境に設定された post_deploy フック。 ローカル開発環境では使用されません。 |
| `wizard:master-slave` | を確認します。 `REDIS_USE_SLAVE_CONNECTION` 変数と `MYSQL_USE_SLAVE_CONNECTION` 変数は `true`. |
| `wizard:scd-on-demand` | を確認します。 `SCD_ON_DEMAND` グローバル環境変数は `true`. |
| `wizard:scd-on-build` | を確認します。 `SCD_ON_DEMAND` グローバル環境変数は `false` および `SKIP_SCD` 環境変数は `false` の場合 _ビルド_ ステージ。 を実行して、 `config.php` ファイルには、ストア、ストアグループ、web サイトの情報が含まれています。 |
| `wizard:scd-on-deploy` | を確認します。 `SCD_ON_DEMAND` グローバル環境変数は `false` および `SKIP_SCD` 環境変数は `false` の場合 _deploy_ ステージ。 を実行して、 `config.php` ファイルの処理 _ではない_ ストア、ストアグループ、web サイトのリストと関連情報が含まれます。 |

例えば、お使いの設定で SCD オンデマンド機能が適切に有効になっていることを確認できます。

```bash
./vendor/bin/ece-tools wizard:scd-on-demand
```

設定が成功すると、次の値が返されます。

```terminal
SCD on-demand is enabled
```

失敗した設定は次を返します。

```terminal
SCD on-demand is disabled
```

## 理想的な設定の検証

この _理想_ クラウドプロジェクトの設定は、ユーザーから要求があったときにキャッシュをウォームアップし、静的コンテンツを生成することで、デプロイメントのダウンタイムを最小限に抑えるのに役立ちます。 このウィザードは、配置プロセス中に自動的に実行されます。 クラウドが設定されていない場合 _理想状態_&#x200B;すると、次のようなメッセージが表示されます。

```terminal
- SCD on build is not configured
- Post-deploy hook is not configured
- Skip HTML minification is disabled

Ideal state is not configured
```

出力に基づいて、設定に次の修正を加える必要があります。

1. 「HTMLをスキップ」縮小変数を有効にします。

   > .magento.env.yaml

   ```yaml
   stage:
     global:
       SKIP_HTML_MINIFICATION: true
   ```

1. デプロイ後フックを設定します。

   > .magento.app.yaml

   ```yaml
       post_deploy: |
           php ./vendor/bin/ece-tools post-deploy
   ```

1. コードの変更をプッシュし、テストを再度実行します。 設定が _理想_&#x200B;すると、次のメッセージが表示されます。

   ```terminal
   Ideal state is configured
   ```
