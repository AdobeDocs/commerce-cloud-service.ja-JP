---
title: コマース用クラウドパッチ
description: クラウドパッチパッケージの最新の改善点のリストを参照してください。
recommendations: noDisplay, catalog
last-substantial-update: 2024-01-16T00:00:00Z
exl-id: ae6b511b-a37d-4776-9a5e-ad7d9f9f6611
source-git-commit: 1e06545340cb63df483d1db5b2a890499e99e6d3
workflow-type: tm+mt
source-wordcount: '2175'
ht-degree: 0%

---

# コマース用クラウドパッチ

The [クラウドパッチ](https://github.com/magento/magento-cloud-patches) パッケージには、すべてのAdobe Commerceバージョンのクラウド環境との統合を改善し、重要な修正の迅速な配信をサポートする、一連の必須パッチが用意されています。

コマース用のクラウドパッチパッケージは、ECE-Tools パッケージの依存関係であり、ECE-Tools パッケージをインストールまたは更新する際にインストールおよび更新されます。 また、コマース用のクラウドパッチをスタンドアロンパッケージとして使用および管理し、クラウドプラットフォーム上にないAdobe Commerceプロジェクトにパッチを適用することもできます。 これらのリリースノートでは、このパッケージの最新の改善点を説明します。

>[!TIP]
>
>プロジェクトに必要なパッチがすべて含まれていることを確認するには、 [ece-tools の最新バージョン](../dev-tools/update-package.md).

>[!NOTE]
>
>詳しくは、 [パッチの適用](../development/apply-patches.md) プロジェクトにパッチを適用する手順については、を参照してください。

The `magento/magento-cloud-patches` パッケージでは、次のバージョンシーケンスが使用されます。 `<major>.<minor>.<patch>`

<!--Add release notes below-->

## v1.0.25 {#latest}

リリース日： 2024 年 1 月 17 日

- **キャッシュの改善** — このパッチは、Adobe Commerceバージョン 2.4.4 以降の場合に、レイアウトのキャッシュ効率を高め、メモリ使用量を大幅に削減します。<!-- MCLOUD-11514 -->
- **CRON ジョブの改善** — このパッチは、Adobe Commerceバージョン 2.4.4 以降で、ジョブのミスが cron ジョブロックを不必要に待たない問題を修正します。<!-- MCLOUD-11329 -->

## v1.0.24

リリース日： 2023 年 9 月 16 日

- **パフォーマンスの向上** — このパッチは、Adobe Commerce 2.4.6 の同じデプロイメント設定が読み込まれる回数を 2.4.6-p1 に減らすことで、パフォーマンスに影響する問題を修正します<!-- MCLOUD-10604 -->

## v1.0.23

リリース日： 2023 年 7 月 31 日

- **パッチ MCLOUD-10604を削除しました。** — このパッチは QPT に移動されました。<!-- MCLOUD-10736 -->

## v1.0.22

リリース日： 2023 年 6 月 20 日

- **QPT CLI のウィザード/出力の拡張**—QPT CLI のウィザード/出力に、依存関係がある場合にパッチの詳細と要件を確認する警告を追加しました。<!-- ACP2E-1963 -->
- **Commerce 2.4.6 用のパッチを追加しました。**
   - 修正された `regexp cache tag` 検証。<!-- MCLOUD-10226 -->
   - 同じデプロイメント設定が読み込まれる回数を減らし、パフォーマンスを向上させました。<!-- MCLOUD-10604 -->
- **Commerce 2.3.7 から 2.4.6 へのパッチを追加しました。** — インクリメントが 1 ではなくランダムな値で増分される問題を修正しました。 `catalog_product_entity_*` テーブル。<!-- MCLOUD-10032 -->
- **Commerce 2.4.0 から 2.4.6 へのパッチを追加しました。** — 次のようなエラーが発生する問題を修正しました。 `The file can't be deleted. Warning!unlink: No such file or directory`：管理から JS/CSS キャッシュをフラッシュする際に発生していました。<!-- MCLOUD-10279 -->

## v1.0.21

リリース日： 2023 年 3 月 11 日

- **PHP 8.2 のサポートの強化** — 特定の PHP 8.2.x バージョンで Commerce 2.4.6 をサポートするための互換性の問題を修正しました。

## v1.0.20

リリース日： 2022 年 10 月 27 日

- **L2 キャッシュの改善パッチを追加しました** — このパッチは、Commerce バージョン 2.4.0 および 2.4.1 のローカル L2 キャッシュをフラッシュする問題を修正しました。<!-- MCLOUD-7845 -->

## v1.0.19

リリース日： 2022 年 9 月 14 日

- **PHP 8.1 のサポートの強化** — 特定の PHP 8.1.x バージョンとの互換性の問題を修正しました。

## v1.0.18

リリース日： 2022 年 8 月 12 日

Adobe Commerce 2.4.5 の重要なパッチ：

- **Braintree支払を使用した注文の問題** — このパッチは、管理者が新しい注文や再注文を行えないという重大な問題を解決します。<!-- MCLOUD-9137 -->

詳しくは、 [管理者が注文/並べ替えを作成できないのはBraintree支払いが有効なときです](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/known-issues-patches-attached/admin-cant-create-order-reorder-when-braintree-payment-enabled.html).

## v1.0.17

リリース日： 2022 年 5 月 24 日

内のセキュリティパッチの制約を修正しました。 `patches.json` ファイル。

## v1.0.16

リリース日： 2022 年 3 月 31 日

Adobe Commerce 2.3.3-p1 以降のバージョンの重要なパッチ：

パッチを更新し、 **重要な** 未認証のリモートコード実行を引き起こす脆弱性。<!-- MCLOUD-8479 -->

詳しくは、 [Adobeセキュリティ情報 APSB22-12](https://helpx.adobe.com/security/products/magento/apsb22-12.html).

## v1.0.15

リリース日： 2022 年 3 月 11 日

- **PHP 8.1 をサポート**—PHP 8.1 のサポートを追加し、PHP 7.0 および 7.1 のサポートを廃止しました。
- **Adobe Commerce 2.3.3 のパッチを追加しました。** — 製品ページに表示される固定通貨。

## v1.0.14

リリース日： 2022 年 2 月 14 日

Adobe Commerce 2.3.3-p1 以降のバージョンの重要なパッチ：

を解決するためのパッチを追加しました。 **重要な** 未認証のリモートコード実行を引き起こす脆弱性。<!-- MCLOUD-8461 -->

詳しくは、 [Adobeセキュリティ情報 APSB22-12](https://helpx.adobe.com/security/products/magento/apsb22-12.html).

## v1.0.13

リリース日： 2021 年 10 月 26 日

- **モノローグを更新** — に必要な最小バージョンを更新しました。 `monolog` ～にパッケージ化する `^2.3`.<!-- ACMP-1263 -->
- **互換性のない PHP メソッド**—Adobe Commerceバージョン 2.4.3 と 2.3.7-p1 に対して互換性のない PHP メソッドを修正しました。<!-- AC-384 -->
- **PHP エラー** — 修正された `PHP error 'Undefined variable: errorMessage' ...` パッチの適用中に発生したエラー。<!-- ACP2E-138 -->

## v1.0.12

リリース日： 2021 年 8 月 13 日

Adobe Commerce 2.4.3 および 2.3.7-p1 の重要なパッチ：

- **API のレート制限の問題** — このパッチは、配列内の 20 個を超える項目を持つ要求を Web API が処理できないデフォルトのレート制限を修正します。 このパッチはレート制限のデフォルト値を引き上げます。 Adobe Commerce [2.4.3 リリースノート](https://devdocs.magento.com/guides/v2.4/release-notes/commerce-2-4-3.html#apply-mc-43048__set_rate_limits__243patch-to-address-issue-with-api-rate-limiting) そして [2.3.7 リリースノート](https://devdocs.magento.com/guides/v2.3/release-notes/2-3-7-p1.html#apply-mc-43048__set_rate_limits__237-p1patch-to-address-issue-with-api-rate-limiting).<!-- MC-43048 -->

## v1.0.11

リリース日： 2021 年 7 月 30 日

- **B2B レイヤードナビゲーションパッチを適用すると発生していた問題を修正しました。**—B2B レイヤードナビゲーションパッチを適用したお客様の場合、この修正は `Undefined offset` ストア表示を切り替えた後に検索ページに表示されるエラー。<!--MCLOUD-5287-->

- **Paypal チェックアウトパッチ** — 以前に配置された注文価格が表示される PayPal Express のAdobe Commerce 2.3.7 の問題を修正しました。<!--MC-42674-->

- **パッチカテゴリのサポート** — 品質パッチに割り当てられたパッチカテゴリと接触チャネルソースの処理のサポートを追加しました。 カテゴリを使用すると、フィルタと並べ替えを使用して、 [クォリティパッチツール](https://github.com/magento/quality-patches) とサイト全体分析ツール (SWAT) を使用します。 <!--MC-38577-->

## v1.0.10

リリース日： 2021 年 5 月 11 日

- **Adobe Commerce 2.3.7 との互換性**— Adobe Commerce 2.3.7 でのインストールで、コンポーザーの依存関係の競合を解決しました。<!--MC-42131-->
- **バンドルされたパッチを複数回適用することが原因で発生していた問題を修正しました。** — バンドルされたパッチ（他の非推奨パッチを含むパッチ）を複数回適用すると、含まれる非推奨パッケージが元に戻る可能性があります。 すべてのパッチが 1 回だけ適用されるようになりました。 同じパッケージを再度適用しようとすると、パッチが既に適用されたというメッセージが表示されます。<!--MC-41912-->
- **B2B レイヤー型ナビゲーションパッチ** — ユーザーが B2B 共有カタログを有効にしたときに、レイヤーナビゲーションですべての製品オプションが表示されない問題を修正しました。<!--MCLOUD-7742-->

## v1.0.9

リリース日： 2021 年 2 月 2 日

- **B2B レイヤー型ナビゲーションパッチ**- B2B 共有カタログが有効な場合に、レイヤーナビゲーションにすべての製品オプションが表示されない問題を修正しました。<!--MCLOUD-6923-->
- **PHP 7.4 との互換性**—PHP 7.4 でのクラウドパッチの互換性の問題を修正しました。<!--MCLOUD-7367-->
- **非推奨（廃止予定）のパッチが表示される** — 非推奨パッチの内容全体を含む置き換えパッチを適用した後、非推奨パッチがパッチテーブルに表示されるというクラウドパッチの問題を修正しました。 他の複数のパッチを組み合わせたパッチを適用した場合に、この問題が発生する可能性があります。<!--MC-40626-->
- **パッチ適用時のサイレントエラー** — クラウドパッチで、 `git apply` 一部の環境で、コマンドが警告なくパッチを適用できませんでした。<!--MC-40529-->

## v1.0.8

リリース日： 2020 年 10 月 14 日

- **magento/magento-cloud-patches の互換性アップデート** — を更新しました。 `symfony` および `semver` のバージョン制約 `composer.json` ファイルを参照してください。<!--MCLOUD-7111-->

## v1.0.7

リリース日： 2020 年 10 月 14 日

- **Adobe Commerce 2.3.0 から 2.3.5、2.4.0 の Redis パッチ** — レベル 2 のキャッシュを実装する際に、カテゴリへの製品の追加をサポートするように Redis パッチを更新しました。 <!--MCLOUD-6659-->

- **BraintreeVBE パッチ** — 管理者がBraintree決済レポートを表示しようとした際にエラーが発生する問題を修正しました。 <!--MCLOUD-6684-->

- さて、 `ece-patches apply` コマンドは Unix を使用します。 `patch` ホストシステムで Git が使用できない場合にパッチを適用するコマンドを使用します。 <!--MCLOUD-7069-->

## v1.0.6

リリース日：

- **Adobe Commerce 2.3.0 ～ 2.3.4 の Redis パッチ** — 通信を最適化し、パフォーマンスを向上
   - Redis とAdobe Commerce間のネットワーク転送のサイズを削減
   - Redis の読み込みおよび書き込み操作の競合状態を修正
   - 保存時のエラーを処理するベースキャッシュアダプタを書き換え
   - Redis の CPU 消費を削減<!--MCLOUD-6139-->

- **Adobe Commerce 2.3.0 ～ 2.3.5 の Redis パッチ** — パフォーマンスを向上させ、エラーを修正
   - 無限ロックを防ぐためにキャッシュロックの実装を修正しました。
   - 現在のロック機構を改善する
   - ロック解除が並列要求から妨げられるように署名付きロックを実装する
   - Redis の書き込み操作で発生する次のエラーを修正します。 `OOM command not allowed when used memory > maxmemory`
   - 次の方法でクリーンキャッシュの処理を修正 `cat_p` 製品のアップデート中に実行されるタグ<!--MCLOUD-6110-->

- 必要な `amzn/amazon-pay-module` Adobe Commerce v2.2.6 または 2.3.5 を使用した、クラウドインフラストラクチャプロジェクト上のAdobe Commerceへのパッチ（このモジュールは含まれません）。 現在は、パッチ適用プロセスでは `amzn/amazon-pay-module` patch モジュールがインストールされていない場合。<!--MCLOUD-6588-->

## v1.0.5

リリース日： 2020 年 6 月 27 日

- **Redis のパフォーマンス向上**—Adobe Commerceバージョン 2.3.3 および 2.3.4 に Redis 最適化機能を追加しました。これらの修正は、Adobe Commerceバージョン 2.3.5 リリースに含まれていました。 詳しくは、 [パフォーマンス向上](https://devdocs.magento.com/guides/v2.3/release-notes/release-notes-2-3-5-commerce.html#performance-boosts) （内） _Adobe Commerce 2.3.5 リリースノート_.<!--MCLOUD-5771-->

- **New Relic log enricher**- Commerce バージョン 1.0.4 のクラウドコンポーネントで導入されたNew Relicログ機能の改善をサポートするために必要な Monolog ProcessorInterface が追加されました。このパッチは、Adobe Commerce 2.1.x のデプロイに必要です。パッチが適用されていない場合、 `di:compile` プロセス。<!--MCLOUD-6029-->

## v1.0.4

リリース日： 2020 年 5 月 13 日

- **Amazon Pay チェックアウト**— Amazon Pay 支払いウィジェットで、顧客が支払い方法を _レビューと支払い_ の手順を実行します。<!--MCLOUD-5930-->

- **カテゴリページでの商品の表示** — のカテゴリページに製品が表示されない問題を修正しました。 _すべてのページを表示_ 表示。<!--MCLOUD-5684-->

- **Page Builder の画像のアップロード** — 画像ギャラリーに画像をアップロードする際に、ページビルダーインターフェイスで次のエラーが発生する問題を修正しました。 `Destination folder is not writable or does not exist`<!--MCLOUD-5837-->

- **不要なサイトマップ生成の警告を抑制する** — サイトマップの生成中にエラーが発生した場合に再試行を追加し、エラーを自動的に回復できる場合に顧客の電子メール通知をスキップします。<!--MCLOUD-3025-->

- **サイトのパフォーマンスの向上**— `Magento\Framework\App\DeploymentConfig\Reader::load` 関数を使用して、サイトのパフォーマンスに影響を与える長い負荷時間を定期的に経験しています。 <!--MCLOUD-5650-->

- 支払い方法パッチのパッチ割り当てを更新し、支払いモジュールが存在する場合にのみ支払いパッチが適用されるように、Magentoベースパッケージ (magento/magento2-base) の代わりに支払いモジュールをターゲットにしました。<!--MCLOUD-5666-->

- パッチを更新し、Magento Open Sourceとの互換性を確保しました。<!--MCLOUD-5701-->

## v1.0.3

リリース日： 2020 年 4 月 28 日

- Adobe Commerce 2.3.5 をサポートするために、「デプロイメント中に FPC が無効になる」パッチの修正を追加しました。

## v1.0.2

リリース日： 2020 年 2 月 28 日

このリリースには、次のパッチと重要な修正が含まれています。

- **magento/magento-cloud-patches の互換性アップデート**

   - 更新された `symfony` および `semver` のバージョン制約 `composer.json` ファイルを参照してください。<!--MAGECLOUD-5127-->

   - の更新された制約 `composer.json` ～との互換性を保つために `ece-tools` 2002.0.22 以降の 2002.0.x リリースです。

- **PayPal Express チェックアウト** — このパッチは 2020 年 2 月 12 日に公開され、PayPal Express Checkout での注文に影響する問題を解決します。注文の配送先住所は、配送先ページのドロップダウンメニューから選択した地域ではなく、テキストフィールドに手動で入力した国地域を指定します。 パッチのダウンロードページで、パッチの完全な説明を参照してください。

- **アプリケーションのデプロイメントの修正** — デプロイメントプロセス中にページ全体のキャッシュが無効になる問題を修正するためのパッチを追加しました。 このパッチは、Adobe Commerce 2.3.2 以降のリリースに適用されます。

- **非同期/一括 API のスコープパラメーター** — このパッチを更新して、 `composer.json` ファイル。 このパッチは、Magento Open Source2.3.1 および 2.3.2 に適用されます。パッチのダウンロードページで、パッチの完全な説明を参照してください。

## v1.0.1

リリース日： 2020 年 2 月 7 日

magento/magento-cloud-patches v1.0.1 リリースのソフトウェアダウンロードページからすべてのMagento Open Source2.x パッチが含まれています。 以前にプロジェクトにパッチをコピーした場合は、競合を避けるために削除します。

このリリースには、次のパッチと重要な修正が含まれています。

- **cron のデッドロックを修正し、cron ロックを改善**—

   - のステータス値が正しくないことが原因で、一部の cron ジョブが実行されない問題を修正しました。 `cron_schedule` 表。 ここでは、Adobe Commerceロックフレームワークを使用し、 `cron_schedule` 表。 エラーステータスで終了した Cron ジョブは、次の Cron 実行時に、24 時間待たずに再試行されます。

   - を追加します。 _再試行_ のデータの更新中にデッドロックを回避する操作 `cron_schedule` 表。

- **更新済み `magento/magento-cloud-patches` Magento Open Source2.x 用のすべての使用可能なパッチを含めるには**— magento/magento-cloud-patches パッケージを更新し、ソフトウェアのダウンロードページで使用可能なMagento Open Source2.x のすべてのパッチを含めました。 以前にクラウドインフラストラクチャ上のAdobe CommerceプロジェクトにMagento Open Sourceパッチをコピーしていた場合は、競合を避けるためにそれらを削除します。<!--MAGECLOUD-4606-->

- **Elasticsearchカタログのページネーションの修正** — magento/magento-cloud-patches v1.0 で提供されるElasticsearchカタログのページネーションパッチを、より効果的な修正に置き換えました。<!--MAGECLOUD-4847-->

- **Page Builder パッチ**—Commerce 1.0.0 のクラウドパッチでは、Page Builder パッチをバンドルして、既知の Page Builder リモートコード実行 (RCE) の脆弱性に対処し、Adobe Commerce 2.3.3 に基づく初期修正をおこないました。これらのパッチを、Adobe Commerce 2.3.4.に基づくより安定した実装で更新しました。この実装には、問題を修正するための複数の最適化が含まれています。<!--MAGECLOUD-4884-->

  magento/magento-cloud-patches 1.0.0 パッケージがある場合でも、Page Builder RCE の脆弱性の問題からは保護されています。 1.0.1 以降に更新した場合は、同じ修正の実装が改善されています。

## v1.0.0

リリース日： 2019 年 11 月 15 日

これは、 [`magento/magento-cloud-patches`](https://github.com/magento/magento-cloud-patches) パッケージ ( `ece-tools` パッケージバージョン 2002.0.22 以降のリリース。

このリリースには、次のパッチと重要な修正が含まれています。

- **2.3.1.x および 2.3.2.x リリース用の Page Builder セキュリティパッチ**— Page Builder プレビューで、未認証ユーザーがネットワーク (RCE) を介した任意のコード実行のトリガーに使用できる一部のテンプレートメソッドにアクセスでき、グローバルな情報リークが発生する問題を修正しました。 この問題は、Adobe Commerceバージョン 2.3.1 および 2.3.2 でサポートされていないバージョンの Page Builder を使用している場合に発生する可能性があります。<!--MAGECLOUD-4649-->

- **MSI パッチ** — 在庫管理にデフォルトの在庫設定を使用する際にインデックス作成エラーやパフォーマンスの問題が発生する問題を修正しました。<!--MAGECLOUD-4428-->

- **新しいメールインターフェイスの後方互換性**- `Magento\Framework\Mail\EmailMessageInterface` Adobe Commerce v2.3.3 で導入された PHP インターフェイス。このパッチの範囲では、新しい `EmailMessageInterface` は、古い `MessageInterface`、およびAdobe Commerceコアモジュールは、 `MessageInterface`.<!--MAGECLOUD-4422-->

- **カタログのページネーションはElasticsearch6.x では機能しません** — カタログ検索エンジンとしてElasticsearch6.x を使用しているお客様に影響する、検索結果のページネーションに関する重要な問題を修正しました。<!--MAGECLOUD-4448-->
