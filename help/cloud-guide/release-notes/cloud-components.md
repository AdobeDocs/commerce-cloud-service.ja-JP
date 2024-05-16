---
title: Commerceのクラウドコンポーネント
description: クラウドコンポーネントパッケージの最新の改善点のリストを確認します。
recommendations: noDisplay, catalog
exl-id: b4e2508a-3558-4fa8-bae0-3eb76c7b2775
source-git-commit: c02dfd2709cdc63ac1630edaa8c89cad5f737ea1
workflow-type: tm+mt
source-wordcount: '560'
ht-degree: 0%

---

# Commerceのクラウドコンポーネント

この [クラウドコンポーネント](https://github.com/magento/magento-cloud-components) パッケージは、クラウドインフラストラクチャにデプロイされたサイトに対して、拡張されたAdobe Commerce コア機能を提供します。 このパッケージは、ECE-Tools パッケージの依存関係です。 これらのリリースノートでは、のコンポーネントであるこのパッケージの最新の改善点について説明します [Commerce用 Cloud Tools スイート](cloud-tools-suite.md).

この `magento/magento-cloud-components` パッケージでは、次のバージョンシーケンスを使用します。 `<major>.<minor>.<patch>`

リリースノートには次のものが含まれます。

- ![新規アイコン](../../assets/new.svg) 新機能
- ![修正アイコン](../../assets/fix.svg) 修正点および改善点

<!--Add release notes below-->

## v1.0.14 {#latest}

リリース日：2024 年 4 月 8 日（PT）

- ![新規アイコン](../../assets/new.svg) **PHP** — PHP 8.3 のサポートを追加。

## v1.0.13

リリース日：2023 年 3 月 10 日（PT）

- ![新規アイコン](../../assets/new.svg) **PHP 8.2 のサポートの強化**—Commerce 2.4.6 をサポートするために、特定の PHP 8.2.x バージョンの互換性の問題を修正しました。

## v1.0.12

リリース日：2022 年 9 月 13 日（PT）

- ![修正アイコン](../../assets/fix.svg) **ウォームアップ時のエラー** – 実行しようとした問題を修正しました。 [ウォームアップ](../environment/variables-post-deploy.md#warm_up_pages) ページ表示がに設定されている場合 [**個別に表示されない**](https://docs.magento.com/user-guide/system/data-attributes-product.html#simple-product-csv-file-structure) 管理画面で、次のように表示されます `ERROR: Warming up failed: <link to page>` デプロイメントログのエラー。<!-- MCLOUD-9134 -->

## v1.0.11

リリース日：2022 年 8 月 4 日（PT）

- ![修正アイコン](../../assets/fix.svg) **Symfony 5.4 互換性のサポートを追加**—Symfony 5.4 との互換性を修正。<!-- AC-3550 -->

## v1.0.10

リリース日：2022 年 3 月 10 日（PT）

- ![新規アイコン](../../assets/new.svg) **PHP 8.1 のサポート**—PHP 8.1 のサポートを追加し、PHP 7.1 のサポートを終了しました。

## v1.0.9

リリース日：2021 年 10 月 25 日（PT）

- ![修正アイコン](../../assets/fix.svg) **モノログの更新** – に必要な最小バージョンを更新しました。 `monolog` パッケージ先 `^2.3`.<!-- ACMP-1263 -->

## v1.0.8

リリース日：2021 年 7 月 29 日（PT）

- ![修正アイコン](../../assets/fix.svg) **自動生成された URL から末尾のスラッシュを削除しました**- キャッシュのウォームアップ中に生成されたカテゴリページ URL から、末尾のスラッシュを削除しました。<!--MCLOUD-7192-->

## v1.0.7

リリース日：2020 年 9 月 9 日（PT）

- ![新規アイコン](../../assets/new.svg) **ログの改善** – のサイズを縮小する `cache.log` ファイルを作成して、パフォーマンスを向上させます。<!--MCLOUD-6859-->

- ![修正アイコン](../../assets/fix.svg) キャッシュ設定値にタイプ エラーがあり、 `php bin/magento cache:evict` 失敗する CLI コマンド。

## v1.0.6

リリース日：2020 年 8 月 5 日（PT）

- ![新規アイコン](../../assets/new.svg) **Redis パフォーマンスの向上** – が追加されました `./bin/magento cache:evict` 期限切れの Redis キーを削除するコマンド。これにより、Redis メモリの使用量が減り、パフォーマンスが向上します。<!--MCLOUD-6023-->

- ![修正アイコン](../../assets/fix.svg) のサポートを削除 *コンテキスト内のNew Relic ログ* パフォーマンスの問題を修正する。<!--MCLOUD-6422-->

## v1.0.5

リリース日：2020 年 6 月 25 日（PT）

- ![修正アイコン](../../assets/fix.svg) magento/magento-cloud-components バージョン 1.0.4 で発生した、デプロイフェーズ中にフラッシュキャッシュ操作が失敗し、デプロイメントプロセスが中断される問題を修正しました。

## v1.0.4

リリース日：2020 年 6 月 25 日（PT）

- ![新規アイコン](../../assets/new.svg) **コンテキストへのNew Relic ログの実装**- Adobe Commerceで生成されたアプリケーションログがNew Relic内のトレースで表示されるようになり、トラブルシューティング機能が向上しました。<!--MCLOUD-6029-->

- ![新規アイコン](../../assets/new.svg) **ログの改善**- キャッシュの無効化と完全な再インデックスイベントを追跡するログを追加しました。<!--MCLOUD-6157-->

## v1.0.3

リリース日：2020 年 2 月 27 日（PT）

- ![修正アイコン](../../assets/fix.svg) サポートする互換性の問題を修正しました `ece-tools` 古い PHP バージョンを使用する 2002.0.x リリース。

## v1.0.2

リリース日：2020 年 2 月 6 日（PT）

- ![新規アイコン](../../assets/new.svg) の機能を拡張しました `WARM_UP_PAGES` 特定の製品ページのキャッシュのプリロードをサポートする環境変数。 を参照してください。 [デプロイ後変数](../environment/variables-post-deploy.md#warm_up_pages) 詳細な機能の説明に関するトピック。<!--MAGECLOUD-4444-->

- ![修正アイコン](../../assets/fix.svg) を使用する際に、無効なストア URL が原因でデプロイ後フックが失敗する問題を修正しました `WARM_UP_PAGES` キャッシュへのデータ入力機能。 この問題は、URL の書き換えが無効になっている場合にのみ発生していました。<!-- MAGECLOUD-4094 -->

## v1.0.1

リリース日：2019 年 7 月 23 日（PT）

- ![修正アイコン](../../assets/fix.svg) に影響する問題を修正しました [**WARM_UP_PAGES**](../environment/variables-post-deploy.md#warm_up_pages) デフォルトストア URL を使用する機能。 ここで、 `config:show:default-url` コマンドでベース URL を取得できない場合は、MAGENTO_CLOUD_ROUTES 変数の URL が使用されます。<!-- MAGECLOUD-3866 -->

## v1.0.0

リリース日：2019 年 6 月 12 日

これは、の最初のリリースです [`magento/magento-cloud-components`](https://github.com/magento/magento-cloud-components) 新しい依存関係であるパッケージ `ece-tools` パッケージバージョン 2002.0.20 以降。

- ![新規アイコン](../../assets/new.svg) 正規表現パターンを使用してを設定する機能を追加しました **WARM_UP_PAGES** 単一ページ、複数ドメイン、複数ページをキャッシュする環境変数。 参照： [デプロイ後変数](../environment/variables-post-deploy.md#warm_up_pages).<!--MAGECLOUD-3258-->
