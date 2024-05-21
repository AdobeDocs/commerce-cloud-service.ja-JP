---
title: Cloud Tools Suite のリリースノート
description: Adobe Commerce用 Cloud Tools スイートの最新の改善点について説明します。
feature: Cloud, Release Notes
exl-id: 6a652e93-46a2-4590-97fc-fb5d114ece9a
source-git-commit: e04a9de8f0e31098f0cc2e47112f206c11a0e23b
workflow-type: tm+mt
source-wordcount: '195'
ht-degree: 1%

---

# Commerce Cloudツールスイートのリリースノート

このリリース情報では、Cloud Platform でのAdobe Commerceのインストールとアップグレードのデプロイと管理を目的としたCommerce パッケージの Cloud Tools Suite の最新の改善点について詳しく説明します。

| リリースノート | バージョン | 説明 | ソース |
| ----------------- |-----------| ---------------------------------------- | --------------------------- |
| [`ece-tools` package](ece-tools-package.md) | 2002.1.19 | クラウドプロジェクトの管理とデプロイを行うために設計された一連のスクリプトとツール | [`magento/ece-tools`](https://github.com/magento/ece-tools/tree/2002.1) |
| [Commerceのクラウドパッチ](cloud-patches.md) | 1.0.27 | すべてのAdobe Commerce バージョンとクラウド環境の統合を改善するパッチセット。 このパッケージには、の使用時に適用される、Adobe Commerceのパッチと使用可能なホットフィックスが含まれています `ece-tools` デプロイする | [`magento/magento-cloud-patches`](https://github.com/magento/magento-cloud-patches/tree/1.0.1) |
| [Cloud Docker for Commerce](cloud-docker.md) | 1.3.7 | Adobe Commerceをローカルクラウド環境にデプロイするための Docker イメージの機能および設定ファイル | [`magento/magento-cloud-docker`](https://github.com/magento/magento-cloud-docker/tree/1.0) |
| [Commerceのクラウドコンポーネント](cloud-components.md) | 1.0.14 | クラウドインフラストラクチャにデプロイされたサイト向けに拡張されたAdobe Commerce コア機能 | [`magento/magento-cloud-components`](https://github.com/magento/magento-cloud-components/tree/1.0.2) |

ECE-Tools 2002.1.0 以降にアップデートすると、の依存関係である他のパッケージの最新バージョンに自動的にアップデートされます。 `ece-tools` パッケージ。 参照： [クラウドメタパッケージ](../development/overview.md#cloud-metapackage) 依存関係のリスト。
