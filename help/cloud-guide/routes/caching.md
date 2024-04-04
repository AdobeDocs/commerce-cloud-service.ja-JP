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

クラウドインフラストラクチャプロジェクト環境でキャッシュを有効にすることができます。 キャッシュを無効にした場合、Adobe Commerceは直接ファイルを提供します。

{{route-placeholder}}

## キャッシュの設定

アプリケーションのキャッシュを有効にするには、 `.magento/routes.yaml` ファイルの内容は次のとおりです。

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

次の例に示すように、複数のルートに対して別々にキャッシュルールを設定することで、詳細なキャッシュを有効にします。

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

前述の例では、次のルートをキャッシュします。

- `http://{default}/`
- `http://{default}/path/more/`
- `http://{default}/path/more/etc/`

そして次のルートは **not** キャッシュ済み：

- `http://{default}/path/`
- `http://{default}/path/etc/`

>[!NOTE]
>
>ルートの正規表現は次のとおりです。 **not** サポート対象。

## キャッシュ時間

キャッシュ時間は `Cache-Control` 応答ヘッダーの値。 いいえの場合 `Cache-Control` ヘッダーが応答に含まれている場合、 `default_ttl` キーが使用されます。

## キャッシュキー

応答のキャッシュ方法を決定するために、Adobe Commerceは、いくつかの要因に依存するキャッシュキーを作成し、このキーに関連付けられた応答を保存します。 リクエストが同じキャッシュキーを持つ場合、応答は再利用されます。 目的は HTTP に似ています [`Vary` ヘッダー](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.44).

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

に設定する場合 `true`、このルートのキャッシュを有効にします。 に設定する場合 `false`、このルートのキャッシュを無効にします。

### `headers`

キャッシュキーが依存する値を定義します。

例えば、 `headers` キーは次のとおりです。

```yaml
cache:
    enabled: true
    headers: ["Accept"]
```

その後、Adobe Commerceは、 `Accept` HTTP ヘッダー。

### `cookies`

The `cookies` キーは、キャッシュキーが依存する値を定義します。

例：

```yaml
cache:
    enabled: true
    cookies: ["value"]
```

キャッシュキーは、 `value` cookie をリクエストに含めます。

特殊なケースが存在する場合、 `cookies` キーには `["*"]` の値です。 この値は、cookie を持つリクエストはすべてキャッシュをバイパスすることを意味します。 これはデフォルト値です。

>[!NOTE]
>
>cookie 名にワイルドカードを使用することはできません。 正確な Cookie 名を使用するか、アスタリスク (`*`) をクリックします。 例： `SESS*` または `~SESS` は現在 **not** 有効な値。

Cookie には次の制限があります。

- 最大 **50 個の cookie** システム内で使用されます。 それ以外の場合、アプリケーションは `Unable to send the cookie. Maximum number of cookies would be exceeded` 例外です。
- Cookie の最大サイズは次のとおりです。 **4096 バイト**. それ以外の場合、アプリケーションは `Unable to send the cookie. Size of '%name' is %size bytes` 例外です。

### `default_ttl`

応答に `Cache-Control` ヘッダー、 `default_ttl` キーは、キャッシュ時間を秒単位で定義するために使用されます。 デフォルト値は `0`は、何もキャッシュされていないことを意味します。
