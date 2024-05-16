---
title: 静的コンテンツデプロイメント
description: 画像、スクリプト、CSS などの静的コンテンツをクラウドインフラストラクチャプロジェクト上のAdobe Commerceにデプロイする方法について説明します。
feature: Cloud, Build, Deploy, SCD
exl-id: e128d0d5-1326-44e5-a822-009e11ba105f
source-git-commit: 1253d8357fd2554050d1775fefbc420a2097db5f
workflow-type: tm+mt
source-wordcount: '707'
ht-degree: 0%

---

# 静的コンテンツのデプロイメント戦略

静的コンテンツのデプロイメント（SCD）は、画像、スクリプト、CSS、ビデオ、テーマ、ロケール、web ページなどの生成するコンテンツの量と、コンテンツを生成するタイミングに依存するストアデプロイメントプロセスに大きな影響を与えます。 例えば、デフォルトの方法では、次の場合に静的コンテンツを生成します [デプロイフェーズ](process.md#deploy-phase-deploy-phase) サイトがメンテナンスモードの場合は、マウントされたディレクトリにコンテンツを直接書き込むのに時間がかかります `pub/static` ディレクトリ。 必要に応じて、デプロイメント時間を短縮するのに役立つオプションや戦略がいくつかあります。

## JavaScript とHTMLのコンテンツの最適化

静的コンテンツのデプロイメント中にバンドルおよび縮小を使用して、最適化された JavaScript およびHTMLコンテンツを構築できます。

### コンテンツの縮小

で静的ビューファイルのコピーをスキップする場合は、デプロイメントプロセス中の SCD の読み込み時間を短縮できます。 `var/view_preprocessed` ディレクトリと生成 _縮小_ HTML（リクエスト時）。 この機能を有効にするには、 [SKIP_HTML_MINIFICATION](../environment/variables-global.md#skiphtmlminification) グローバル環境変数をに `true` が含まれる `.magento.env.yaml` ファイル。

>[!NOTE]
>
>で始まる `ece-tools` パッケージバージョン 2002.0.13 では、SKIP_VARIATION_MINIFICATIONHTMLのデフォルト値はに設定されています。 `true`.

保存できます **詳細** 不要なテーマファイルの数を減らして、デプロイメント時間とディスク容量を削減します。 例えば、 `magento/backend` 英語のテーマおよび他の言語のカスタムテーマ。 これらのテーマの設定は、 [SCD_MATRIX](../environment/variables-deploy.md#scdmatrix) 環境変数。

## デプロイ方法の選択

デプロイメント戦略は、 _ビルド_ フェーズ、 _deploy_ フェーズ、または _オンデマンド_. 次のチャートに示すように、デプロイフェーズでは静的コンテンツを生成することが、最適な選択ではありません。 HTMLが縮小されている場合でも、各コンテンツファイルをマウントされたにコピーする必要があります `~/pub/static` ディレクトリ（時間がかかる場合があります）。 静的コンテンツをオンデマンドで生成することは、最適な選択と思われます。 ただし、コンテンツファイルがキャッシュに存在しない場合は、リクエストされた瞬間に生成されるので、読み込み時間がユーザーエクスペリエンスに長くなります。 したがって、ビルドフェーズでは静的コンテンツを生成するのが最適です。

![SCD 負荷比較](../../assets/scd-load-times.png)

### ビルド時の SCD の設定

ビルドフェーズでの静的コンテンツの生成をHTMLを縮小することが、次の場合に最適な設定です。 [**ダウンタイムなし** デプロイメント](reduce-downtime.md)（別名） **理想状態**. ファイルをマウントしたドライブにコピーする代わりに、からシンボリックリンクを作成します。 `./init/pub/static` ディレクトリ。

静的コンテンツを生成するには、テーマとロケールにアクセスする必要があります。 Adobe Commerceは、テーマをファイルシステムに格納します。これはビルドフェーズでアクセスできますが、Adobe Commerceはデータベースにロケールを格納します。 データベースは _ではない_ ビルドフェーズで使用できます。 ビルドフェーズで静的コンテンツを生成するには、次を使用する必要があります `config:dump` のコマンド `ece-tools` パッケージを作成して、ロケールをファイルシステムに移動します。 ロケールが読み取られ、 `app/etc/config.php` ファイル。

**ビルド時に SCD を生成するようにプロジェクトを設定するには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。
1. SSH を使用してリモート環境にログインします。

   ```bash
   magento-cloud ssh
   ```

1. ロケールをファイルシステムに移動してから、 [`config.php` ファイル](../development/commerce-version.md#create-a-configphp-file).

1. この `.magento.env.yaml` 設定ファイルには、次の値を含める必要があります。

   - [SKIP_HTML_MINIFICATION](../environment/variables-global.md#skip_html_minification) 等しい `true`
   - [SKIP_SCD](../environment/variables-build.md#skip_scd) ビルドステージで： `false`
   - [SCD_STRATEGY](../environment/variables-build.md#scd_strategy) 等しい `compact`

1. の設定の確認 [デプロイ後フック](../application/hooks-property.md) が含まれる `.magento.app.yaml` ファイル。

1. 次を実行して設定を検証 [理想的な状態を実現するスマートウィザード](smart-wizards.md).

   ```bash
   php ./vendor/bin/ece-tools wizard:ideal-state
   ```

### SCD のオンデマンド設定

SCD をオンデマンドで生成することは、統合環境における開発ワークフローに最適です。 デプロイメント時間が短縮されるので、実装をすばやく確認したり、統合テストを実行したりできます。 を有効にする [SCD_ON_DEMAND](../environment/variables-global.md#scdondemand) のグローバルステージの環境変数 `.magento.env.yaml` ファイル。 変数 SCD_ON_DEMAND は、SCD に関連するその他すべての設定を上書きし、 `~/pub/static` ディレクトリ。

SCD オンデマンド戦略を使用する場合、ホームページなど、要求するページをキャッシュにプリロードすると便利です。 に期待されるページのリストを追加します。 [WARM_UP_PAGES](../environment/variables-post-deploy.md#warmuppages) のデプロイ後ステージの環境変数 `.magento.env.yaml` ファイル。

>[!WARNING]
>
>実稼動環境では SCD のオンデマンド戦略を使用しないでください。

### SCD をスキップしています

静的コンテンツの生成を完全にスキップすることもできます。 を設定できます [SKIP_SCD](../environment/variables-build.md#skipscd) scd に関連するその他の設定を無視する場合のグローバルステージの環境変数。 これは、の既存のコンテンツには影響しません `~/pub/static` ディレクトリ。
