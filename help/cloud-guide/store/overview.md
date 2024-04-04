---
title: ストアオプションと設定管理の概要
description: クラウドインフラストラクチャ上のAdobe Commerceストアをカスタマイズします。
feature: Cloud, Configuration, Services
exl-id: 06d477e4-02de-4742-8495-541458400e93
source-git-commit: 8a0523f1714b6ea41887e99b5c31294cf5e5255e
workflow-type: tm+mt
source-wordcount: '191'
ht-degree: 0%

---

# ストアオプションと設定管理の概要

ストアをカスタマイズする方法は多数あります。例えば、カスタムテーマの追加、拡張機能のインストール、クラウドインフラストラクチャ環境全体での特定の設定の適用などです。 特定のサービスの設定は、ステージング環境と実稼動環境で直接おこなうことができます。 複数の Web サイトやストアを設定できます。 ストア設定を使用すると、ローカルワークステーションでこれらのオプションを設定し、環境をまたいで特定の設定をデプロイできます。

ストアフロントにアクセスするには、 `magento-cloud url` コマンドを入力し、プロンプトに答えます。 または、 [!DNL Cloud Console] under **サイトにアクセス**.

## ストアオプションの設定

ストアオプションは次のとおりです。

* [B2B(B2B)](b2b-module.md)
* [カスタムテーマ](custom-theme.md)
* [拡張機能](extensions.md)
* [複数のサイト](multiple-sites.md)
* [支払いサービス](paypal.md)

## サービスと統合の設定

特定の [設定ファイル](../environment/overview.md) リモート環境に対する特定のデプロイメント動作を管理する 次のトピックを個別に確認できます。

* [アプリケーションのデプロイ](../application/configure-app-yaml.md)
* [環境のビルドとデプロイアクション](../environment/configure-env-yaml.md)
* [受信リクエストルート](../routes/routes-yaml.md)
* [サポートされるサービス](../services/services-yaml.md)

## 設定の管理

ストアオプション、サービス、統合を設定したら、設定管理を使用して、ダウンタイムを最小限に抑えながら、すべての環境にわたって、これらの設定を一貫してデプロイします。 詳しくは、 [設定管理](store-settings.md).
