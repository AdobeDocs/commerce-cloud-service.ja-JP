---
title: 環境変数
description: クラウドインフラストラクチャー上のAdobe Commerceに固有の環境変数のリストを参照してください。
feature: Cloud, Build, Configuration, Deploy
exl-id: bfee2f69-93a6-4d26-bb9e-be8acc5673c3
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '258'
ht-degree: 0%

---

# 環境変数

クラウドインフラストラクチャー上のAdobe Commerceを使用すると、設定オプションを上書きする環境変数を割り当てることができます。 この `ece-tools` パッケージはに値を設定します `env.php` の値に基づいたファイル [クラウド変数](variables-cloud.md)、で設定された変数 [!DNL Cloud Console]、および `.magento.env.yaml` 設定ファイル。

の環境変数 `.magento.env.yaml` ファイルは、既存のCommerce設定を上書きしてクラウド環境をカスタマイズします。 デフォルト値がの場合 `Not Set`を選択し、続いて `ece-tools` パッケージテイク **不可** アクションとの使用 [!DNL Commerce] デフォルト、またはの値 `MAGENTO_CLOUD_RELATIONSHIPS` 設定。 デフォルト値が設定されている場合は、 `ece-tools` パッケージは、そのデフォルトを設定するように動作します。

環境変数のタイプには、次のものが含まれます。

- [ADMIN](variables-admin.md) – 変数はプロジェクトの ADMIN 変数を上書きする
- [MAGENTO_クラウド](variables-cloud.md) – クラウドインフラストラクチャに固有の変数
- で使用される変数 `.magento.env.yaml` ファイル：
   - [グローバル](variables-global.md) – 変数は、ビルド、デプロイ、デプロイ後のステージに影響を与える
   - [ビルド](variables-build.md) – 変数は構築アクションを制御する
   - [デプロイ](variables-deploy.md) – 変数は展開アクションを制御する
   - [デプロイ後](variables-post-deploy.md) – 変数はデプロイ後のアクションを制御する

変数は _階層_&#x200B;つまり、変数が上書きされない場合は、親環境から継承されます。

次を設定できます [管理変数](variables-admin.md) から [!DNL Cloud Console] またはAdobe Commerce CLI を使用します。 その他の環境変数は、以下から管理できます [`.magento.env.yaml`](configure-env-yaml.md) ファイル：ステージング環境と実稼動環境を含むすべての環境で、サポートチケットを必要とせずにビルドおよびデプロイアクションを管理します。

>[!TIP]
>
>YAML ファイルでは大文字と小文字が区別され、タブは使用できません。 全体で一貫したインデントを使用するように注意してください `.magento.env.yaml` ファイルまたは設定が期待どおりに動作しない可能性があります。 このドキュメントとサンプルファイルの例では、次のことを使用します _2 つのスペース_ インデント。 の使用 [ece-tools validate コマンド](configure-env-yaml.md#validate-configuration-file) をクリックして、設定を確認します。
