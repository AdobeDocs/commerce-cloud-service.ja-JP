---
title: ルートの設定
description: クラウドインフラストラクチャ環境上のAdobe Commerceに対する受信 HTTPS リクエストのルートを定義する方法について説明します。
feature: Cloud, Configuration, Routes
exl-id: a33797e5-14cc-45eb-a048-96180b872a4a
source-git-commit: 1253d8357fd2554050d1775fefbc420a2097db5f
workflow-type: tm+mt
source-wordcount: '886'
ht-degree: 0%

---

# ルートの設定

The `routes.yaml` ファイルを `.magento/routes.yaml` ディレクトリは、クラウドインフラストラクチャの統合、ステージング、実稼動環境でAdobe Commerceのルートを定義します。 ルートは、アプリケーションが受信する HTTP および HTTPS リクエストを処理する方法を決定します。

デフォルト `routes.yaml` ファイルは、単一のデフォルトドメインを持つプロジェクトと複数のドメイン用に設定されたプロジェクトで、HTTP 要求を HTTPS として処理するためのルートテンプレートを指定します。

```yaml
"http://{default}/":
    type: upstream
    upstream: "mymagento:http"
"http://{all}/":
    type: upstream
    upstream: "mymagento:http"
```

以下を使用します。 `magento-cloud` CLI で、設定されたルートのリストを表示します。

```bash
magento-cloud environment:routes

+-------------------+----------+---------------+
| Route             | Type     | To            |
+-------------------+----------+---------------+
| http://{default}/ | upstream | mymagento     |
+-------------------+----------+---------------+
```

## Pro 環境の設定を更新しました。

{{pro-self-service-warning}}

## ルートテンプレート

The `routes.yaml` file は、テンプレート化されたルートとその設定のリストです。 ルートテンプレートでは、次のプレースホルダを使用できます。

- The `{default}` placeholder は、プロジェクトのデフォルトとして設定された修飾ドメイン名を表します。

  例えば、デフォルトドメインを持つプロジェクト `example.com` と次のルートテンプレートが含まれます。

  ```text
  https://www.{default}/
  https://{default}/blog
  ```

  これらのテンプレートは、実稼動環境では次の URL に解決されます。

  ```text
  https://www.example.com/
  https://example.com/blog
  ```

- The `{all}` placeholder は、プロジェクトに設定されているすべてのドメイン名を表します。

  例えば、 `example.com` および `example1.com` 次のルートテンプレートを持つドメイン：

  ```text
  https://www.{all}/
  
  https://{all}/blog
  ```

  これらのテンプレートは、プロジェクト内のすべてのドメインに対して、次のルートに解決されます。

  ```text
  https://www.example.com/
  
  https://www.example1.com/
  
  https://example.com/blog
  
  https://example1.com/blog
  ```

  The `{all}` プレースホルダーは、複数のドメイン用に設定されたプロジェクトに役立ちます。 実稼動以外のブランチでは、 `{all}` は、各ドメインのプロジェクト ID と環境 ID に置き換えられます。

  プロジェクトにドメインが設定されていない場合（開発時によく使用されるドメイン）、 `{all}` プレースホルダーは、 `{default}` プレースホルダー。

また、Adobe Commerceは、アクティブな統合環境ごとにルートを生成します。 統合環境の場合、 `{default}` プレースホルダーは次のドメイン名に置き換えられます。

```text
[branch]-[per-environment-random-string]-[project-id].[region].magentosite.cloud
```

例えば、 `refactorcss` 分岐 `mswy7hzcuhcjw` でホストされるプロジェクト `us` クラスターは、次のドメインを持ちます。

```text
https://refactorcss-oy3m2pq-mswy7hzcuhcjw.us.magentosite.cloud/
```

>[!NOTE]
>
>Cloud プロジェクトが複数のストアをサポートしている場合は、 [複数の Web サイトまたはストア](../store/multiple-sites.md).

### 末尾のスラッシュ

ルート定義の末尾には、フォルダーまたはディレクトリを示すスラッシュが含まれます。ただし、同じコンテンツを末尾にスラッシュを付けることも、末尾にスラッシュを付けないこともできます。 次の URL は同じものを解決しますが、 _2 つの異なる_ URL:

```text
https://www.example.com/blog/

https://www.example.com/blog
```

>[!TIP]
>
>ディレクトリには末尾のスラッシュを使用することをお勧めしますが、どの方法を選択する場合でも、次の操作が重要です。 **一貫性を保つ** 2 つの場所が生成されるのを避けるため。

## ルートプロトコル

すべての環境で、HTTP と HTTPS の両方が自動的にサポートされます。

- 設定で HTTP ルートのみを指定した場合、HTTPS ルートが自動的に作成され、リダイレクトを必要とせずに HTTP と HTTPS の両方からサイトを提供できます。

  例えば、デフォルトドメインを持つプロジェクト `example.com` と次のルートテンプレート

  ```text
  http://{default}/
  ```

  このテンプレートは次の URL に解決されます。

  ```text
  http://example.com/
  
  https://example.com/
  ```

- 設定で HTTPS ルートのみを指定した場合、すべての HTTP リクエストは HTTPS にリダイレクトされます。

  例えば、デフォルトドメインを持つプロジェクト `example.com` 次のルートテンプレートを使用：

  ```text
  https://{default}/
  ```

  このテンプレートは次の URL に解決されます。

  ```text
  https://example.com/
  ```

  また、次のリダイレクトも処理します。

  `http://example.com/` ==> `https://example.com/`

TLS 経由ですべてのページを提供します。 この設定では、次のいずれかの方法を使用して、TLS に対する暗号化されていないすべての要求に対してリダイレクトを設定する必要があります。

- でプロトコルを HTTPS に変更します。 `routes.yaml` ファイル。

  ```yaml
  "https://{default}/":
      type: upstream
      upstream: "mymagento:http"
  "https://{all}/":
      type: upstream
      upstream: "mymagento:http"
  ```

- ステージング環境と実稼動環境の場合は、 [TLS を Fastly に強制](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/how-to/redirect-http-to-https-for-all-pages-on-cloud-force-tls.html) 」オプションを使用して、Admin UI から選択します。 このオプションを使用すると、Fastly が HTTPS へのリダイレクトを処理するので、 `routes.yaml` 設定。

## ルートオプション

次のプロパティを使用して、各ルートを個別に設定します。

| プロパティ | 説明 |
| ---------------- | ----------- |
| `type: upstream` | アプリケーションを提供します。 また、 `upstream` アプリケーションの名前を指定するプロパティ ( `.magento.app.yaml`) の後に `:http` endpoint. |
| `type: redirect` | 別のルートにリダイレクトします。 その後に、 `to` プロパティ。テンプレートによって識別される別のルートへの HTTP リダイレクトです。 |
| `cache:` | コントロール [ルートのキャッシュ](caching.md). |
| `redirects:` | コントロール [リダイレクトルール](redirects.md). |
| `ssi:` | の有効化を制御 [サーバーサイドのインクルード](server-side-includes.md). |

## 単純なルート

次の例では、ルート設定で apex ドメインと `www` サブドメインを `mymagento` アプリケーション。 このルートは、HTTPS リクエストをリダイレクトしません。

**例 1:**

```yaml
"http://{default}/":
    type: upstream
    upstream: "mymagento:http"

"http://www.{default}/":
    type: redirect
    to: "http://{default}/"
```

この例では、要求ルーティングは次のルールに従います。

- サーバーは、次の URL パターンを使用して、要求に直接応答します。

  ```text
  http://example.com/path
  ```

- サーバーが _301 リダイレクト_ 次の URL パターンを持つ要求の場合：

  ```text
  http://www.example.com/mypath
  ```

  これらのリクエストは、apex ドメインにリダイレクトされます。次に例を示します。

  ```text
  http://example.com/mypath
  ```

次の例では、ルート設定は、www ドメインから apex ドメインに URL をリダイレクトしません。 代わりに、 www ドメインと apex ドメインの両方からリクエストが提供されます。

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

Adobe Commerce on cloud infrastructure は、ワイルドカードルートをサポートしているので、複数のサブドメインを同じアプリケーションにマッピングできます。 これは、リダイレクトルートとアップストリームルートで機能します。 ルートの先頭にアスタリスク (\*) を付けます。 例えば、次のルートは同じアプリケーションに送信されます。

- `*.example.com`
- `www.example.com`
- `blog.example.com`
- `us.example.com`

これは、実稼働環境の包括的ドメインとして機能します。

### マッピングされていないドメインのルーティング

ドット (`.`) でサブドメインを区切ります。

**例：**

を含むプロジェクト `add-theme` 分岐ルートは次の URL になります。

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

ドットとルートが解決される前に、任意のサブドメインを挿入できます。

**例：**

次のルートテンプレートを定義します。

```text
http://*.{default}/
```

このテンプレートは、次の両方の URL を解決します。

```text
http://foo.add-theme-projectID.us.magentosite.cloud/
http://bar.add-theme-projectID.us.magentosite.cloud/
```

環境への SSH 接続を確立し、 `magento-cloud` CLI を使用してルートを一覧表示します。

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

詳しくは、 [リダイレクト](redirects.md)を使用すると、次のような複雑なリダイレクトルールを管理できます。 _部分的なリダイレクト_、およびルートベースのルールを指定します。 [caching](caching.md):

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
