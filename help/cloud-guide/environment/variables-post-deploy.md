---
title: デプロイ後変数
description: Adobe Commerce on cloud infrastructure のデプロイ後のフェーズで、アクションを制御する環境変数のリストを参照してください。
feature: Cloud, Configuration, Cache
recommendations: noDisplay, catalog
role: Developer
exl-id: e460335f-cd2b-4c98-b1ff-32504599b33d
source-git-commit: 8b02757591c4e8f607e936de4eda74d76953d9b7
workflow-type: tm+mt
source-wordcount: '511'
ht-degree: 0%

---

# デプロイ後変数

次の _デプロイ後_ 変数は、デプロイ後のフェーズのアクションを制御し、の値を継承および上書きできます。 [グローバル変数](variables-global.md). これらの変数を `post-deploy` ステージ `.magento.env.yaml` ファイル：

```yaml
stage:
  post-deploy:
    POST-DEPLOY_VARIABLE_NAME: value
```

ビルドおよびデプロイプロセスのカスタマイズに関する詳細情報：

- [デプロイメント設定](configure-env-yaml.md)
- [デプロイメントプロセス](../deploy/process.md)

## `TTFB_TESTED_PAGES`

- **デフォルト**— `[]` （空の配列）
- **バージョン**—Adobe Commerce 2.1.4 以降

設定 _最初のバイトまでの時間_ （TTFB）指定したページをテストして、サイトのパフォーマンスをテストします。 テストが必要な各ページに対して、絶対パス参照、またはプロトコルとホストを含む URL を指定します。

```yaml
stage:
  post-deploy:
    TTFB_TESTED_PAGES:
       - "index.php"
       - "index.php/customer/account/create"
       - "https://example.com/catalog/some-category"
```

変更をテストしてコミットするページを指定した後、 _最初のバイトまでの時間_ テストはデプロイ後のフェーズで実行され、各パスの結果をクラウドログに投稿します。

```terminal
[2019-06-20 20:42:22] INFO: TTFB test result: 0.313s {"url":"https://staging-tkyicst-xkmwgjkwmwfuk.us-4.magentosite.cloud/customer/account/create","status":200}
[2019-06-20 20:42:22] INFO: TTFB test result: 0.408s {"url":"https://staging-tkyicst-xkmwgjkwmwfuk.us-4.magentosite.cloud/checkout/cart","status":200}
```

リダイレクトされたパスの場合、ログには、環境変数で設定されたパスではなく、リダイレクトターゲットのパスが報告されます。 無効なパスを指定すると、ログに警告メッセージが表示されます。

## `WARM_UP_CONCURRENCY`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

サーバー負荷を軽減するために、キャッシュウォームアップ操作中に送信する同時リクエストの制限を指定します。 この値は、並列接続の数を制限し、 `WARM_UP_PAGES` デプロイ後変数は、キャッシュのプリロードに複数のページを指定します。

```yaml
stage:
  post-deploy:
    WARM_UP_CONCURRENCY: 4
```

## `WARM_UP_PAGES`

- **デフォルト**— `index.php`
- **バージョン**—Adobe Commerce 2.1.4 以降

内のキャッシュのプリロードに使用するページのリストのカスタマイズ `post_deploy` ステージ。 デプロイ後フックを設定する必要があります。 を参照してください。 [フック セクション](../application/hooks-property.md) の `.magento.app.yaml` ファイル。

- **単一ページ**- キャッシュに追加する単一ページを指定します。 デフォルトのベース URL を指定する必要はありません。 次の例では、をキャッシュします `BASE_URL/index.php` ページ：

  ```yaml
  stage:
    post-deploy:
      WARM_UP_PAGES:
        - "index.php"
  ```

- **複数のドメイン** – 複数の URL をリストします。 次の例では、2 つのドメインからページをキャッシュします。

  ```yaml
  stage:
    post-deploy:
      WARM_UP_PAGES:
        - 'http://example1.com/test'
        - 'http://example2.com/test'
  ```

- **複数ページ** – 特定の正規表現パターンに従って複数のページをキャッシュするには、次の形式を使用します。

  ```terminal
  <entity_type>:<pattern|url|product_sku>:<store_id|store_code>
  ```

   - `entity_type`：考えられるバリアント `category`, `cms-page`, `product`, `store-page`
   - `pattern|url|product_sku`：を使用します `regexp` パターンまたは完全一致 `url` url をフィルタリングするか、すべてのページにアスタリスク（\*）を使用します。 次に対して製品 SKU を使用： `product` エンティティタイプ
   - `store_id|store_code`：ストアの ID またはコード、またはすべてのストアのアスタリスク（\*）を使用します。複数のストア ID またはコードをで区切って渡すことができます `|`

  次の例では、をキャッシュします `category` および `cms-page` 次の条件に基づいたエンティティタイプ：
   - id を持つストアのすべてのカテゴリ ページ `1`
   - コードが含まれるストアのすべてのカテゴリ ページ `store1` および `store2`
   - カテゴリページ `cars` コードを含んだストアの場合 `store_en`
   - cms ページ `contact` すべてのストア用
   - cms ページ `contact` ID を持つストアの場合 `1` および `2`
   - を含むカテゴリページ `car_` で終わる `html` ID 2 のストアの場合
   - を含むカテゴリページ `tires_` コードを含んだストアの場合 `store_gb`

     ```yaml
     stage:
       post-deploy:
         WARM_UP_PAGES:
           - "category:*:1"
           - "category:*:store1|store2"
           - "category:cars:store_en"
           - "cms-page:contact:*"
           - "cms-page:contact:1|2"
           - "category:|car_.*?\\.html$|:2"
           - "category:|tires_.*|:store_gb"
     ```

  次の例では、をキャッシュします `product` 次の条件に基づいたエンティティタイプ：
   - すべてのストアのすべての製品（パフォーマンスの問題を回避するために、プログラムによってストアあたり 100 個に制限）
   - 店舗用のすべての製品 `store1`
   - を使用した製品 `sku1` すべてのストア用
   - を使用した製品 `sku1` コードを含むストアの場合 `store1` および `store2`
   - を使用した製品 `sku1`, `sku2` および `sku3` コードを含むストアの場合 `store1` および `store2`

     ```yaml
     stage:
       post-deploy:
         WARM_UP_PAGES:
           - "product:*:*"
           - "product:*:store1"
           - "product:sku1:*"
           - "product:sku1:store1|store2"
           - "product:sku1|sku2|sku3:store1|store2"
     ```

  次の例では、をキャッシュします `store-page` 次の条件に基づいたエンティティタイプ：
   - ページ `/contact-us` すべてのストア用
   - ページ `/contact-us` ID を持つストアの場合 `1`
   - ページ `/contact-us` コードを含むストアの場合 `code1` および `code2`

  ```yaml
        stage:
          post-deploy:
            WARM_UP_PAGES:
              - "store-page:/contact-us:*"
              - "store-page:/contact-us:1"
              - "store-page:/contact-us:code1|code2"
  ```
