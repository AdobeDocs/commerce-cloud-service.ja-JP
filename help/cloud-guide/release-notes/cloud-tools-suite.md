---
title: Cloud Tools Suite のリリースノート
description: Adobe Commerceのクラウドツールスイートの最新の改善点について説明します。
feature: Cloud, Release Notes
exl-id: 6a652e93-46a2-4590-97fc-fb5d114ece9a
source-git-commit: 40c0d4ca6b70d7cea988209e7e3c8b7e3cd3522e
workflow-type: tm+mt
source-wordcount: '195'
ht-degree: 1%

---

# Commerce Cloudツールスイートのリリースノート

このリリース情報では、Adobe Commerceのインストールとクラウドプラットフォームでのアップグレードをデプロイおよび管理するために設計された、 Cloud Tools Suite for Commerce パッケージの最新の改善点を詳しく説明します。

| リリースノート | バージョン | 説明 | ソース |
| ----------------- |-----------| ---------------------------------------- | --------------------------- |
| [`ece-tools` パッケージ](ece-tools-package.md) | 2002.1.18 | クラウドプロジェクトを管理およびデプロイするために設計された一連のスクリプトおよびツール | [`magento/ece-tools`](https://github.com/magento/ece-tools/tree/2002.1) |
| [コマース用クラウドパッチ](cloud-patches.md) | 1.0.26 | すべてのAdobe Commerceバージョンとクラウド環境の統合を改善する一連のパッチです。 このパッケージには、Adobe Commerceパッチと、 `ece-tools` デプロイする | [`magento/magento-cloud-patches`](https://github.com/magento/magento-cloud-patches/tree/1.0.1) |
| [Cloud Docker for Commerce](cloud-docker.md) | 1.3.7 | Adobe Commerceをローカルクラウド環境にデプロイするための Docker イメージの機能と設定ファイル | [`magento/magento-cloud-docker`](https://github.com/magento/magento-cloud-docker/tree/1.0) |
| [コマースのクラウドコンポーネント](cloud-components.md) | 1.0.14 | クラウドインフラストラクチャにデプロイされたサイト向けのAdobe Commerceの拡張コア機能 | [`magento/magento-cloud-components`](https://github.com/magento/magento-cloud-components/tree/1.0.2) |

ECE-Tools 2002.1.0 以降に更新すると、他のパッケージの最新バージョンに自動的に更新されます。これは、 `ece-tools` パッケージ。 詳しくは、 [クラウドメタパッケージ](../development/overview.md#cloud-metapackage) ：依存関係のリスト。
