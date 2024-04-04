---
title: デプロイメントプロセス
description: クラウドインフラストラクチャプロジェクトでのAdobe Commerceに対するデプロイメントの仕組みについて説明します。
feature: Cloud, Build, Deploy, SCD
exl-id: 378fa290-5a71-4ac2-816a-a7c837e45b2f
source-git-commit: 3d9e3294872fedcc43744f54a71c71d8951ed853
workflow-type: tm+mt
source-wordcount: '396'
ht-degree: 0%

---

# デプロイメントプロセス

デプロイメントプロセスは、環境の結合、プッシュまたは同期を実行するとき、または環境をトリガーしたときに開始します [手動再導入](../dev-tools/cloud-cli-overview.md#redeploy-the-environment). デプロイメントプロセスには時間がかかりますが、実稼働サイトを開発およびテストしているか、使用しているかに応じて、デプロイメントを最適化する方法があります。 特に、 [静的コンテンツのデプロイメント](static-content.md).

デプロイメントプロセスには、ビルド、デプロイ、デプロイ後の 3 つのフェーズがあります。 各フェーズでは、限られたリソースで特定のアクションを実行します。

## ![ビルドフェーズ](../../assets/status-build.png) ビルドフェーズ

The _ビルド_ フェーズは、設定ファイルで定義されたサービスのコンテナを構築し、に基づいて依存関係をインストールします。 `composer.lock` ファイルを作成し、 `.magento.app.yaml` ファイル。 どのサービスにも接続したり、データベースにアクセスしたりできない場合、ビルドフェーズは環境に限定されたリソースに依存します。

## ![導入段階](../../assets/status-deploy.png) 導入段階

The _デプロイ_ フェーズは、受信する要求を一時的に保留し、サイトをに移行します。 [メンテナンスモード](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/setup/application-modes.html). 導入フェーズでは、新しいコンテナを使用し、ファイル・システムをマウントした後、ネットワーク接続を開き、 `relationships` のセクション `.magento.app.yaml` ファイルを作成し、 `.magento.app.yaml` ファイル。 すべてが _読み取り専用_（で定義されたディレクトリを除く） `.magento.app.yaml` ファイル。 デフォルトでは、 [`mounts` プロパティ](../application/properties.md#mounts) には、次のディレクトリが含まれます。

- `app/etc` — に含まれます。 `env.php` および `config.php` 設定ファイル
- `pub/media` — 製品やカテゴリなど、すべてのメディアデータが含まれます。
- `pub/static` — 生成された静的ファイルを含みます
- `var` — 実行時に作成された一時ファイルを含みます

その他のディレクトリには、読み取り専用の権限があります。 新しいサイトは、メンテナンスモードから移行し、受信リクエストの一時的な保留が解除されると、デプロイフェーズの終わりにアクティブになります。

デプロイフェーズでは、 `app/etc/config.php` および `app/etc/env.php` デプロイメント設定ファイルは、BAK 拡張子と共に保存されます。 詳しくは、 [ストア設定](../store/store-settings.md#restore-configuration-files) を参照してください。

## ![デプロイ後の段階](../../assets/status-post-deploy.png) デプロイ後の段階

The _デプロイ後_ フェーズでは、 `.magento.app.yaml` ファイル。 このフェーズで任意のアクションを実行すると、サイトのパフォーマンスに影響を与える可能性がありますが、 [WARM_UP_PAGES](../environment/variables-post-deploy.md#warmuppages) 環境変数を使用して、キャッシュを設定します。

## ![状態の検証](../../assets/status-verify.png) 設定を検証

プロジェクトの状態に最適な設定をテストするには、 [スマートウィザード](smart-wizards.md).

>[!NOTE]
>
>を使用 `ece-tools` 2002.1.0 以降では、シナリオベースのデプロイメント機能を使用して、Adobe Commerce on cloud infrastructure プロジェクトのビルド、デプロイ、デプロイ後のプロセスをカスタマイズできます。 詳しくは、 [シナリオベースのデプロイメント](scenario-based.md).
