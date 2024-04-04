---
title: 環境変数
description: クラウドインフラストラクチャ上のAdobe Commerceに固有の環境変数の一覧を参照してください。
feature: Cloud, Build, Configuration, Deploy
exl-id: bfee2f69-93a6-4d26-bb9e-be8acc5673c3
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '258'
ht-degree: 0%

---

# 環境変数

Adobe Commerce on cloud infrastructure を使用すると、環境変数を割り当てて、設定オプションを上書きできます。 The `ece-tools` パッケージは、 `env.php` 値に基づくファイル [クラウド変数](variables-cloud.md)、変数を [!DNL Cloud Console]、および `.magento.env.yaml` 設定ファイル。

の環境変数 `.magento.env.yaml` ファイル既存のコマース設定を上書きして、クラウド環境をカスタマイズします。 デフォルト値が `Not Set`を、 `ece-tools` パッケージテイク **いいえ** アクションと [!DNL Commerce] default または `MAGENTO_CLOUD_RELATIONSHIPS` 設定。 デフォルト値が設定されている場合、 `ece-tools` パッケージは、そのデフォルトを設定するために動作します。

環境変数のタイプは次のとおりです。

- [管理者](variables-admin.md) — 変数は、プロジェクト管理者変数を上書きします。
- [MAGENTO_CLOUD](variables-cloud.md) — クラウドインフラストラクチャ固有の変数
- で使用される変数 `.magento.env.yaml` ファイル：
   - [グローバル](variables-global.md) — 変数は、ビルド、デプロイ、デプロイ後のステージに影響します。
   - [ビルド](variables-build.md) — 変数はビルドアクションを制御します
   - [デプロイ](variables-deploy.md) — 変数はデプロイアクションを制御します
   - [デプロイ後](variables-post-deploy.md) — 変数がデプロイ後のアクションを制御します

変数は _階層_：変数が上書きされない場合、その変数は親環境から継承されます。

次の設定が可能です。 [管理変数](variables-admin.md) から [!DNL Cloud Console] またはAdobe Commerce CLI を使用して、 他の環境変数は、 [`.magento.env.yaml`](configure-env-yaml.md) ファイルを使用して、サポートチケットを必要とせずに、Pro Staging や Production など、あらゆる環境にわたってビルドとデプロイのアクションを管理できます。

>[!TIP]
>
>YAML ファイルでは大文字と小文字が区別され、タブは使用できません。 一貫したインデントを `.magento.env.yaml` ファイルまたは設定が期待どおりに動作しない可能性があります。 このドキュメントおよびサンプルファイルの例では、 _2 スペース_ インデントです。 以下を使用します。 [ece-tools validate コマンド](configure-env-yaml.md#validate-configuration-file) 設定を確認します。
