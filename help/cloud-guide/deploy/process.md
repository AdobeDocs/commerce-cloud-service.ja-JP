---
title: デプロイメントプロセス
description: クラウドインフラストラクチャプロジェクトでのAdobe Commerceのデプロイメントの仕組みについて説明します。
feature: Cloud, Build, Deploy, SCD
exl-id: 378fa290-5a71-4ac2-816a-a7c837e45b2f
source-git-commit: 3d9e3294872fedcc43744f54a71c71d8951ed853
workflow-type: tm+mt
source-wordcount: '396'
ht-degree: 0%

---

# デプロイメントプロセス

トリガー デプロイメントプロセスは、環境の結合、プッシュ、同期を実行するか、 [手動での再デプロイ](../dev-tools/cloud-cli-overview.md#redeploy-the-environment). デプロイメントプロセスには時間がかかりますが、開発とテストを行っているか、ライブサイトを使用しているかによって、デプロイメントを最適化する方法があります。 最も顕著なのは、 [静的コンテンツデプロイメント](static-content.md).

デプロイメントプロセスには、ビルド、デプロイ、ポストデプロイの 3 つの異なるフェーズがあります。 各フェーズでは、限られたリソースで特定のアクションが実行されます。

## ![ビルドフェーズ](../../assets/status-build.png) ビルドフェーズ

この _ビルド_ フェーズでは、設定ファイルで定義されたサービスのコンテナを組み立て、 `composer.lock` ファイルに書き込み、 `.magento.app.yaml` ファイル。 サービスに接続したりデータベースにアクセスしたりできない場合、ビルドフェーズは環境に限定されたリソースに応じて異なります。

## ![デプロイフェーズ](../../assets/status-deploy.png) デプロイフェーズ

この _deploy_ フェーズでは、受信リクエストを一時的に保持し、サイトをに移行します [メンテナンスモード](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/setup/application-modes.html). デプロイフェーズでは新しいコンテナを使用し、ファイルシステムのマウント後にネットワーク接続を開き、 `relationships` の節 `.magento.app.yaml` ファイルに書き込み、 `.magento.app.yaml` ファイル。 万事がうまくいっている _読み取り専用_&#x200B;で定義されたディレクトリを除く。 `.magento.app.yaml` ファイル。 デフォルトでは、 [`mounts` プロパティ](../application/properties.md#mounts) には、次のディレクトリが含まれます。

- `app/etc` – 次を含む `env.php` および `config.php` 設定ファイル
- `pub/media` – 製品やカテゴリなどのすべてのメディア データが含まれます
- `pub/static` – 生成された静的ファイルを含む
- `var` – 実行時に作成された一時ファイルが含まれます

その他のすべてのディレクトリには、読み取り専用の権限があります。 新しいサイトは、メンテナンスモードを終了し、受信リクエストの一時的な保留を解除すると、デプロイフェーズの最後にアクティブになります。

導入段階では、 `app/etc/config.php` および `app/etc/env.php` デプロイメント設定ファイルは、BAK 拡張子で保存されます。 参照： [ストアの設定](../store/store-settings.md#restore-configuration-files) を参照してください。

## ![デプロイ後フェーズ](../../assets/status-post-deploy.png) デプロイ後フェーズ

この _デプロイ後_ フェーズでは、 `.magento.app.yaml` ファイル。 このフェーズで何らかのアクションを実行すると、サイトのパフォーマンスに影響を与える可能性がありますが、 [WARM_UP_PAGES](../environment/variables-post-deploy.md#warmuppages) キャッシュに格納する環境変数。

## ![状態の検証](../../assets/status-verify.png) 設定の検証

プロジェクトの状態に最適な設定をテストするには、次を実行します [スマート・ウィザード](smart-wizards.md).

>[!NOTE]
>
>（を使用） `ece-tools` 2002.1.0 以降では、シナリオベースのデプロイメント機能を使用して、クラウドインフラストラクチャプロジェクト上のAdobe Commerceのビルド、デプロイ、デプロイ後のプロセスをカスタマイズできます。 参照： [シナリオベースのデプロイメント](scenario-based.md).
