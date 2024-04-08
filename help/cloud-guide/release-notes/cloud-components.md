---
title: コマース用のクラウドコンポーネント
description: クラウドコンポーネントパッケージの最新の改善点のリストを参照してください。
recommendations: noDisplay, catalog
exl-id: b4e2508a-3558-4fa8-bae0-3eb76c7b2775
source-git-commit: c02dfd2709cdc63ac1630edaa8c89cad5f737ea1
workflow-type: tm+mt
source-wordcount: '560'
ht-degree: 0%

---

# コマース用のクラウドコンポーネント

The [クラウドコンポーネント](https://github.com/magento/magento-cloud-components) パッケージは、クラウドインフラストラクチャにデプロイされたサイト向けの拡張Adobe Commerceコア機能を提供します。 このパッケージは、ECE-Tools パッケージの依存関係です。 これらのリリースノートでは、このパッケージの最新の改善点を説明します。改善点は、のコンポーネントです。 [Cloud Tools Suite for Commerce](cloud-tools-suite.md).

The `magento/magento-cloud-components` パッケージでは、次のバージョンシーケンスが使用されます。 `<major>.<minor>.<patch>`

リリースノートには次の内容が含まれます。

- ![新しいアイコン](../../assets/new.svg) 新機能
- ![修正アイコン](../../assets/fix.svg) 修正点および改善点

<!--Add release notes below-->

## v1.0.14 {#latest}

リリース日： 2024 年 4 月 8 日

- ![新しいアイコン](../../assets/new.svg) **PHP** — PHP 8.3 のサポートを追加しました。

## v1.0.13

リリース日： 2023 年 3 月 11 日

- ![新しいアイコン](../../assets/new.svg) **PHP 8.2 のサポートの強化** — 特定の PHP 8.2.x バージョンで Commerce 2.4.6 をサポートするための互換性の問題を修正しました。

## v1.0.12

リリース日： 2022 年 9 月 14 日

- ![修正アイコン](../../assets/fix.svg) **ウォームアップ時のエラー**— [暖かい](../environment/variables-post-deploy.md#warm_up_pages) ページの表示が [**個別に表示されない**](https://docs.magento.com/user-guide/system/data-attributes-product.html#simple-product-csv-file-structure) が管理者に返され、結果は `ERROR: Warming up failed: <link to page>` エラーがデプロイメントログに記録されました。<!-- MCLOUD-9134 -->

## v1.0.11

リリース日： 2022 年 8 月 5 日

- ![修正アイコン](../../assets/fix.svg) **Symfony 5.4 との互換性のサポートを追加**—Symfony 5.4 との互換性を修正しました。<!-- AC-3550 -->

## v1.0.10

リリース日： 2022 年 3 月 11 日

- ![新しいアイコン](../../assets/new.svg) **PHP 8.1 をサポート**—PHP 8.1 のサポートを追加し、PHP 7.1 のサポートを廃止しました。

## v1.0.9

リリース日： 2021 年 10 月 26 日

- ![修正アイコン](../../assets/fix.svg) **モノローグを更新** — に必要な最小バージョンを更新しました。 `monolog` ～にパッケージ化する `^2.3`.<!-- ACMP-1263 -->

## v1.0.8

リリース日： 2021 年 7 月 30 日

- ![修正アイコン](../../assets/fix.svg) **自動生成された URL から末尾のスラッシュを削除しました。** — キャッシュのウォームアップ中に生成されたカテゴリページ URL から末尾のスラッシュを削除しました。<!--MCLOUD-7192-->

## v1.0.7

リリース日： 2020 年 9 月 10 日

- ![新しいアイコン](../../assets/new.svg) **ログの改善** — サイズを縮小します。 `cache.log` ファイルを編集して、パフォーマンスを向上させます。<!--MCLOUD-6859-->

- ![修正アイコン](../../assets/fix.svg) キャッシュ設定値で、 `php bin/magento cache:evict` CLI コマンドが失敗する。

## v1.0.6

リリース日： 2020 年 8 月 6 日

- ![新しいアイコン](../../assets/new.svg) **Redis のパフォーマンスを向上** — が追加されました。 `./bin/magento cache:evict` コマンドを使用して期限切れの Redis キーを削除します。これにより、Redis のメモリ使用量が減り、パフォーマンスが向上します。<!--MCLOUD-6023-->

- ![修正アイコン](../../assets/fix.svg) のサポートを削除しました。 *New Relic Logs in Context* をクリックして、パフォーマンスの問題を修正します。<!--MCLOUD-6422-->

## v1.0.5

リリース日： 2020 年 6 月 26 日

- ![修正アイコン](../../assets/fix.svg) magento/magento-cloud-components バージョン 1.0.4 で発生した、デプロイフェーズ中にフラッシュキャッシュ操作が失敗し、デプロイメントプロセスが中断される問題を修正しました。

## v1.0.4

リリース日： 2020 年 6 月 26 日

- ![新しいアイコン](../../assets/new.svg) **コンテキストでのNew Relicログの実装**—Adobe Commerceで生成されたアプリケーションログがNew Relic内のトレースに表示され、トラブルシューティング機能が向上しました。<!--MCLOUD-6029-->

- ![新しいアイコン](../../assets/new.svg) **ログの改善** — キャッシュの無効化と完全な再インデックスイベントを追跡するログが追加されました。<!--MCLOUD-6157-->

## v1.0.3

リリース日： 2020 年 2 月 28 日

- ![修正アイコン](../../assets/fix.svg) サポートの互換性の問題を修正しました。 `ece-tools` 2002.0.x リリースでは、古い PHP バージョンを使用しています。

## v1.0.2

リリース日： 2020 年 2 月 7 日

- ![新しいアイコン](../../assets/new.svg) の機能を拡張しました。 `WARM_UP_PAGES` 環境変数を使用して、特定の製品ページのキャッシュのプリロードをサポートします。 詳しくは、 [デプロイ後の変数](../environment/variables-post-deploy.md#warm_up_pages) トピックを参照してください。<!--MAGECLOUD-4444-->

- ![修正アイコン](../../assets/fix.svg) 無効なストア URL が原因で、デプロイ後のフックが `WARM_UP_PAGES` 機能を使用してキャッシュを設定できます。 この問題は、URL の書き換えが無効な場合にのみ発生していました。<!-- MAGECLOUD-4094 -->

## v1.0.1

リリース日：2019 年 7 月 24 日

- ![修正アイコン](../../assets/fix.svg) 次の項目に影響する問題を修正しました： [**WARM_UP_PAGES**](../environment/variables-post-deploy.md#warm_up_pages) の機能です。デフォルトのストア URL を使用します。 ここで、 `config:show:default-url` コマンドはベース URL を取得できないので、MAGENTO_CLOUD_ROUTES 変数の URL が使用されます。<!-- MAGECLOUD-3866 -->

## v1.0.0

リリース日：2019 年 6 月 13 日

これは、 [`magento/magento-cloud-components`](https://github.com/magento/magento-cloud-components) パッケージ（新しい依存関係） `ece-tools` パッケージバージョン 2002.0.20 以降。

- ![新しいアイコン](../../assets/new.svg) 正規表現パターンを使用して **WARM_UP_PAGES** 環境変数を使用して、単一のページ、複数のドメインおよび複数のページをキャッシュします。 詳しくは、 [デプロイ後の変数](../environment/variables-post-deploy.md#warm_up_pages).<!--MAGECLOUD-3258-->
