---
title: OpenSearch サービスを設定する
description: クラウドインフラストラクチャ上でAdobe Commerceの OpenSearch サービスを有効にする方法を説明します。
feature: Cloud, Search, Services
exl-id: 10dc6367-3f90-4ab6-a84e-15e8c3b32a38
source-git-commit: d4c36b084094846cfad69adc2bffd567a58fab26
workflow-type: tm+mt
source-wordcount: '651'
ht-degree: 0%

---

# OpenSearch サービスを設定する

The [OpenSearch](https://www.opensearch.org) サービスは、Elasticsearch7.10.2のオープンソースのフォークで、Elasticsearchのライセンス変更に従っています。 詳しくは、 [OpenSource プロジェクト](https://github.com/opensearch-project) （GitHub 内）を参照してください。

{{elasticsearch-support}}

OpenSearch を使用すると、あらゆるソースや形式からデータを取り出し、リアルタイムでデータを検索および視覚化できます。

- 商品カタログ内の商品に関するクイック検索と詳細検索
- OpenSearch アナライザーは複数の言語をサポートしています
- ストップワードと同義語のサポート
- インデックス作成は、インデックス再作成操作が完了するまで顧客に影響を与えません

{{service-instruction}}

>[!TIP]
>
>Adobe Commerceアプリケーションにサードパーティの検索ツールを設定する予定がある場合でも、Adobeでは、クラウドインフラストラクチャプロジェクト上でAdobe Commerceに対して OpenSearch を常に設定することをお勧めします。 OpenSearch の設定は、サードパーティの検索ツールが失敗した場合にフォールバックオプションを提供します。

**OpenSearch を有効にするには**:

1. Starter と Pro の統合環境の場合、 `opensearch` に対するサービス `.magento/services.yaml` ファイルに、適切なバージョンと割り当て済みのディスク領域を MB 単位で指定します。 この場合、バージョン 2 が適切です。 クラウドインフラストラクチャは最新バージョンの OpenSearch を使用するので、マイナーバージョンは必要ありません。

   ```yaml
   opensearch:
       type: opensearch:2
       disk: 1024
   ```

   Pro プロジェクトの場合、 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) をクリックして、ステージング環境と実稼動環境で OpenSearch のバージョンを変更します。

1. を設定または検証します。 `relationships` プロパティを `.magento.app.yaml` ファイル。

   ```yaml
   relationships:
       opensearch: "opensearch:opensearch"
   ```

1. コードの変更を追加、コミット、およびプッシュします。

   ```bash
   git add .magento/services.yaml .magento.app.yaml
   ```

   ```bash
   git commit -m "Enable OpenSearch"
   ```

   ```bash
   git push origin <branch-name>
   ```

   これらの変更が環境に及ぼす影響について詳しくは、 [サービスの設定](services-yaml.md).

1. デプロイメントプロセスが完了したら、SSH を使用してリモート環境にログインします。

   ```bash
   magento-cloud ssh
   ```

1. カタログ検索のインデックスを再作成します。

   ```bash
   bin/magento indexer:reindex catalogsearch_fulltext
   ```

1. キャッシュをクリーンアップします。

   ```bash
   bin/magento cache:clean
   ```

{{service-change-tip}}

## OpenSearch ソフトウェアの互換性

クラウドインフラストラクチャプロジェクトにAdobe Commerceをインストールまたはアップグレードする場合は、OpenSearch サービスのバージョンと [OpenSearch PHP](https://github.com/opensearch-project/opensearch-php) Adobe Commerce用クライアント。

- **初回のセットアップ**- `services.yaml` ファイルは、Adobe Commerce用に設定された OpenSearch PHP クライアントと互換性があります。

- **プロジェクトのアップグレード** — 新しいアプリケーションバージョンの OpenSearch PHP クライアントが、クラウドインフラストラクチャにインストールされている OpenSearch サービスバージョンと互換性があることを確認します。

サービスのバージョンと互換性のサポートは、クラウドインフラストラクチャでテストおよび導入されたバージョンによって決まり、Adobe Commerceオンプレミスでの導入でサポートされるバージョンとは異なる場合があります。 詳しくは、 [必要システム構成](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/system-requirements.html) （内） _インストールガイド_ を参照してください。

**OpenSearch ソフトウェアの互換性を検証するには**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. アクティブな環境の OpenSearch の詳細を表示します。

   ```bash
   magento-cloud relationships --property=opensearch
   ```

1. または、SSH を使用してリモート環境にログインすることもできます。

   ```bash
   magento-cloud ssh
   ```

1. OpenSearch サービス接続の詳細を取得します。

   ```bash
   vendor/bin/ece-tools env:config:show services
   ```

   応答で、OpenSearch サービスエンドポイントの IP アドレスとポートを探します。

   ```terminal
   +------------------------------------------+--------------------------------------------------------+
   | opensearch:                                                                                       |
   +------------------------------------------+--------------------------------------------------------+
   | username                                 | null                                                   |
   | scheme                                   | http                                                   |
   | service                                  | opensearch                                             |
   | fragment                                 | null                                                   |
   | ip                                       | 169.254.220.11                                         |
   | hostname                                 | hostf75wi3sd24l.opensearch.service._.magentosite.cloud |
   | port                                     | 9200                                                   |
   | cluster                                  | projectID-develop-4ranwui                              |
   | host                                     | opensearch.internal                                    |
   | rel                                      | opensearch                                             |
   | path                                     | null                                                   |
   | query                                    |                                                        |
   | password                                 | null                                                   |
   | type                                     | opensearch:2                                           |
   | public                                   | false                                                  |
   | host_mapped                              | false                                                  |
   ```

1. インストールされている OpenSearch サービスを取得する `version:number` サービスエンドポイントから。

   ```bash
   curl -XGET <opensearch-service-endpoint-ip-address>:9200
   ```

   ```terminal
   {
      "name" : "opensearch.0",
      "cluster_name" : "opensearch",
      "cluster_uuid" : "_yzaae6-ywSEW1MaAF8ZPWyQ",
      "version" : {
        "distribution" : "opensearch",
        "number" : "2.5.0",
        "build_type" : "deb",
        "build_hash" : "aaaaaaa",
        "build_date" : "2023-01-23T12:07:18.760675Z",
        "build_snapshot" : false,
        "lucene_version" : "9.4.2",
        "minimum_wire_compatibility_version" : "7.10.0",
        "minimum_index_compatibility_version" : "7.0.0"
   },
   "tagline" : "The OpenSearch Project: https://opensearch.org/"
   }
   ```

{{pro-update-service}}

## OpenSearch サービスを再起動します。

OpenSearch サービスを再起動する必要がある場合は、Adobe Commerceサポートにお問い合わせください。

## 追加の検索設定

- デフォルトでは、クラウド環境の検索設定は、デプロイするたびに再生成されます。 以下を使用すると、 `SEARCH_CONFIGURATION` 変数をデプロイして、デプロイメント間でカスタム検索設定を保持します。 詳しくは、 [変数をデプロイ](../environment/variables-deploy.md#search_configuration).

- プロジェクトの OpenSearch サービスを設定した後、管理 UI を使用して OpenSearch 接続をテストし、Adobe Commerceの OpenSearch 設定をカスタマイズします。

### OpenSearch のプラグインを追加

オプションで、OpenSearch 用のプラグインを追加するには、 `configuration:plugins` セクションを `.magento/services.yaml` ファイル。 たとえば、次のコードは、ICU 分析と音声分析のプラグインを有効にします。

```yaml
opensearch:
    type: opensearch:2
    disk: 1024
    configuration:
        plugins:
            - analysis-icu
            - analysis-phonetic
```

詳しくは、 [OpenSearch プロジェクト](https://github.com/opensearch-project) プラグインについて詳しくは、を参照してください。

### OpenSearch のプラグインを削除する

からのプラグインエントリの削除 `opensearch:` のセクション `.magento/services.yaml` ファイル **not** サービスをアンインストールまたは無効にします。 サービスを完全に無効にするには、プラグインを `.magento/services.yaml` ファイル。 この設計により、これらのプラグインに依存するデータの損失や破損を防ぐことができます。

**OpenSearch プラグインを削除するには、以下を実行します。**:

1. 次の場所から OpenSearch プラグインエントリを削除します： `.magento/services.yaml` ファイル。
1. コードの変更を追加、コミット、およびプッシュします。

   ```bash
   git add .magento/services.yaml
   ```

   ```bash
   git commit -m "Remove OpenSearch plugin"
   ```

   ```bash
   git push origin <branch-name>
   ```

1. をコミットする `.magento/services.yaml` クラウドリポジトリに対する変更。
1. カタログ検索のインデックスを再作成します。

   ```bash
   bin/magento indexer:reindex catalogsearch_fulltext
   ```

1. キャッシュをクリーンアップします。

   ```bash
   bin/magento cache:clean
   ```
