---
title: デプロイ後の変数
description: デプロイ後のクラウドインフラストラクチャに関するAdobe Commerceのアクションを制御する環境変数の一覧を参照してください。
feature: Cloud, Configuration, Cache
recommendations: noDisplay, catalog
role: Developer
exl-id: e460335f-cd2b-4c98-b1ff-32504599b33d
source-git-commit: 8b02757591c4e8f607e936de4eda74d76953d9b7
workflow-type: tm+mt
source-wordcount: '511'
ht-degree: 0%

---

# デプロイ後の変数

次の _デプロイ後_ 変数は、デプロイ後の段階でのアクションを制御し、 [グローバル変数](variables-global.md). 次の変数を `post-deploy` 段階 `.magento.env.yaml` ファイル：

```yaml
stage:
  post-deploy:
    POST-DEPLOY_VARIABLE_NAME: value
```

ビルドおよびデプロイプロセスのカスタマイズについて詳しくは、次の手順を参照してください。

- [デプロイメント設定](configure-env-yaml.md)
- [デプロイメントプロセス](../deploy/process.md)

## `TTFB_TESTED_PAGES`

- **デフォルト**— `[]` （空の配列）
- **バージョン**—Adobe Commerce 2.1.4 以降

設定 _最初のバイトまでの時間_ (TTFB) 指定したページのテストを使用して、サイトのパフォーマンスをテストできます。 テストが必要なページごとに、絶対パス参照またはプロトコルとホストを含む URL を指定します。

```yaml
stage:
  post-deploy:
    TTFB_TESTED_PAGES:
       - "index.php"
       - "index.php/customer/account/create"
       - "https://example.com/catalog/some-category"
```

変更をテストおよびコミットするページを指定した後、 _最初のバイトまでの時間_ のテストはデプロイ後の段階で実行され、クラウドログの各パスの結果が次のように投稿されます。

```terminal
[2019-06-20 20:42:22] INFO: TTFB test result: 0.313s {"url":"https://staging-tkyicst-xkmwgjkwmwfuk.us-4.magentosite.cloud/customer/account/create","status":200}
[2019-06-20 20:42:22] INFO: TTFB test result: 0.408s {"url":"https://staging-tkyicst-xkmwgjkwmwfuk.us-4.magentosite.cloud/checkout/cart","status":200}
```

リダイレクトされたパスの場合、ログは、環境変数で設定されたパスではなく、リダイレクトターゲットのパスを報告します。 無効なパスを指定すると、ログに警告メッセージが表示されます。

## `WARM_UP_CONCURRENCY`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

キャッシュのウォームアップ操作中に送信する同時要求の制限を指定して、サーバーの負荷を軽減します。 この値は、並列接続の数を制限し、 `WARM_UP_PAGES` post-deploy 変数は、キャッシュのプリロード用に複数のページを指定します。

```yaml
stage:
  post-deploy:
    WARM_UP_CONCURRENCY: 4
```

## `WARM_UP_PAGES`

- **デフォルト**— `index.php`
- **バージョン**—Adobe Commerce 2.1.4 以降

キャッシュをプリロードするために使用するページのリストをカスタマイズします ( `post_deploy` ステージ。 デプロイ後のフックを設定する必要があります。 詳しくは、 [フック部](../application/hooks-property.md) の `.magento.app.yaml` ファイル。

- **単一ページ** — キャッシュに追加する単一のページを指定します。 デフォルトのベース URL を指定する必要はありません。 次の例では、 `BASE_URL/index.php` ページ：

  ```yaml
  stage:
    post-deploy:
      WARM_UP_PAGES:
        - "index.php"
  ```

- **複数のドメイン** — 複数の URL をリストします。 次の例では、2 つのドメインのページをキャッシュします。

  ```yaml
  stage:
    post-deploy:
      WARM_UP_PAGES:
        - 'http://example1.com/test'
        - 'http://example2.com/test'
  ```

- **複数ページ** — 次の形式を使用して、特定の正規表現パターンに従って複数のページをキャッシュします。

  ```terminal
  <entity_type>:<pattern|url|product_sku>:<store_id|store_code>
  ```

   - `entity_type`：可能なバリアント `category`, `cms-page`, `product`, `store-page`
   - `pattern|url|product_sku`：を使用します `regexp` pattern または完全一致 `url` をクリックして URL をフィルターするか、すべてのページにアスタリスク (\*) を使用します。 製品 sku を `product` エンティティタイプ
   - `store_id|store_code`：ストアの ID またはコード、またはすべてのストアのアスタリスク (\*) を使用すると、複数のストア ID またはコードをで区切って渡すことができます。 `|`

  次の例では、のキャッシュを `category` および `cms-page` 次の条件に基づくエンティティタイプ：
   - ID を持つストアのすべてのカテゴリページ `1`
   - コード付き店舗のすべてのカテゴリページ `store1` および `store2`
   - カテゴリページ `cars` コード付きのストア用 `store_en`
   - cms ページ `contact` すべての店舗で
   - cms ページ `contact` （ID を持つストアの場合） `1` および `2`
   - カテゴリページ `car_` およびで終わる `html` （ID 2 のストア用）
   - カテゴリページ `tires_` コード付きのストア用 `store_gb`

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

  次の例では、 `product` 次の条件に基づくエンティティタイプ：
   - すべてのストアのすべての製品（パフォーマンスの問題を回避するため、プログラムではストアあたり 100 に制限）
   - すべての製品を保存する `store1`
   - 製品と `sku1` すべての店舗で
   - 製品と `sku1` （コード付き店舗用） `store1` および `store2`
   - 製品と `sku1`, `sku2` および `sku3` （コード付き店舗用） `store1` および `store2`

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

  次の例では、 `store-page` 次の条件に基づくエンティティタイプ：
   - ページ `/contact-us` すべての店舗で
   - ページ `/contact-us` ID を持つストアの場合 `1`
   - ページ `/contact-us` （コード付き店舗用） `code1` および `code2`

  ```yaml
        stage:
          post-deploy:
            WARM_UP_PAGES:
              - "store-page:/contact-us:*"
              - "store-page:/contact-us:1"
              - "store-page:/contact-us:code1|code2"
  ```
