---
title: アプリケーションのデプロイメントの設定
description: を制御するアプリケーション設定ファイルのプロパティを設定する方法を説明します [!DNL Commerce] アプリケーションは、クラウド環境にビルドおよびデプロイされます。
feature: Cloud, Configuration, Build, Deploy
exl-id: 900da20d-98d2-4c9f-97ec-578aee775b55
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '142'
ht-degree: 0%

---

# アプリケーションのデプロイメントの設定

この `.magento.app.yaml` ファイルは、アプリケーションのビルドおよびデプロイ方法を制御します。 クラウドインフラストラクチャー上のAdobe Commerceでは、プロジェクトごとに複数のアプリケーションをサポートしていますが、通常、プロジェクトには以下を持つ 1 つのアプリケーションがあります `.magento.app.yaml` リポジトリのルートにあるファイル。

この `.magento.app.yaml` には多くの既定値があります。を参照してください。 [サンプル `.magento.app.yaml` ファイル](https://github.com/magento/magento-cloud/blob/master/.magento.app.yaml). 常にをレビューする `.magento.app.yaml` （インストールされているバージョン用）。 このファイルは、クラウドインフラストラクチャー上のAdobe Commerceのバージョンによって異なる場合があります。

の使用 `.magento.app.yaml` ファイルを使用して、次の設定値を定義します。

- [プロパティ](properties.md)- アプリケーションインスタンスのプロパティ値を定義します。
- [Variables プロパティ](variables-property.md) – に必要な環境変数を確認する [!DNL Commerce] アプリケーションのバージョン。
- [PHP 設定](php-settings.md) – 実行時の PHP オプションを設定する
- [静的ファイルのキャッシュの設定](set-cache.md)- メディアおよび静的ファイルのキャッシュ TTL を設定します。
