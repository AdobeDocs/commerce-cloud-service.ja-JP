---
title: アプリケーションのデプロイメントを設定
description: 次の方法を制御するアプリケーション設定ファイルでプロパティを設定する方法を説明します。 [!DNL Commerce] アプリケーションがビルドされ、クラウド環境にデプロイされます。
feature: Cloud, Configuration, Build, Deploy
exl-id: 900da20d-98d2-4c9f-97ec-578aee775b55
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '142'
ht-degree: 0%

---

# アプリケーションのデプロイメントを設定

The `.magento.app.yaml` ファイルは、アプリケーションがビルドおよびデプロイする方法を制御します。 クラウドインフラストラクチャ上のAdobe Commerceは、プロジェクトごとに複数のアプリケーションをサポートしますが、通常、プロジェクトには、 `.magento.app.yaml` ファイルをリポジトリのルートに配置します。

The `.magento.app.yaml` には多くのデフォルト値があります。詳しくは、 [見本 `.magento.app.yaml` ファイル](https://github.com/magento/magento-cloud/blob/master/.magento.app.yaml). 常に `.magento.app.yaml` を設定します。 このファイルは、クラウドインフラストラクチャ上のAdobe Commerceのバージョン間で異なる場合があります。

以下を使用します。 `.magento.app.yaml` ファイル：次の設定値を定義します。

- [プロパティ](properties.md) — アプリケーションインスタンスのプロパティ値を定義します。
- [Variables プロパティ](variables-property.md)— [!DNL Commerce] アプリケーションのバージョン。
- [PHP 設定](php-settings.md) — ランタイム PHP オプションを設定します。
- [静的ファイルのキャッシュを設定](set-cache.md) — メディアおよび静的ファイルのキャッシュ TTL を設定します。
