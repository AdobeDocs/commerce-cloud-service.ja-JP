---
title: キャッシュ
description: クラウドインフラストラクチャ環境でAdobe Commerceのキャッシュを有効にする方法を説明します。
feature: Cloud, Cache, Routes
exl-id: 4856aa94-2947-4dc8-b0d1-0960869dc39c
source-git-commit: 7b9c6a4cd17069c25455195bd8f273664b8a29eb
workflow-type: tm+mt
source-wordcount: '378'
ht-degree: 0%

---

# キャッシュ

クラウドインフラストラクチャプロジェクト環境でキャッシュを有効にすることができます。 キャッシュを無効にした場合、Adobe Commerceはファイルを直接提供します。

{{route-placeholder}}

## キャッシュの設定

でキャッシュルールを設定して、アプリケーションのキャッシュを有効にします。 `.magento/routes.yaml` ファイルの内容は次のとおりです。

```yaml
http://{default}/:
    type: upstream
    upstream: php:php
    cache:
        enabled: true
        headers: [ "Accept", "Accept-Language", "X-Language-Locale" ]
        cookies: ["*"]
        default_ttl: 60
```

## ルートベースのキャッシュ

次の例に示すように、複数のルートのキャッシュルールを別々に設定して、詳細なキャッシュを有効にします。

```yaml
http://{default}/:
    type: upstream
    upstream: php:php
    cache:
        enabled: true

http://{default}/path/:
    type: upstream
    upstream: php:php
    cache:
        enabled: false

http://{default}/path/more/:
    type: upstream
    upstream: php:php
    cache:
        enabled: true
```

上記の例では、次のルートをキャッシュします。

- `http://{default}/`
- `http://{default}/path/more/`
- `http://{default}/path/more/etc/`

次のルートがあります **ではない** キャッシュ：

- `http://{default}/path/`
- `http://{default}/path/etc/`

>[!NOTE]
>
>ルートの正規表現は次のとおりです **ではない** サポート。

## キャッシュ時間

キャッシュ時間は、 `Cache-Control` 応答ヘッダー値。 ない場合 `Cache-Control` ヘッダーが応答にある場合、 `default_ttl` キーが使用されます。

## キャッシュキー

レスポンスをキャッシュする方法を決定するために、Adobe Commerceは、いくつかの要因に依存するキャッシュキーを作成し、そのキーに関連付けられたレスポンスを保存します。 リクエストに同じキャッシュキーが含まれる場合、応答は再利用されます。 目的は HTTP に似ています [`Vary` ヘッダー](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.44).

パラメーター `headers` および `cookies` キーを使用すると、このキャッシュキーを変更できます。

これらのキーのデフォルト値は次のとおりです。

```yaml
cache:
    enabled: true
    headers: ["Accept-Language", "Accept"]
    cookies: ["*"]
```

## キャッシュ属性

### `enabled`

に設定されている場合 `true`、このルートのキャッシュを有効にします。 に設定されている場合 `false`、このルートのキャッシュを無効にします。

### `headers`

キャッシュキーが依存する必要がある値を定義します。

例えば、 `headers` 重要なのは次のとおりです。

```yaml
cache:
    enabled: true
    headers: ["Accept"]
```

次に、Adobe Commerceは、の値ごとに異なる応答をキャッシュします `Accept` HTTP ヘッダー。

### `cookies`

この `cookies` キーは、キャッシュキーが依存する必要がある値を定義します。

例：

```yaml
cache:
    enabled: true
    cookies: ["value"]
```

キャッシュキーは、の値によって異なります `value` リクエスト内の cookie。

次の場合は、特殊なケースがあります。 `cookies` キーに含まれる `["*"]` の値。 この値は、cookie を持つリクエストによってキャッシュがバイパスされることを意味します。 これがデフォルト値です。

>[!NOTE]
>
>cookie 名にワイルドカードを使用することはできません。 正確な cookie 名を使用するか、アスタリスク（`*`）に設定します。 例： `SESS*` または `~SESS` 現在 **ではない** 有効な値。

Cookie には次の制限があります。

- 最大個を設定できます **50 の cookie** システム内の それ以外の場合、アプリケーションは `Unable to send the cookie. Maximum number of cookies would be exceeded` 例外。
- Cookie の最大サイズはです **4096 バイト**. それ以外の場合、アプリケーションは `Unable to send the cookie. Size of '%name' is %size bytes` 例外。

### `default_ttl`

応答にがありません `Cache-Control` ヘッダー、 `default_ttl` キーを使用して、キャッシュ時間を秒単位で定義します。 デフォルト値はです `0`。これは、何もキャッシュされないことを意味します。
