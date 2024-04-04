---
title: 開発の概要
description: クラウドインフラストラクチャ上でのAdobe Commerceとのローカル開発に備えます。
role: Developer
feature: Cloud, Install
topic: Development
last-substantial-update: 2024-02-06T00:00:00Z
exl-id: d4452d7d-d3dc-4f8d-8bd7-76f05d89f545
source-git-commit: abe9aa36b907be8bdfdf42e6f28f1e1eac68fecf
workflow-type: tm+mt
source-wordcount: '548'
ht-degree: 0%

---

# 開発の概要

クラウドインフラストラクチャのリモート環境のAdobe Commerce **読み取り専用**（すべてのスターター環境と、すべての Pro 統合、ステージング、実稼動環境を含む） ローカル開発環境では、コードを記述してテストしてから統合環境にプッシュし、ステージング環境と実稼動環境でのテストとデプロイメントをおこなうことができます。

ローカルワークスペースを準備する前に、 [資格情報](../../get-started/prepare-workspace.md). ローカル開発では、PHP および Composer のインストールが必要です（使用を選択しない場合）。 [Cloud Docker for Commerce](#docker-environment).

## 必要なパッケージ

Adobe Commerce on cloud infrastructure では、Composer を使用して、プロジェクトの依存関係とアップグレードを管理します。 ローカル開発の場合は、Cloud プロジェクトと互換性のある PHP および Composer バージョンをインストールする必要があります。 例えば、 [!DNL Commerce] 2.4.6 クラウドテンプレートの場合は、 [`.magento.app.yaml`](https://github.com/magento/magento-cloud/blob/2.4.6/.magento.app.yaml) 設定ファイルを使用 **PHP 8.2** および **Composer 2.2.21**.

プロジェクトに必要なライブラリと依存関係をにインストールします。 `vendor` ディレクトリ。 次の必須の Composer ファイルは、プロジェクトのルートディレクトリにあります。

- `composer.json`— `composer.json` ファイル：製品のインストールとアップグレードを管理します。
- `composer.lock`- `composer.lock` ファイルには、プロジェクトの依存ツリー内のすべてのパッケージに対するすべての要件のバージョン制約を満たす、正確なバージョン依存関係のセットが格納されます。

**一般的なコマンド：**

| コマンド | 説明 |
|--------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| `composer update` | 依存関係の最新バージョンを更新し、 `composer.json` ファイル。 これにより、 `composer.lock` ファイル。 |
| `composer install` | 次の項目を読み取ります。 `composer.lock` ファイルを使用して依存関係をダウンロードします。 最新のコピーを保持することをお勧めします。 `composer.lock` をプロジェクトリポジトリに追加します。 |

{style="table-layout:auto"}

更新されたコードを追加、コミット、およびプッシュすると、デプロイメントプロセスによって自動的に `composer install` コマンド [構築段階](../deploy/process.md#build-phase-build-phase).

### クラウドメタパッケージ

Adobe Commerce on cloud infrastructure は、 `magento/product-enterprise-edition`. 最新バージョンの Commerce の最新の更新を取得するには、次の制約構文を使用します。

```text
>=current_version <next_version
```

例えば、最新のAdobe Commerceバージョン 2.4.5 を使用するには、 `2.4.5` を「現在の」バージョンとして、および `2.4.6` を `composer.json` ファイル：

```text
"magento/magento-cloud-metapackage": ">=2.4.5 <2.4.6"
```

このメタパッケージの主なパッケージは次のとおりです。

- **vendor/magento/ece-tools**- `ece-tools` パッケージは、Adobe Commerceバージョン 2.1.4 以降と互換性があり、Adobe Commerce on cloud infrastructure プロジェクトの管理に使用できる豊富な機能セットを提供します。 コードの管理とプロジェクトの自動構築およびデプロイを支援するスクリプトおよびAdobe Commerce on cloud infrastructure コマンドが含まれています。 詳しくは、 [`ece-tools` パッケージの概要](../dev-tools/package-overview.md).
- **vendor/magento/product-enterprise-edition** — このメタパッケージには、モジュール、フレームワーク、テーマなどのアプリケーションコンポーネントが必要です。
- **vendor/fastly2/magento2** — このモジュールは、Pro ステージング環境および実稼動環境およびスターター実稼動環境用の Fastly CDN およびサービスを管理します。 詳しくは、 [Fastly サービス](/help/cloud-guide/cdn/fastly.md#fastly-cdn-module-for-magento-2).
- **vendor/magento/module-paypal-on-boarding** — このモジュールは、PayPal マーチャントアカウントに接続することにより、PayPal の支払いゲートウェイのチェックアウトを提供します。 詳しくは、 [PayPal オンボーディングツール](../store/paypal.md).

>[!TIP]
>
>詳しくは、 [Adobe Commerce用クラウドパッケージ](/help/cloud-guide/release-notes/cloud-packages.md) （内） _Commerce リリースノート_ 依存関係とサードパーティライセンスの一覧

## Docker 環境

Cloud Docker for Commerce ツールを使用して、クラウドインフラストラクチャの実稼動環境および開発環境上でのAdobe Commerceをローカル開発用にエミュレートできます。 Cloud Docker for Commerce では、PHP および Composer をローカルにインストールする必要はありません。

- [Cloud Docker を使用したローカル開発](https://developer.adobe.com/commerce/cloud-tools/docker/setup/) Adobe Developerサイト内
- [Docker のアーキテクチャと一般的なコマンド](../dev-tools/cloud-docker.md)
- [Cloud Docker リリースノート](../release-notes/cloud-docker.md)

>[!TIP]
>
>クラウドインフラストラクチャ上でAdobe Commerceを使用して Git ベースのホスティングサービスを使用する方法については、 [統合](../integrations/overview.md).
