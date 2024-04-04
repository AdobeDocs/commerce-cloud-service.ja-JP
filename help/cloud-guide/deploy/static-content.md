---
title: 静的コンテンツのデプロイメント
description: クラウドインフラストラクチャプロジェクト上のAdobe Commerceに画像、スクリプト、CSS などの静的コンテンツをデプロイするための戦略について説明します。
feature: Cloud, Build, Deploy, SCD
exl-id: e128d0d5-1326-44e5-a822-009e11ba105f
source-git-commit: 1253d8357fd2554050d1775fefbc420a2097db5f
workflow-type: tm+mt
source-wordcount: '707'
ht-degree: 0%

---

# 静的コンテンツデプロイメント戦略

静的コンテンツのデプロイメント (SCD) は、生成するコンテンツの量（画像、スクリプト、CSS、ビデオ、テーマ、ロケール、Web ページなど）と、コンテンツを生成するタイミングに応じて、ストアのデプロイメントプロセスに大きな影響を与えます。 例えば、デフォルトの方法では、 [導入段階](process.md#deploy-phase-deploy-phase) サイトがメンテナンスモードの場合。ただし、このデプロイメント戦略では、マウントされたに直接コンテンツを書き込むのに時間がかかります `pub/static` ディレクトリ。 必要に応じてデプロイメント時間を短縮するためのオプションや戦略がいくつか用意されています。

## JavaScript およびHTMLコンテンツの最適化

バンドル化と縮小化を使用して、静的コンテンツのデプロイメント中に、最適化された JavaScript とHTMLコンテンツを作成できます。

### コンテンツを縮小

で静的ビュー・ファイルのコピーをスキップすると、デプロイメント・プロセス中の SCD のロード時間を短縮できます。 `var/view_preprocessed` ディレクトリと生成 _縮小化_ HTMLがリクエストされた場合。 この機能を有効にするには、 [SKIP_MINIFICATION_SKIP_HTML_MINIFICATION](../environment/variables-global.md#skiphtmlminification) グローバル環境変数をに設定します。 `true` （内） `.magento.env.yaml` ファイル。

>[!NOTE]
>
>次で始まる `ece-tools` パッケージ・バージョン 2002.0.13 の場合、SKIP_VERSION_MINIFICATION 変数のデフォルト値は次のように設定されます。HTML: `true`.

保存できます **その他** 不要なテーマファイルの数を減らすことで、デプロイメントに要する時間とディスク容量を確保できます。 例えば、 `magento/backend` 英語のテーマと他の言語のカスタムテーマ。 これらのテーマ設定は、 [SCD_MATRIX](../environment/variables-deploy.md#scdmatrix) 環境変数。

## デプロイ戦略の選択

デプロイメント戦略は、 _ビルド_ フェーズ、 _デプロイ_ フェーズ、または _オンデマンド_. 次の図に示すように、デプロイフェーズで静的コンテンツを生成することは、最適な選択肢の最も低いです。 縮小HTMLの場合でも、各コンテンツファイルをマウントされた `~/pub/static` ディレクトリに格納されます。このディレクトリには長い時間がかかる場合があります。 オンデマンドでの静的コンテンツの生成は、最適な選択肢のようです。 ただし、コンテンツファイルがキャッシュに存在しない場合は、要求された時点で生成され、読み込み時間がユーザーエクスペリエンスに追加されます。 したがって、ビルドフェーズでの静的コンテンツの生成が最適です。

![SCD ロード比較](../../assets/scd-load-times.png)

### ビルド時の SCD の設定

ビルドフェーズでの静的コンテンツの生成は、HTMLを縮小した最適な設定です。 [**ダウンタイムなし** デプロイメント](reduce-downtime.md)( 別名 **理想状態**. マウントされたドライブにファイルをコピーする代わりに、 `./init/pub/static` ディレクトリ。

静的コンテンツを生成するには、テーマとロケールにアクセスする必要があります。 Adobe Commerceは、テーマをファイルシステムに保存します。このファイルシステムはビルドフェーズでアクセスできますが、Adobe Commerceはロケールをデータベースに保存します。 データベースは _not_ は、ビルドフェーズで使用できます。 ビルドフェーズで静的コンテンツを生成するには、 `config:dump` コマンドを `ece-tools` ロケールをファイルシステムに移動するパッケージ。 ロケールを読み取り、それらを `app/etc/config.php` ファイル。

**ビルド時に SCD を生成するようにプロジェクトを構成するには**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。
1. SSH を使用してリモート環境にログインします。

   ```bash
   magento-cloud ssh
   ```

1. ロケールをファイルシステムに移動し、 [`config.php` ファイル](../development/commerce-version.md#create-a-configphp-file).

1. The `.magento.env.yaml` 設定ファイルには次の値が含まれている必要があります。

   - [SKIP_MINIFICATION_SKIP_HTML_MINIFICATION](../environment/variables-global.md#skip_html_minification) 次に該当 `true`
   - [SKIP_SCD](../environment/variables-build.md#skip_scd) ビルドステージで `false`
   - [SCD_STRATEGY](../environment/variables-build.md#scd_strategy) 次に該当 `compact`

1. の設定を確認 [デプロイ後のフック](../application/hooks-property.md) （内） `.magento.app.yaml` ファイル。

1. 設定を確認するには、 [理想的な状態のスマートウィザード](smart-wizards.md).

   ```bash
   php ./vendor/bin/ece-tools wizard:ideal-state
   ```

### SCD をオンデマンドで設定する

オンデマンドでの SCD の生成は、統合環境での開発ワークフローに最適です。 デプロイメント時間を短縮できるので、実装をすばやく確認し、統合テストを実行できます。 を有効にします。 [SCD_ON_DEMAND](../environment/variables-global.md#scdondemand) 環境変数は、 `.magento.env.yaml` ファイル。 SCD_ON_DEMAND 変数は、SCD に関連する他のすべての構成を上書きし、 `~/pub/static` ディレクトリ。

SCD オンデマンド戦略を使用する場合、ホームページなど、リクエストするページでキャッシュをプリロードするのに役立ちます。 期待されるページのリストを [WARM_UP_PAGES](../environment/variables-post-deploy.md#warmuppages) 環境変数を設定します。 `.magento.env.yaml` ファイル。

>[!WARNING]
>
>実稼動環境では SCD オンデマンド戦略を使用しないでください。

### SCD をスキップしています

静的コンテンツの生成を完全にスキップすることもできます。 次の設定が可能です。 [SKIP_SCD](../environment/variables-build.md#skipscd) グローバルステージの環境変数を使用して、SCD に関連する他の設定を無視します。 これは、 `~/pub/static` ディレクトリ。
