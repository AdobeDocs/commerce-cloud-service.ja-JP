---
title: Commerceのクラウドパッチ
description: クラウドパッチパッケージの最新の改善点のリストを確認します。
recommendations: noDisplay, catalog
last-substantial-update: 2024-05-21T00:00:00Z
exl-id: ae6b511b-a37d-4776-9a5e-ad7d9f9f6611
source-git-commit: 61c42a1bd1d5a28f90b8756032ee6f45be4565b2
workflow-type: tm+mt
source-wordcount: '2208'
ht-degree: 0%

---

# Commerceのクラウドパッチ

この [クラウドパッチ](https://github.com/magento/magento-cloud-patches) パッケージには、すべてのAdobe Commerce バージョンとクラウド環境の統合を改善し、重要な修正の迅速な配信をサポートする必須パッチのセットが用意されています。

Cloud Patches for Commerce パッケージは、ECE-Tools パッケージの依存関係であり、ECE-Tools パッケージをインストールまたは更新するとインストールおよび更新されます。 また、Commerce as a スタンドアロンパッケージとしてクラウドパッチを使用および管理して、Cloud Platform 上にないAdobe Commerce プロジェクトにパッチを適用することもできます。 これらのリリースノートでは、このパッケージの最新の改善点について説明します。

>[!TIP]
>
>プロジェクトに必要なパッチがすべて含まれていることを確認するには、 [ece-tools の最新バージョン](../dev-tools/update-package.md).

>[!NOTE]
>
>参照： [パッチの適用](../development/apply-patches.md) プロジェクトにパッチを適用する手順については、を参照してください。

この `magento/magento-cloud-patches` パッケージでは、次のバージョンシーケンスを使用します。 `<major>.<minor>.<patch>`

<!--Add release notes below-->

## v1.0.27 {#latest}

リリース日：2024 年 5 月 21 日（PT）

- **PHP 8.3 のサポート** – このパッチは、php 8.3 と composer パッケージバージョン間の互換性エラーを解決します。

## v1.0.26

リリース日：2024 年 4 月 8 日（PT）

- ![新規アイコン](../../assets/new.svg) **PHP** — PHP 8.3 のサポートを追加。

## v1.0.25

リリース日：2024 年 1 月 16 日（PT）

- **キャッシュの改善** – このパッチは、Adobe Commerce バージョン 2.4.4 以降で、レイアウトキャッシュの効率を高め、メモリ使用量を大幅に削減します。<!-- MCLOUD-11514 -->
- **CRON ジョブの改善** – このパッチは、Adobe Commerce バージョン 2.4.4 以降で、失敗したジョブが cron ジョブロックを不必要に待つ問題を修正します。<!-- MCLOUD-11329 -->

## v1.0.24

リリース日：2023 年 9 月 15 日（PT）

- **パフォーマンスの向上** – このパッチは、Adobe Commerce 2.4.6 と同じデプロイメント設定の読み込み回数を 2.4.6-p1 に減らすことで、パフォーマンスに影響を与える問題を修正します。<!-- MCLOUD-10604 -->

## v1.0.23

リリース日：2023 年 7 月 31 日（PT）

- **パッチ MCLOUD-10604 を削除。** – このパッチは QPT に移動されました。<!-- MCLOUD-10736 -->

## v1.0.22

リリース日：2023 年 6 月 19 日（PT）

- **拡張 QPT CLI ウィザード/出力**—QPT CLI ウィザード/出力に、パッチの詳細と依存関係がある場合の要件を確認するように促す警告を追加しました。<!-- ACP2E-1963 -->
- **Commerce 2.4.6 用のパッチを追加しました。**
   - を修正 `regexp cache tag` 検証。<!-- MCLOUD-10226 -->
   - 同じデプロイメント設定の読み込み回数を減らすことにより、パフォーマンスを向上しました。<!-- MCLOUD-10604 -->
- **Commerce 2.3.7 から 2.4.6 へのパッチの追加** – の 1 による増分ではなく、ランダムな値による増分が発生する問題を修正しました `catalog_product_entity_*` テーブル。<!-- MCLOUD-10032 -->
- **Commerce 2.4.0 から 2.4.6 へのパッチの追加** – というエラーを修正しました。 `The file can't be deleted. Warning!unlink: No such file or directory`これは、管理者から JS/CSS キャッシュをフラッシュする際に発生しました。<!-- MCLOUD-10279 -->

## v1.0.21

リリース日：2023 年 3 月 10 日（PT）

- **PHP 8.2 のサポートの強化**—Commerce 2.4.6 をサポートするために、特定の PHP 8.2.x バージョンの互換性の問題を修正しました。

## v1.0.20

リリース日：2022 年 10 月 27 日（PT）

- **L2 キャッシュ改善パッチを追加** – このパッチは、Commerce バージョン 2.4.0 および 2.4.1 のローカル L2 キャッシュをフラッシュする際の問題を修正します。<!-- MCLOUD-7845 -->

## v1.0.19

リリース日：2022 年 9 月 13 日（PT）

- **PHP 8.1 のサポートの強化** – 特定の PHP 8.1.x バージョンとの互換性の問題を修正しました。

## v1.0.18

リリース日：2022 年 8 月 11 日（PT）

Adobe Commerce 2.4.5 の重要なパッチ：

- **Braintree支払を使用した注文の問題** – このパッチは、管理者が新しい注文や再注文を行うのを妨げる重要な問題を解決します。<!-- MCLOUD-9137 -->

参照： [Braintree支払いが有効な場合、管理者が注文の作成/並べ替えを行うことはできません](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/known-issues-patches-attached/admin-cant-create-order-reorder-when-braintree-payment-enabled.html).

## v1.0.17

リリース日：2022 年 5 月 24 日（PT）

のセキュリティパッチに対する制約を修正しました。 `patches.json` ファイル。

## v1.0.16

リリース日：2022 年 3 月 31 日（PT）

Adobe Commerce 2.3.3-p1 以降のバージョン用の重要なパッチ：

を解決するためにパッチを更新しました **重大** 認証されていないリモートコードが実行される脆弱性。<!-- MCLOUD-8479 -->

参照： [Adobeセキュリティ速報 APSB22-12](https://helpx.adobe.com/security/products/magento/apsb22-12.html).

## v1.0.15

リリース日：2022 年 3 月 10 日（PT）

- **PHP 8.1 のサポート**—PHP 8.1 のサポートを追加し、PHP 7.0 および 7.1 のサポートを終了しました。
- **Adobe Commerce 2.3.3 にパッチを追加しました。** – 製品ページに表示される固定通貨。

## v1.0.14

リリース日：2022 年 2 月 13 日（PT）

Adobe Commerce 2.3.3-p1 以降のバージョン用の重要なパッチ：

を解決するためのパッチを追加しました。 **重大** 認証されていないリモートコードが実行される脆弱性。<!-- MCLOUD-8461 -->

参照： [Adobeセキュリティ速報 APSB22-12](https://helpx.adobe.com/security/products/magento/apsb22-12.html).

## v1.0.13

リリース日：2021 年 10 月 25 日（PT）

- **モノログの更新** – に必要な最小バージョンを更新しました。 `monolog` パッケージ先 `^2.3`.<!-- ACMP-1263 -->
- **互換性のない PHP メソッド**—Adobe Commerce 2.4.3 および 2.3.7-p1 用の互換性のない PHP メソッドを修正。<!-- AC-384 -->
- **PHP エラー** – を修正しました。 `PHP error 'Undefined variable: errorMessage' ...` パッチの適用中にエラーが発生しました。<!-- ACP2E-138 -->

## v1.0.12

リリース日：2021 年 8 月 12 日（PT）

Adobe Commerce 2.4.3 および 2.3.7-p1 の重要なパッチ：

- **API レート制限の問題** – このパッチは、配列に 20 個を超える項目を含むリクエストを Web API が処理できなかったデフォルトのレート制限を修正します。 このパッチは、レート制限のデフォルト値を引き上げます。 Adobe Commerceを参照 [2.4.3 リリースノート](https://devdocs.magento.com/guides/v2.4/release-notes/commerce-2-4-3.html#apply-mc-43048__set_rate_limits__243patch-to-address-issue-with-api-rate-limiting) および [2.3.7 リリースノート](https://devdocs.magento.com/guides/v2.3/release-notes/2-3-7-p1.html#apply-mc-43048__set_rate_limits__237-p1patch-to-address-issue-with-api-rate-limiting).<!-- MC-43048 -->

## v1.0.11

リリース日：2021 年 7 月 29 日（PT）

- **B2B レイヤーナビゲーションパッチの適用によって発生する問題を修正しました**—B2B レイヤーナビゲーションパッチを適用したお客様の場合、この修正により、 `Undefined offset` ストア表示を切り替えた後、検索ページに表示されるエラー。<!--MCLOUD-5287-->

- **Paypal チェックアウトパッチ** – 以前に注文した価格が表示される PayPal Express のAdobe Commerce 2.3.7 の問題を修正しました。<!--MC-42674-->

- **パッチカテゴリのサポート** – 品質パッチに割り当てられたパッチカテゴリと接触チャネルソースの処理に対するサポートが追加されました。 カテゴリを使用すると、ユーザーはフィルターと並べ替えを使用して、 [品質向上パッチツール](https://github.com/magento/quality-patches) と Site-wide Analysis Tool （SWAT）を使用します。 <!--MC-38577-->

## v1.0.10

リリース日：2021 年 5 月 10 日（PT）

- **Adobe Commerce 2.3.7 との互換性**—Adobe Commerce 2.3.7 にインストールする際の composer の依存関係の競合を解決。<!--MC-42131-->
- **バンドルされたパッチを複数回適用することで発生する問題を修正しました。**- バンドルされたパッチ（非推奨の他のパッチが含まれているパッチ）を 2 回以上適用すると、含まれている非推奨のパッケージが元に戻る場合があります。 すべてのパッチが 1 回だけ適用されるようになりました。 同じパッケージを再度適用しようとすると、パッチが既に適用されていることを示すメッセージが表示されます。<!--MC-41912-->
- **B2B レイヤー化されたナビゲーションパッチ**- ユーザーが B2B 共有カタログを有効にすると、レイヤーナビゲーションですべての製品オプションが表示されない別の問題を修正しました。<!--MCLOUD-7742-->

## v1.0.9

リリース日：2021 年 2 月 1 日（PT）

- **B2B レイヤー化されたナビゲーションパッチ**- B2B 共有カタログが有効な場合に、レイヤーナビゲーションですべての製品オプションが表示されない問題を修正しました。<!--MCLOUD-6923-->
- **PHP 7.4 との互換性**—PHP 7.4 でのクラウドパッチの互換性の問題を修正しました。<!--MCLOUD-7367-->
- **非推奨パッチが表示される** – 非推奨パッチのコンテンツ全体を含む代替パッチを適用した後、非推奨パッチがパッチテーブルに表示されるクラウドパッチの問題を修正しました。 これは、他の複数のパッチを組み合わせたパッチを適用した場合に発生する可能性があります。<!--MC-40626-->
- **パッチ適用時のサイレントエラー** – に関するクラウドパッチの問題を修正しました。 `git apply` コマンドは、一部の環境でパッチの適用に暗黙のうちに失敗しました。<!--MC-40529-->

## v1.0.8

リリース日：2020 年 10 月 14 日

- **magento/magento-cloud-patches の互換性のアップデート** – さんが、 `symfony` および `semver` でのバージョン制約 `composer.json` Adobe Commerce 2.4.1 以降のリリースとの互換性を保つためのファイル。<!--MCLOUD-7111-->

## v1.0.7

リリース日：2020 年 10 月 14 日

- **Adobe Commerce 2.3.0 から 2.3.5、2.4.0 用 Redis パッチ**- レベル 2 キャッシュを実装する際のカテゴリへの製品の追加をサポートするために、Redis パッチを更新しました。 <!--MCLOUD-6659-->

- **BraintreeVBE パッチ** – 管理者がBraintree精算レポートを表示しようとした際にエラーが発生した問題を修正します。 <!--MCLOUD-6684-->

- さて、 `ece-patches apply` コマンドは Unix を使用 `patch` ホストシステムで Git が利用できない場合にパッチを適用するコマンド。 <!--MCLOUD-7069-->

## v1.0.6

リリース日：

- **Adobe Commerce 2.3.0 ～ 2.3.4 用 Redis パッチ** – コミュニケーションの最適化とパフォーマンスの向上
   - Redis とAdobe Commerce間のネットワーク転送のサイズを削減
   - Redis の読み込みと書き込み操作の競合状態を修正
   - 保存時にエラーを処理するための基本キャッシュ アダプターの書き換え
   - Redis の CPU 消費を減らす<!--MCLOUD-6139-->

- **Adobe Commerce 2.3.0 ～ 2.3.5 用 Redis パッチ** – パフォーマンスの向上とエラーの修正
   - 無限ロックを防ぐためのキャッシュロックの実装の修正
   - 現在のロック機構の改善
   - 署名済みロックを実装して、並列リクエストからのロック解除を防ぐ
   - Redis 書き込み操作で発生する次のエラーを修正します。 `OOM command not allowed when used memory > maxmemory`
   - キャッシュのクリーンアップ処理の修正方法 `cat_p` 製品のアップデート中に実行されるタグ<!--MCLOUD-6110-->

- 必要なを適用する際にエラーが発生する問題を修正しました `amzn/amazon-pay-module` このモジュールが含まれていないAdobe Commerce v2.2.6 または 2.3.5 を使用している、クラウドインフラストラクチャプロジェクトのAdobe Commerceにパッチを適用します。 現在は、パッチ適用プロセスは、をスキップします `amzn/amazon-pay-module` モジュールがインストールされていない場合はパッチを適用します。<!--MCLOUD-6588-->

## v1.0.5

リリース日：2020 年 6 月 26 日（PT）

- **Redis パフォーマンスの向上**- Adobe Commerce バージョン 2.3.3 および 2.3.4 に Redis 最適化機能を追加します。これらの修正は、Adobe Commerce バージョン 2.3.5 リリースに含まれていました。 参照： [パフォーマンスの向上](https://devdocs.magento.com/guides/v2.3/release-notes/release-notes-2-3-5-commerce.html#performance-boosts) が含まれる _Adobe Commerce 2.3.5 リリースノート_.<!--MCLOUD-5771-->

- **New Relic ログ エンリチャー**- Commerce バージョン 1.0.4 のクラウドコンポーネントで導入されたNew Relicのログ機能の強化をサポートするために必要な Monolog ProcessorInterface を追加します。このパッチは、Adobe Commerce 2.1.x をデプロイするために必要です。パッチが適用されていない場合、ビルドは次の期間に失敗します `di:compile` プロセス。<!--MCLOUD-6029-->

## v1.0.4

リリース日：2020 年 5 月 12 日（PT）

- **Amazon ペイチェックアウト**- Amazon Pay 支払いウィジェットで、お客様が以下の支払方法を変更できなかった問題を修正しました。 _レビューと支払い_ チェックアウトプロセス中の手順。<!--MCLOUD-5930-->

- **カテゴリページの商品表示** – のカテゴリページに製品が表示されない問題を修正しました _すべてのページを表示_ 表示。<!--MCLOUD-5684-->

- **ページビルダー画像のアップロード** – 画像を画像ギャラリーにアップロードする際に次のエラーが発生することがあったページビルダーインターフェイスの問題を修正します。 `Destination folder is not writable or does not exist`<!--MCLOUD-5837-->

- **不要なサイトマップ生成警告を抑制**- サイトマップの生成中にエラーが発生した場合に再試行を追加し、エラーを自動的に復元できる場合に顧客のメール通知をスキップします。<!--MCLOUD-3025-->

- **サイトのパフォーマンスの向上** – のパフォーマンスの問題を修正します。 `Magento\Framework\App\DeploymentConfig\Reader::load` 機能：サイトのパフォーマンスに影響を与える長い読み込み時間が定期的に発生しました。 <!--MCLOUD-5650-->

- 支払方法パッチのパッチ割当を更新し、支払ベース・パッケージ（magento/magento2-base）ではなくMagentoモジュールをターゲットにして、支払モジュールが存在する場合にのみ支払パッチが適用されるようにしました。<!--MCLOUD-5666-->

- Magento Open Sourceとの互換性を確保するためにパッチを更新しました。<!--MCLOUD-5701-->

## v1.0.3

リリース日：2020 年 4 月 28 日（PT）

- Adobe Commerce 2.3.5 をサポートするための「FPC がデプロイメント中に無効になる」パッチの修正を追加しました。

## v1.0.2

リリース日：2020 年 2 月 27 日（PT）

このリリースには、次のパッチと重要な修正が含まれています。

- **magento/magento-cloud-patches の互換性のアップデート**

   - が更新されました `symfony` および `semver` でのバージョン制約 `composer.json` Adobe Commerce 2.4 以降のリリースとの互換性を保つためのファイル。<!--MAGECLOUD-5127-->

   - の制約を更新しました `composer.json` との互換性のために `ece-tools` 2002.0.22 以降 2002.0.x リリース。

- **PayPal Express チェックアウト**—2020 年 2 月 12 日（PT）に公開され、PayPal Express Checkout で注文に影響を与える問題を解決します。この問題では、注文の配送先住所が、「配送」ページのドロップダウンメニューから選択するのではなく、テキストフィールドに手動で入力された国地域を指定します。 詳しくは、パッチのダウンロードページの完全なパッチの説明を参照してください。

- **アプリケーション導入の修正**- デプロイメントプロセス中にフルページキャッシュが無効になる問題を修正するためのパッチを追加しました。 このパッチは、Adobe Commerce 2.3.2 以降のリリースに適用されます。

- **非同期/一括 API のスコープパラメーター** – このパッチを更新して、 `composer.json` ファイル。 このパッチは、Magento Open Source 2.3.1 および 2.3.2 に適用されます。詳しくは、パッチのダウンロードページの完全なパッチの説明を参照してください。

## v1.0.1

リリース日：2020 年 2 月 6 日（PT）

magento/magento-cloud-patches v1.0.1 リリースのMagento Open Sourceダウンロードページからすべてのソフトウェア 2.x パッチを取り込みました。 以前にプロジェクトにパッチをコピーした場合は、競合を避けるために削除します。

このリリースには、次のパッチと重要な修正が含まれています。

- **cron デッドロックを修正し、cron ロックを改善する**—

   - のステータス値が正しくないために一部の cron ジョブが実行されない問題を修正しました `cron_schedule` テーブル。 ここでは、を使用する代わりに、Adobe Commerce lock フレームワークを使用して、cron ジョブステータスを確認および更新します `cron_schedule` テーブル。 エラーステータスで終了した Cron ジョブは、24 時間待たずに、次回の Cron 実行時に再試行されます。

   - を追加します _再試行_ 内のデータの更新中にデッドロックを回避する操作 `cron_schedule` テーブル。

- **更新日 `magento/magento-cloud-patches` Magento Open Source 2.x で使用可能なすべてのパッチを含めるには**- ソフトウェアのダウンロードページで使用可能なすべてのMagento Open Source 2.x パッチが含まれるように magento/magento-cloud-patches パッケージを更新しました。 以前にクラウドインフラストラクチャプロジェクト上のAdobe CommerceにMagento Open Sourceパッチをコピーした場合は、競合を避けるために、それらを削除します。<!--MAGECLOUD-4606-->

- **Elasticsearchカタログのページネーションの修正** —magento/magento-cloud-patches v1.0 で提供されているElasticsearchカタログのページネーションパッチを、より効果的な修正に置き換えました。<!--MAGECLOUD-4847-->

- **ページビルダーパッチ**—Commerce 1.0.0 の Cloud Patches では、既知の Page Builder Remote Code Execution （RCE）の脆弱性に対処するために、Adobe Commerce 2.3.3 に基づく初期修正と共に Page Builder パッチをバンドルしました。問題を修正するための複数の最適化を含む、Adobe Commerce 2.3.4.に基づくより安定した実装で、これらのパッチを更新しました。<!--MAGECLOUD-4884-->

  magento/magento-cloud-patches 1.0.0 パッケージを使用している場合でも、ページビルダー RCE の脆弱性の問題から保護されます。 1.0.1 以降にアップデートすると、同じ修正をより適切に実装できます。

## v1.0.0

リリース日：2019 年 11 月 14 日（PT）

これは、の最初のリリースです [`magento/magento-cloud-patches`](https://github.com/magento/magento-cloud-patches) パッケージ（ `ece-tools` パッケージバージョン 2002.0.22 以降のリリース。

このリリースには、次のパッチと重要な修正が含まれています。

- **2.3.1.x および 2.3.2.x リリース用 Page Builder セキュリティパッチ** – ページビルダープレビューで、ネットワーク（RCE）経由の任意のコード実行をトリガーするために使用でき、グローバルな情報リークを引き起こす一部のテンプレートメソッドに認証されていないユーザーがアクセスできる問題を修正しました。 この問題は、Adobe Commerce バージョン 2.3.1 および 2.3.2 で、サポートされていないバージョンのページビルダーを使用している場合に発生する可能性があります。<!--MAGECLOUD-4649-->

- **MSI パッチ**- デフォルトの在庫設定を使用して在庫を管理する際に、インデックス作成エラーやパフォーマンスの問題が発生する問題を修正します。<!--MAGECLOUD-4428-->

- **新しいメールインターフェイスの後方互換性** – に起因する後方非互換性の問題を修正します。 `Magento\Framework\Mail\EmailMessageInterface` Adobe Commerce v2.3.3 で導入された PHP インターフェイス。このパッチの範囲では、 `EmailMessageInterface` 古いのを継承 `MessageInterface`、およびAdobe Commerceコアモジュールは、依存するに戻ります `MessageInterface`.<!--MAGECLOUD-4422-->

- **カタログのページネーションがElasticsearch 6.x で機能しない**—Elasticsearch 6.x をカタログ検索エンジンとして使用する場合に発生する、検索結果のページネーションに関する重大な問題を修正しました。<!--MAGECLOUD-4448-->
