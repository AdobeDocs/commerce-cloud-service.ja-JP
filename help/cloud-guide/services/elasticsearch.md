---
title: Elasticsearchサービスの設定
description: クラウドインフラストラクチャ上でAdobe CommerceのElasticsearchサービスを有効にする方法を説明します。
feature: Cloud, Search, Services
exl-id: ac559cbb-342a-4756-ade5-49eba4827965
source-git-commit: 8147b43b26370d9305c3c7dc47865ddcbae1904d
workflow-type: tm+mt
source-wordcount: '798'
ht-degree: 0%

---

# Elasticsearchサービスの設定

[Elasticsearch](https://www.elastic.co) は、あらゆるソースや形式からデータを取り出し、リアルタイムで検索および視覚化できるオープンソース製品です。

{{elasticsearch-support}}

Adobe Commerceバージョン 2.4.4 以降の場合は、 [OpenSearch サービスを設定する](opensearch.md).

- Elasticsearchは、製品カタログ内の製品に対して、すばやく詳細検索を実行します。
- Elasticsearchアナライザーは複数の言語をサポートしています
- ストップワードと同義語のサポート
- インデックス作成は、インデックス再作成操作が完了するまで顧客に影響を与えません

>[!TIP]
>
>Adobe Commerceアプリケーションにサードパーティの検索ツールを設定する予定がある場合でも、Adobeは、クラウドインフラストラクチャプロジェクト上のAdobe Commerceに対してElasticsearchを常に設定することをお勧めします。 「Elasticsearch」の設定には、サードパーティの検索ツールが失敗した場合に備えたフォールバックオプションが用意されています。

{{service-instruction}}

**Elasticsearchを有効にするには**:

1. スタータープロジェクトの場合は、 `elasticsearch` に対するサービス `.magento/services.yaml` ファイルにElasticsearchのバージョンと割り当て済みディスク領域を MB 単位で指定します。

   ```yaml
   elasticsearch:
       type: elasticsearch:<version>
       disk: 1024
   ```

   Pro プロジェクトの場合、ステージング環境と実稼動環境でElasticsearchのバージョンを変更するには、Adobe Commerceサポートチケットを送信する必要があります。

1. を設定します。 `relationships` プロパティを `.magento.app.yaml` ファイル。

   ```yaml
   relationships:
       elasticsearch: "elasticsearch:elasticsearch"
   ```

1. コードの変更を追加、コミット、およびプッシュします。

   ```bash
   git add .magento/services.yaml .magento.app.yaml && git commit -m "Enable Elasticsearch" && git push origin <branch-name>
   ```

   これらの変更が環境に及ぼす影響について詳しくは、 [サービス](services-yaml.md).

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

## Elasticsearchソフトウェアの互換性

クラウドインフラストラクチャプロジェクトにAdobe Commerceをインストールまたはアップグレードする場合は、Elasticsearchサービスのバージョンと [ElasticsearchPHP](https://github.com/elastic/elasticsearch-php) Adobe Commerce用クライアント。

- **初回のセットアップ** — 指定したElasticsearchバージョンが `services.yaml` ファイルは、Adobe Commerce用に設定されたElasticsearchPHP クライアントと互換性があります。

- **プロジェクトのアップグレード** — 新しいElasticsearchバージョンの PHP クライアントが、クラウドインフラストラクチャにインストールされているElasticsearchサービスバージョンと互換性があることを確認します。

クラウドインフラストラクチャ上のAdobe Commerceのサービスバージョンと互換性のサポートは、クラウドインフラストラクチャ上にデプロイされたバージョンによって決まり、Adobe Commerceオンプレミスデプロイメントでサポートされるバージョンとは異なる場合があります。 詳しくは、 [サービスバージョン](services-yaml.md#service-versions).

**Elasticsearch・ソフトウェアの互換性を確認するには**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. アクティブなElasticsearchの環境の詳細を表示します。

   ```bash
   magento-cloud relationships --property=elasticsearch
   ```

1. または、SSH を使用してリモート環境にログインすることもできます。

   ```bash
   magento-cloud ssh
   ```

1. の Composer パッケージのバージョンを確認します。 `elasticsearch/elasticsearch`.

   ```bash
   composer show elasticsearch/elasticsearch
   ```

   応答で、 `versions` プロパティ。

   ```terminal
   name     : elasticsearch/elasticsearch
   descrip. : PHP Client for Elasticsearch
   keywords : client, elasticsearch, search
   versions : * v7.17.1
   type     : library
   license  : Apache License 2.0 (Apache-2.0) (OSI approved) https://spdx.org/licenses/Apache-2.0.html#licenseText
   license  : GNU Lesser General Public License v2.1 only (LGPL-2.1-only) (OSI approved) https://spdx.org/licenses/LGPL-2.1-only.html#licenseText
   homepage :
   source   : [git] git@github.com:elastic/elasticsearch-php.git f1b8918f411b837ce5f6325e829a73518fd50367
   dist     : [zip] https://api.github.com/repos/elastic/elasticsearch-php/zipball/f1b8918f411b837ce5f6325e829a73518fd50367 f1b8918f411b837ce5f6325e829a73518fd50367
   path     : ~/vendor/elasticsearch/elasticsearch
   names    : elasticsearch/elasticsearch
   ```

   また、PHP クライアントのElasticsearchは、  `composer.lock` ファイルが環境のルートディレクトリにある。

1. コマンドラインから、Elasticsearchサービス接続の詳細を取得します。

   ```bash
   vendor/bin/ece-tools env:config:show services
   ```

   応答で、Elasticsearchサービスエンドポイントの IP アドレスを見つけます。

   ```terminal
   | elasticsearch:                                                                                                  |
   +------------------------------------------+----------------------------------------------------------------------+
   | username                                 | null                                                                 |
   | scheme                                   | http                                                                 |
   | service                                  | elasticsearch                                                        |
   | fragment                                 | null                                                                 |
   | ip                                       | 169.254.220.11                                                       |
   | hostname                                 | dzggu33f75wi3sd24lgwtoupxm.elasticsearch.service._.magentosite.cloud |
   | public                                   | false                                                                |
   | cluster                                  | fo3qdoxtla4j4-master-7rqtwti                                         |
   | host                                     | elasticsearch.internal                                               |
   | rel                                      | elasticsearch                                                        |
   | query                                    |                                                                      |
   | path                                     | null                                                                 |
   | password                                 | null                                                                 |
   | type                                     | elasticsearch:6.5                                                    |
   | port                                     | 9200                                                                 |
   +------------------------------------------+----------------------------------------------------------------------+
   ```

1. インストールされているElasticsearchサービスの取得 `version:number` サービスエンドポイントから。

   ```bash
   curl -XGET <elasticsearch-service-endpoint-ip-address>:9200/
   ```

   ```terminal
   {
      "name" : "-AqGi9D",
      "cluster_name" : "elasticsearch",
      "cluster_uuid" : "_yze6-ywSEW1MaAF8ZPWyQ",
      "version" : {
        "number" : "6.5.4",
        "build_flavor" : "default",
        "build_type" : "deb",
        "build_hash" : "82a8aa7",
        "build_date" : "2019-01-23T12:07:18.760675Z",
        "build_snapshot" : false,
        "lucene_version" : "7.5.0",
        "minimum_wire_compatibility_version" : "5.6.0",
        "minimum_index_compatibility_version" : "5.0.0"
   },
   "  tagline" : "You Know, for Search"
   }
   ```

1. バージョンサービスと PHP クライアント間のElasticsearchの互換性を確認します。

   バージョンに互換性がない場合は、環境設定に対して次のいずれかの更新をおこないます。

   - ElasticsearchPHP クライアントを、Elasticsearchサービスバージョンと互換性のあるバージョンに変更します。

     ```bash
     composer require "elasticsearch/elasticsearch:~<version>"
     ```

   - のElasticsearchサービスのバージョンを変更します。 `services.yaml` ファイルを、ElasticsearchPHP クライアントと互換性のあるバージョンに保存します。

     {{pro-update-service}}

## Elasticsearchサービスを再起動

を再起動する必要がある場合は、 [Elasticsearch](https://www.elastic.co) サービスをご利用の場合は、Adobe Commerceサポートにお問い合わせください。

## 追加の検索設定

- デフォルトでは、クラウド環境の検索設定は、デプロイするたびに再生成されます。 以下を使用すると、 `SEARCH_CONFIGURATION` 変数をデプロイして、デプロイメント間でカスタム検索設定を保持します。 詳しくは、 [変数をデプロイ](../environment/variables-deploy.md#search_configuration).

- プロジェクトのElasticsearchサービスを設定したら、Admin UI を使用してElasticsearch接続をテストし、Adobe CommerceのElasticsearch設定をカスタマイズします。

### Elasticsearchのプラグインを追加

オプションで、Elasticsearchのプラグインを追加するには、 `configuration:plugins` セクションをElasticsearchサービスに追加します。 `.magento/services.yaml` ファイル。 たとえば、次のコードは、ICU 分析と音声分析のプラグインを有効にします。

```yaml
elasticsearch:
    type: elasticsearch:<service-version>
    disk: 1024
    configuration:
        plugins:
            - analysis-icu
            - analysis-phonetic
```

Elastic Suite サードパーティプラグインを使用する場合は、 [を更新します。 `ece-tools` パッケージ](../dev-tools/update-package.md) をバージョン 2002.0.19 以降に変更した場合。
Elastic Suite を設定する際に、 `ELASTICSUITE_CONFIGURATION` 変数をデプロイします。 この設定は、デプロイメント全体で設定を保存します。

### Elasticsearchのプラグインを削除

からのプラグインエントリの削除 `elasticsearch:` in `.magento/services.yaml` では、期待どおりにこれらをアンインストールまたは無効化しません。 Elasticsearchデータのインデックスを再作成する必要があります。 この動作は、これらのプラグインに依存するデータの損失や破損を防ぐために意図されています。

**Elasticsearchプラグインを削除するには**:

1. からElasticsearchプラグインエントリを削除します。 `.magento/services.yaml` ファイル。
1. コードの変更を追加、コミット、およびプッシュします。

   ```bash
   git add .magento/services.yaml
   ```

   ```bash
   git commit -m "Remove Elasticsearch plugin"
   ```

   ```bash
   git push origin <branch-name>
   ```

1. をコミットする `.magento/services.yaml` Cloud リポジトリに対する変更。
1. カタログ検索のインデックスを再作成します。

   ```bash
   bin/magento indexer:reindex catalogsearch_fulltext
   ```

1. キャッシュをクリーンアップします。

   ```bash
   bin/magento cache:clean
   ```

>[!TIP]
>
>Adobe Commerceでの Elastic Suite プラグインの使用またはトラブルシューティングについて詳しくは、 [Elastic Suite ドキュメント](https://github.com/Smile-SA/elasticsuite).

## トラブルシューティング

問題のトラブルシューティングに関するヘルプについては、次のAdobe CommerceサポートElasticsearchを参照してください。

- [Elasticsearch5 が設定されていますが、「Fielddata is disabled...」というエラーが表示された検索ページが読み込まれません。](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/elasticsearch/elasticsearch-5-is-configured-but-search-page-does-not-load-with-fielddata-is-disabled...-error.html)
- [Elasticsearch6.x が使用されている場合、カタログのページネーションが機能しません](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/known-issues-patches-attached/catalog-pagination-doesn-t-work-when-elasticsearch-6.x-is-used.html)
- [Adobe CommerceのElasticsearch](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/elasticsearch/elasticsearch-in-magento-troubleshooter.html)
- [Elasticsearchインデックスのステータス： `yellow` または `red`](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/elasticsearch/elasticsearch-index-status-is-yellow-or-red.html)
