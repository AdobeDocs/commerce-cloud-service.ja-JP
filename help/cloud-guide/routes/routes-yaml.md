---
title: ルートの設定
description: クラウドインフラストラクチャ環境でAdobe Commerceの受信 HTTPS リクエストのルートを定義する方法について説明します。
feature: Cloud, Configuration, Routes
exl-id: a33797e5-14cc-45eb-a048-96180b872a4a
source-git-commit: 1253d8357fd2554050d1775fefbc420a2097db5f
workflow-type: tm+mt
source-wordcount: '886'
ht-degree: 0%

---

# ルートの設定

この `routes.yaml` 内のファイル `.magento/routes.yaml` ディレクトリは、クラウドインフラストラクチャ統合、ステージングおよび実稼動環境でのAdobe Commerceのルートを定義します。 ルートは、受信する HTTP リクエストと HTTPS リクエストをアプリケーションがどのように処理するかを決定します。

デフォルト `routes.yaml` ファイルは、単一のデフォルトドメインを持つプロジェクトと、複数のドメインに対して設定されたプロジェクトで HTTP リクエストを HTTPS として処理するためのルートテンプレートを指定します。

```yaml
"http://{default}/":
    type: upstream
    upstream: "mymagento:http"
"http://{all}/":
    type: upstream
    upstream: "mymagento:http"
```

の使用 `magento-cloud` CLI を使用して設定済みルートのリストを表示：

```bash
magento-cloud environment:routes

+-------------------+----------+---------------+
| Route             | Type     | To            |
+-------------------+----------+---------------+
| http://{default}/ | upstream | mymagento     |
+-------------------+----------+---------------+
```

## Pro 環境の設定アップデート

{{pro-self-service-warning}}

## ルートテンプレート

この `routes.yaml` ファイルは、テンプレート化されたルートとその設定のリストです。 ルートテンプレートでは、以下のプレースホルダーを使用できます。

- この `{default}` プレースホルダーは、プロジェクトのデフォルトとして設定された修飾ドメイン名を表します。

  例えば、デフォルトのドメインを持つプロジェクトの場合 `example.com` および次のルートテンプレート：

  ```text
  https://www.{default}/
  https://{default}/blog
  ```

  これらのテンプレートは、実稼動環境では次の URL に解決されます。

  ```text
  https://www.example.com/
  https://example.com/blog
  ```

- この `{all}` プレースホルダーには、プロジェクト用に設定されたすべてのドメイン名が表示されます。

  例えば、を使用したプロジェクトの場合 `example.com` および `example1.com` 次のルートテンプレートを持つドメイン：

  ```text
  https://www.{all}/
  
  https://{all}/blog
  ```

  これらのテンプレートは、プロジェクト内のすべてのドメインの次のルートに解決されます。

  ```text
  https://www.example.com/
  
  https://www.example1.com/
  
  https://example.com/blog
  
  https://example1.com/blog
  ```

  この `{all}` プレースホルダーは、複数のドメイン用に設定されたプロジェクトで役立ちます。 非実稼動ブランチでは、 `{all}` は、各ドメインのプロジェクト ID と環境 ID に置き換えられます。

  プロジェクトにドメインが設定されていない場合（開発時によく発生する）、 `{all}` プレースホルダーは、と同じように動作します。 `{default}` プレースホルダー。

Adobe Commerceは、アクティブな統合環境ごとにルートも生成します。 統合環境の場合、 `{default}` プレースホルダーが次のドメイン名に置き換えられます。

```text
[branch]-[per-environment-random-string]-[project-id].[region].magentosite.cloud
```

例： `refactorcss` 分岐： `mswy7hzcuhcjw` でホストされているプロジェクト `us` クラスターは次のドメインを持っています：

```text
https://refactorcss-oy3m2pq-mswy7hzcuhcjw.us.magentosite.cloud/
```

>[!NOTE]
>
>クラウドプロジェクトが複数のストアをサポートする場合は、のルート設定手順に従います。 [複数の web サイトまたはストア](../store/multiple-sites.md).

### 末尾のスラッシュ

ルート定義には、フォルダーまたはディレクトリを示す末尾のスラッシュが含まれています。ただし、末尾のスラッシュの有無にかかわらず、同じコンテンツを提供できます。 次の URL は同じように解決されますが、と解釈できます。 _2 つの異なる_ URL:

```text
https://www.example.com/blog/

https://www.example.com/blog
```

>[!TIP]
>
>ディレクトリには末尾のスラッシュを使用することをお勧めしますが、どの方法を選択した場合でも、次のことが重要です **一貫性を保つ** 2 つの場所の生成を避けるために使用します。

## ルート プロトコル

すべての環境で、HTTP と HTTPS の両方を自動的にサポートしています。

- 設定で HTTP ルートのみが指定されている場合、HTTPS ルートが自動的に作成され、リダイレクトを必要とせずにサイトが HTTP と HTTPS の両方から提供されます。

  例えば、デフォルトのドメインを持つプロジェクトの場合 `example.com` と次のルートテンプレート：

  ```text
  http://{default}/
  ```

  このテンプレートは次の URL に解決されます。

  ```text
  http://example.com/
  
  https://example.com/
  ```

- 設定で HTTPS ルートのみが指定されている場合、すべての HTTP リクエストは HTTPS にリダイレクトされます。

  例えば、デフォルトのドメインを持つプロジェクトの場合 `example.com` 次のルートテンプレートを使用します。

  ```text
  https://{default}/
  ```

  このテンプレートは次の URL に解決されます。

  ```text
  https://example.com/
  ```

  また、次のリダイレクトも処理します。

  `http://example.com/` ==> `https://example.com/`

TLS 経由ですべてのページを提供します。 この設定では、次のいずれかの方法を使用して、暗号化されていないすべてのリクエストを TLS と同等の値にリダイレクトするように設定する必要があります。

- でプロトコルを HTTPS に変更します `routes.yaml` ファイル。

  ```yaml
  "https://{default}/":
      type: upstream
      upstream: "mymagento:http"
  "https://{all}/":
      type: upstream
      upstream: "mymagento:http"
  ```

- ステージング環境と実稼動環境では、を有効にします [Fastly で TLS を強制](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/how-to/redirect-http-to-https-for-all-pages-on-cloud-force-tls.html) 管理 UI の「」オプション。 このオプションを使用すると、Fastly は HTTPS へのリダイレクトを処理するので、 `routes.yaml` 設定。

## ルートオプション

次のプロパティを使用して、各ルートを個別に設定します。

| プロパティ | 説明 |
| ---------------- | ----------- |
| `type: upstream` | アプリケーションを提供します。 また、次のものがあります `upstream` アプリケーションの名前を指定するプロパティ （で定義） `.magento.app.yaml`）に続いて、 `:http` エンドポイント。 |
| `type: redirect` | 別のルートにリダイレクトします。 この後に `to` プロパティ：テンプレートで識別される別のルートへの HTTP リダイレクト。 |
| `cache:` | コントロール [ルートのキャッシュ](caching.md). |
| `redirects:` | コントロール [リダイレクトルール](redirects.md). |
| `ssi:` | の有効化を制御 [サーバーサイドインクルード](server-side-includes.md). |

## シンプルなルート

次の例では、ルート設定は apex ドメインと `www` サブドメインをに `mymagento` アプリケーション。 このルートでは、HTTPS リクエストはリダイレクトされません。

**例 1:**

```yaml
"http://{default}/":
    type: upstream
    upstream: "mymagento:http"

"http://www.{default}/":
    type: redirect
    to: "http://{default}/"
```

この例では、リクエストルーティングは次のルールに従います。

- サーバーは、次の URL パターンを使用してリクエストに直接応答します。

  ```text
  http://example.com/path
  ```

- サーバーが _301 リダイレクト_ 以下の URL パターンを持つリクエストの場合：

  ```text
  http://www.example.com/mypath
  ```

  これらのリクエストは、apex ドメインにリダイレクトされます。例：

  ```text
  http://example.com/mypath
  ```

次の例では、ルート設定で www ドメインから apex ドメインに URL がリダイレクトされません。 代わりに、リクエストは www ドメインと apex ドメインの両方から提供されます。

**例 2:**

```yaml
"http://{default}/":
    type: upstream
    upstream: "mymagento:http"

"http://www.{default}/":
    type: upstream
    upstream: "mymagento:http"
```

## ワイルドカードルート

クラウドインフラストラクチャー上のAdobe Commerceでは、ワイルドカードルートをサポートしているので、複数のサブドメインを同じアプリケーションにマッピングできます。 これは、リダイレクト ルートとアップストリーム ルートで機能します。 ルートの前にはアスタリスク（\*）を付けます。 例えば、同じアプリケーションに対して次のようなルートがあります。

- `*.example.com`
- `www.example.com`
- `blog.example.com`
- `us.example.com`

これは、ライブ環境ではキャッチオール ドメインとして機能します。

### マッピングされていないドメインのルーティング

ドット（`.`）を選択して、サブドメインを区切ります。

**例：**

を使用したプロジェクト `add-theme` ブランチは次の URL にルーティングされます。

```text
http://add-theme-projectID.us.magento.com/
```

次のルートテンプレートを定義する場合：

```text
http://www.{default}/
```

ルートは次の URL に解決されます。

```text
http://www.add-theme-projectID.us.magento.com/
```

ドットとルートが解決される前に任意のサブドメインを挿入できます。

**例：**

次のルートテンプレートを定義します。

```text
http://*.{default}/
```

このテンプレートは次の両方の URL を解決します。

```text
http://foo.add-theme-projectID.us.magentosite.cloud/
http://bar.add-theme-projectID.us.magentosite.cloud/
```

マッピングされていないドメインのルートパターンは、環境への SSH 接続を確立し、を使用することで確認できます `magento-cloud` CLI を使用してルートを一覧表示します。

```terminal
web@mymagento.0:~$ vendor/bin/ece-tools env:config:show routes

Magento Cloud Routes:
+------------------------------------------+--------------------------------------------------------------+
| Route configuration                      | Value                                                        |
+------------------------------------------+--------------------------------------------------------------+
| http://www.add-theme-projectID.us.magento.com/:                                                         |
+------------------------------------------+--------------------------------------------------------------+
| primary                                  | false                                                        |
| id                                       | null                                                         |
| attributes                               |                                                              |
| type                                     | upstream                                                     |
| to                                       | mymagento                                                    |
| original_url                             | https:/{default}/                                            |
+------------------------------------------+--------------------------------------------------------------+
| https://*.add-theme-projectID.us.magentosite.cloud/:                                                    |
+------------------------------------------+--------------------------------------------------------------+
| primary                                  | false                                                        |
| id                                       | null                                                         |
| attributes                               |                                                              |
| type                                     | upstream                                                     |
| to                                       | mymagento                                                    |
| original_url                             | https://*.{default}/                                         |
+------------------------------------------+--------------------------------------------------------------+
```

## リダイレクトとキャッシュ

で詳しく説明されているように [リダイレクト](redirects.md)では、次のような複雑なリダイレクトルールを管理できます _部分的なリダイレクト_、およびルートベースのルールの指定 [キャッシュ](caching.md):

```yaml
https://www.{default}/:
    type: redirect
    to: https://{default}/
https://{default}/:
    cache:
        cookies: [""]
        default_ttl: 0
        enabled: true
        headers:
            - Accept
            - Accept-Language
    ssi:
        enabled: false
    type: upstream
    upstream: mymagento:http
```
