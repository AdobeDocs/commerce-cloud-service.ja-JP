---
title: 開発の概要
description: クラウドインフラストラクチャプロジェクト上のAdobe Commerceとのローカル開発の準備。
role: Developer
feature: Cloud, Install
topic: Development
last-substantial-update: 2024-02-06T00:00:00Z
exl-id: d4452d7d-d3dc-4f8d-8bd7-76f05d89f545
source-git-commit: 99272d08a11f850a79e8e24857b7072d1946f374
workflow-type: tm+mt
source-wordcount: '548'
ht-degree: 0%

---

# 開発の概要

クラウドインフラストラクチャー上のAdobe Commerce リモート環境は次のとおりです **読み取り専用**（すべてのスターター環境とすべての Pro 統合、ステージングおよび実稼動環境を含む）。 ローカル開発環境では、コードを記述してテストしてから統合環境にプッシュし、ステージング環境と実稼動環境へのさらなるテストとデプロイメントを行うことができます。

ローカルワークスペースを準備する前に、次のものが用意されていることを確認します [資格情報](../../get-started/prepare-workspace.md). を使用しない場合、ローカル開発には PHP および Composer のインストールが必要です。 [Cloud Docker for Commerce](#docker-environment).

## 必須パッケージ

クラウドインフラストラクチャー上のAdobe Commerceでは、Composer を使用してプロジェクトの依存関係やアップグレードを管理します。 ローカル開発を保つには、Cloud プロジェクトと互換性のある PHP および Composer バージョンをインストールする必要があります。 例えば、 [!DNL Commerce] 2.4.7 クラウドテンプレートの場合、 [`.magento.app.yaml`](https://github.com/magento/magento-cloud/blob/2.4.7/.magento.app.yaml) 設定ファイルで使用されるもの **PHP 8.3** および **Composer 2.7.2**.

Composer は、プロジェクトに必要なライブラリおよび依存関係をにインストールします。 `vendor` ディレクトリ。 次の必須の Composer ファイルは、プロジェクトのルート ディレクトリにあります。

- `composer.json` – を使用する `composer.json` 製品のインストールとアップグレードを管理するためのファイル。
- `composer.lock` – この `composer.lock` ファイルには、プロジェクトの依存関係ツリー内のすべてのパッケージについて、すべての要件のバージョン制約を満たす正確なバージョン依存関係のセットが格納されます。

**共通コマンド：**

| コマンド | 説明 |
|--------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| `composer update` | に反映された依存関係の最新バージョンへの更新 `composer.json` ファイル。 これにより、 `composer.lock` ファイル。 |
| `composer install` | を読み取ります。 `composer.lock` 依存関係をダウンロードするファイル。 の最新のコピーを保持することがベストプラクティスです。 `composer.lock` プロジェクトのリポジトリで作成します。 |

{style="table-layout:auto"}

更新されたコードを追加、コミット、プッシュすると、デプロイメントプロセスによって自動的に `composer install` 実行中のコマンド [ビルドフェーズ](../deploy/process.md#build-phase-build-phase).

### クラウドメタパッケージ

クラウドインフラストラクチャー上のAdobe Commerceは、次を必要とするメタパッケージを使用します `magento/product-enterprise-edition`. 最新バージョンのCommerceの最新のアップデートを取得するには、次の制約構文を使用します。

```text
>=current_version <next_version
```

例えば、最新バージョンのAdobe Commerce 2.4.7 を使用するには、次のように設定します `2.4.7` 「現在の」バージョンとして、および `2.4.8` を「次へ」バージョンとして設定する `composer.json` ファイル：

```text
"magento/magento-cloud-metapackage": ">=2.4.7 <2.4.8"
```

このメタパッケージの主なパッケージは以下の通りです。

- **vendor/magento/ece-tools** – この `ece-tools` パッケージはAdobe Commerce バージョン 2.1.4 以降と互換性があり、クラウドインフラストラクチャプロジェクトでのAdobe Commerceの管理に使用できる豊富な機能を提供します。 コードの管理とプロジェクトの自動ビルドおよびデプロイに役立つスクリプトやAdobe Commerce on cloud infrastructure コマンドが含まれています。 を参照してください。 [`ece-tools` パッケージの概要](../dev-tools/package-overview.md).
- **vendor/magento/product-enterprise-edition** – このメタパッケージには、モジュール、フレームワーク、テーマなど、アプリケーションコンポーネントが必要です。
- **vendor/fastly2/magento2** – このモジュールは、Pro ステージング環境および実稼動環境とスターター実稼動環境用の Fastly CDN とサービスを管理します。 参照： [Fastly サービス](/help/cloud-guide/cdn/fastly.md#fastly-cdn-module-for-magento-2).
- **vendor/magento/module-paypal-on-boarding** – このモジュールは、PayPal マーチャントアカウントに接続することにより、PayPal 支払いゲートウェイチェックアウトを提供します。 参照： [PayPal オンボーディングツール](../store/paypal.md).

>[!TIP]
>
>参照： [Adobe Commerce用のクラウドパッケージ](/help/cloud-guide/release-notes/cloud-packages.md) が含まれる _Commerce リリースノート_ 依存関係とサードパーティライセンスのリストの参照。

## Docker 環境

Cloud Docker for Commerce ツールを使用して、クラウドインフラストラクチャ上のAdobe Commerceの実稼動環境およびローカル開発環境をエミュレートできます。 Cloud Docker for Commerceでは、PHP と Composer をローカルにインストールする必要はありません。

- [Cloud Docker とのローカル開発](https://developer.adobe.com/commerce/cloud-tools/docker/setup/) Adobe Developer サイト内
- [Docker アーキテクチャと一般的なコマンド](../dev-tools/cloud-docker.md)
- [Cloud Docker リリースノート](../release-notes/cloud-docker.md)

>[!TIP]
>
>Cloud Infrastructure 上のAdobe Commerceで Git ベースのホスティングサービスを使用する方法については、を参照してください。 [統合](../integrations/overview.md).
