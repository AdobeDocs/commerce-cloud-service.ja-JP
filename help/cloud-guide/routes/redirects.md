---
title: リダイレクト
description: クラウドインフラストラクチャプロジェクト上のAdobe Commerceのリダイレクトルールを管理する方法を説明します。
feature: Cloud, Routes
exl-id: 7089a790-6341-4443-990a-df42091f0680
source-git-commit: 649c11b111aa9c9105e54908bf9c6f48741f10e4
workflow-type: tm+mt
source-wordcount: '646'
ht-degree: 0%

---

# リダイレクト

リダイレクトルールの管理は、Web アプリケーションで一般的に必要となる要件です。特に、時間の経過と共に変更または削除された受信リンクを失いたくない場合に、この要件を満たす必要があります。

以下に、 `routes.yaml` 設定ファイル。 このトピックで説明したリダイレクト方法が機能しない場合は、ヘッダーのキャッシュを使用しても同じことを実行できます。

{{route-placeholder}}

## Pro 環境のアップデート

{{pro-self-service-warning}}

>[!WARNING]
>
>クラウドインフラストラクチャのAdobe Commerceプロジェクトの場合、 `routes.yaml` ファイルが原因で、パフォーマンスの問題が発生する可能性があります。 次の場合、 `routes.yaml` ファイルが 32 KB 以上の場合は、regex 以外のリダイレクトをオフロードし、Fastly に書き換えます。 詳しくは、 [Nginx（ルート）ではなく Fastly にリダイレクトする非正規表現のオフロード](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/offload-non-regex-redirects-to-fastly-instead-of-nginx-routes.html) （内） _Adobe Commerce Help Center_.

## 全ルートリダイレクト

全ルートリダイレクトを使用すると、 `routes.yaml` ファイル。 例えば、apex ドメインから `www` サブドメインを次のように指定します。

```yaml
http://{default}/:
    type: redirect
    to: http://www.{default}/
```

## 部分ルートリダイレクト

Adobe Analytics の `.magento/routes.yaml` ファイルでは、パターンマッチングに基づいて、既存のルートに部分的なリダイレクトルールを追加できます。

```yaml
http://{default}/:
    redirects:
        expires: 1d
        paths:
          "/from": { to: "http://example.com/" }
          "/regexp/(.*)/matching": { to: "http://example.com/$1", regexp: true }
```

部分リダイレクトは、アプリケーションから直接提供されるルートを含む、あらゆるタイプのルートで機能します。

以下に 2 つのキーを使用できます。 `redirects`:

- **有効期限** — オプション。ブラウザーでリダイレクトをキャッシュする時間を指定します。 有効な値の例は次のとおりです。 `3600s`, `1d`, `2w`, `3m`.

- **パス** — 部分ルートリダイレクトルールの設定を指定する 1 つ以上のキーと値のペア。

  キーは、リダイレクトのリクエストパスをフィルターするための式です。 値は、リダイレクトのターゲット先と、リダイレクトを処理するためのオプションを指定するオブジェクトです。

  value オブジェクトには次のプロパティがあります。

  | プロパティ | 説明 |
  | ---------- | ----------- |
  | `to` | 必須。絶対パスの一部、プロトコルとホストを含む URL、またはリダイレクトルールのターゲットの宛先を指定するパターン。 |
  | `regexp` | オプション、デフォルトはです。 `false`. パスキーを PCRE の正規表現として解釈する必要があるかどうかを指定します。 |
  | `prefix` | リダイレクトをパスとそのすべての子の両方に適用するか、パス自体にのみ適用するかを指定します。 デフォルトはです。 `true`. この値は、 `regexp` 次に該当 `true`. |
  | `append_suffix` | サフィックスをリダイレクトと共に持ち越すかどうかを指定します。 デフォルトはです。 `true`. この値は、 `regexp` キーは `true` または*の場合 `prefix` キーは `false`. |
  | `code` | HTTP ステータスコードを指定します。 有効なステータスコードは次のとおりです。 [`301` （恒久的に移動）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.3.2), [`302`](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.3.3), [`307`](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.3.8)、および [`308`](https://www.rfc-editor.org/rfc/rfc7238). デフォルトはです。 `302`. |
  | `expires` | （オプション）ブラウザーでリダイレクトをキャッシュする時間を指定します。 デフォルトでは `expires` 直接下に定義された値 `redirects` キーに変更できますが、このレベルでは、個々の部分リダイレクトについてキャッシュの有効期限を微調整できます。 |

## 部分ルートリダイレクトの例

次の例は、 `routes.yaml` 様々な `paths` 設定オプション。

### 正規表現パターンのマッチング

次の形式を使用して、正規表現に基づいてリダイレクトリクエストを設定します。

```yaml
http://{default}/:
    type: upstream
    redirects:
    paths:
        "/regexp/(.*)/match": { to: "http://example.com/$1", regexp: true }
```

この設定は、正規表現に照らしてパスをリクエストし、一致するリクエストをにリダイレクトする設定です。 `https://example.com`. 例えば、 `https://example.com/regexp/a/b/c/match` リダイレクト先 `https://example.com/a/b/c`.

### プレフィックスパターンの一致

次の形式を使用して、指定したプレフィックスパターンで始まるパスに対するリダイレクトリクエストを設定します。

```yaml
http://{default}/:
    type: upstream
    redirects:
    paths:
        "/from": { to: "https://{default}/to", prefix: true }
```

この設定は次のように動作します。

- パターンに一致するリクエストをリダイレクト `/from` パスに `http://{default}/to`.

- パターンに一致するリクエストをリダイレクト `/from/another/path` から `https://{default}/to/another/path`.

- 次の項目を変更した場合、 `prefix` プロパティを `false`、に一致する `/from` patternトリガーはリダイレクトですが、 `/from/another/path` pattern が無効です。

### サフィックスパターンの一致

次の形式を使用して、リクエストのパスサフィックスをターゲット宛先に追加するリダイレクトリクエストを設定します。

```yaml
http://{default}/:
    type: upstream
    redirects:
    paths: "/from": { to: "https://{default}/to", append_suffix: false }
```

この設定は次のように動作します。

- パターンに一致するリクエストをリダイレクト `/from/path/suffix` パスに `https://{default}/to`.

- 次の項目を変更した場合、 `append_suffix` プロパティを `true`、に一致する `/from/path/suffix`  パスにリダイレクト `https://{default}/to/path/suffix`.

### パス固有のキャッシュ設定

次の形式を使用して、特定のパスからのリダイレクトをキャッシュする時間をカスタマイズします。

```yaml
http://{default}/:
    type: upstream
    redirects:
    expires: 1d
    paths:
        "/from": { to: "https://example.com/" }
        "/here": { to: "https://example.com/there", expires: "2w" }
```

この設定は次のように動作します。

- 最初のパス (`/from`) は 1 日間キャッシュされます。

- 2 番目のパス (`/here`) は 2 週間キャッシュされます。
