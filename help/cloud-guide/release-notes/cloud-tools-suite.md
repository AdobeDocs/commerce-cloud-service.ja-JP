---
title: Cloud Tools Suite のリリースノート
description: Adobe Commerce用 Cloud Tools スイートの最新の改善点について説明します。
feature: Cloud, Release Notes
exl-id: 6a652e93-46a2-4590-97fc-fb5d114ece9a
source-git-commit: f294370379ec2216ec32ccc606b77453211aa50a
workflow-type: tm+mt
source-wordcount: '230'
ht-degree: 1%

---

# Commerce Cloudツールスイートのリリースノート

このリリース情報では、Cloud Platform でのAdobe Commerceのインストールとアップグレードのデプロイと管理を目的としたCommerce パッケージの Cloud Tools Suite の最新の改善点について詳しく説明します。

| リリースノート | バージョン | 説明 | Source |
| ----------------- |-----------| ---------------------------------------- | --------------------------- |
| パッケ [`ece-tools` ジ ](ece-tools-package.md) | 2002.2.0 | クラウドプロジェクトの管理とデプロイを行うために設計された一連のスクリプトとツール | [`magento/ece-tools`](https://github.com/magento/ece-tools/tree/2002.2.0) |
| [Commerceのクラウドパッチ ](cloud-patches.md) | 1.1.0 | すべてのAdobe Commerce バージョンとクラウド環境の統合を改善するパッチセット。 このパッケージには、Adobe Commerceのパッチと、`ece-tools` を使用してデプロイする際に適用される使用可能なホットフィックスが含まれています | [`magento/magento-cloud-patches`](https://github.com/magento/magento-cloud-patches/tree/1.1.0) |
| [Commerce用の Cloud Docker](cloud-docker.md) | 1.4.0 | Adobe Commerceをローカルクラウド環境にデプロイするための Docker イメージの機能および設定ファイル | [`magento/magento-cloud-docker`](https://github.com/magento/magento-cloud-docker/tree/1.0) |
| [Commerceのクラウドコンポーネント ](cloud-components.md) | 1.1.0 | クラウドインフラストラクチャにデプロイされたサイト向けに拡張されたAdobe Commerce コア機能 | [`magento/magento-cloud-components`](https://github.com/magento/magento-cloud-components/tree/1.1.0) |

ECE-Tools 2002.1.0 以降に更新すると、`ece-tools` パッケージの依存関係である他のパッケージの最新バージョンに自動的に更新されます。 依存関係のリストについては、[ クラウドメタパッケージ ](../development/overview.md#cloud-metapackage) を参照してください。

`ece-tools` の新しいバージョン （2002.2.0）は、PHP バージョン 8.1 以降（8.2, 8.3）でのみ利用可能です。 古いバージョンの PHP は非推奨（7.4, 7.3, 7.2）となりました。 以前のバージョンの `ece-tools` を古いバージョンの PHP で使用できます。
