---
title: リダイレクト
description: クラウドインフラストラクチャプロジェクトでAdobe Commerceのリダイレクトルールを管理する方法について説明します。
feature: Cloud, Routes
exl-id: 7089a790-6341-4443-990a-df42091f0680
source-git-commit: 649c11b111aa9c9105e54908bf9c6f48741f10e4
workflow-type: tm+mt
source-wordcount: '646'
ht-degree: 0%

---

# リダイレクト

リダイレクトルールの管理は、特に、時間の経過と共に変更または削除された受信リンクを失いたくない場合、web アプリケーションの一般的な要件です。

以下では、を使用して、クラウドインフラストラクチャプロジェクト上のAdobe Commerceでリダイレクトルールを管理する方法を示します `routes.yaml` 設定ファイル。 このトピックで説明したリダイレクトメソッドが機能しない場合は、キャッシュヘッダーを使用して同じ操作を実行できます。

{{route-placeholder}}

## Pro 環境のアップデート

{{pro-self-service-warning}}

>[!WARNING]
>
>クラウドインフラストラクチャプロジェクトのAdobe Commerceの場合、で多数の非正規表現リダイレクトおよび書き換えを設定する `routes.yaml` ファイルは、パフォーマンスの問題を引き起こす可能性があります。 次の場合 `routes.yaml` ファイルは 32 KB 以上で、非正規表現のリダイレクトと Fastly への書き換えをオフロードします。 参照： [非正規表現のオフロードにより、Nginx （ルート）ではなく Fastly にリダイレクトされる](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/offload-non-regex-redirects-to-fastly-instead-of-nginx-routes.html) が含まれる _Adobe Commerceヘルプセンター_.

## ルート全体リダイレクト

ルート全体のリダイレクトを使用すると、 `routes.yaml` ファイル。 例えば、apex ドメインからにリダイレクトできます `www` サブドメインを次のように設定します。

```yaml
http://{default}/:
    type: redirect
    to: http://www.{default}/
```

## 部分的なルート リダイレクト

が含まれる `.magento/routes.yaml` ファイル。パターン一致に基づいて、既存のルートに部分的なリダイレクトルールを追加できます。

```yaml
http://{default}/:
    redirects:
        expires: 1d
        paths:
          "/from": { to: "http://example.com/" }
          "/regexp/(.*)/matching": { to: "http://example.com/$1", regexp: true }
```

部分リダイレクトは、アプリケーションから直接提供されるルートを含む、あらゆるタイプのルートで機能します。

では、2 つのキーを使用できます。 `redirects`:

- **expires**- ブラウザーでリダイレクトをキャッシュする時間を指定します（オプション）。 有効な値の例を次に示します `3600s`, `1d`, `2w`, `3m`.

- **パス** – 部分ルートリダイレクトルールの設定を指定する 1 つ以上のキーと値のペア。

  各リダイレクトルールでは、キーはリダイレクトのリクエストパスをフィルタリングするための式です。 値は、リダイレクトのターゲット先とリダイレクトを処理するためのオプションを指定するオブジェクトです。

  value オブジェクトには、次のプロパティがあります。

  | プロパティ | 説明 |
  | ---------- | ----------- |
  | `to` | 必須。リダイレクトルールのターゲット先を指定する、部分的な絶対パス、プロトコルとホストを含む URL、またはパターンです。 |
  | `regexp` | （オプション、デフォルトは） `false`. パス キーを PCRE 正規表現として解釈するかどうかを指定します。 |
  | `prefix` | リダイレクトをパスとその子の両方に適用するか、パス自体に適用するかを指定します。 デフォルトは `true`. 次の場合、この値はサポートされません。 `regexp` 等しい `true`. |
  | `append_suffix` | リダイレクトでサフィックスを引き継ぐかどうかを決定します。 デフォルトは `true`. 次の場合、この値はサポートされません `regexp` キー： `true` または* （該当する場合） `prefix` キー： `false`. |
  | `code` | HTTP ステータスコードを指定します。 有効なステータスコードは次のとおりです [`301` （完全に移動）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.3.2), [`302`](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.3.3), [`307`](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.3.8)、および [`308`](https://www.rfc-editor.org/rfc/rfc7238). デフォルトは `302`. |
  | `expires` | オプションで、ブラウザーでリダイレクトをキャッシュする時間を指定します。 デフォルトは `expires` の直下に定義された値 `redirects` 重要ですが、このレベルでは、個々の部分リダイレクトのキャッシュの有効期限を微調整できます。 |

## 部分的なルートリダイレクトの例

次の例は、部分的なルート リダイレクトを `routes.yaml` 各種を使用したファイル `paths` 設定オプション。

### 正規表現のパターンマッチング

正規表現に基づいてリダイレクトリクエストを設定するには、次の形式を使用します。

```yaml
http://{default}/:
    type: upstream
    redirects:
    paths:
        "/regexp/(.*)/match": { to: "http://example.com/$1", regexp: true }
```

この設定では、正規表現に対してリクエストパスがフィルタリングされ、一致するリクエストがにリダイレクトされます `https://example.com`. 例えば、へのリクエストです。 `https://example.com/regexp/a/b/c/match` はにリダイレクトされます `https://example.com/a/b/c`.

### プレフィックスのパターンマッチング

次の形式を使用して、指定した接頭辞パターンで始まるパスのリダイレクトリクエストを設定します。

```yaml
http://{default}/:
    type: upstream
    redirects:
    paths:
        "/from": { to: "https://{default}/to", prefix: true }
```

この設定は次のように動作します。

- パターンに一致するリクエストをリダイレクトします `/from` パスに追加します `http://{default}/to`.

- パターンに一致するリクエストをリダイレクトします `/from/another/path` 対象： `https://{default}/to/another/path`.

- 変更した場合 `prefix` プロパティ先 `false`、に一致するリクエスト `/from` パターン リダイレクトをトリガーしますが、に一致するリクエストです `/from/another/path` パターンが存在しない。

### サフィックスパターンのマッチング

次の形式を使用して、リクエストのパスサフィックスをターゲット宛先に追加するリダイレクト要求を設定します。

```yaml
http://{default}/:
    type: upstream
    redirects:
    paths: "/from": { to: "https://{default}/to", append_suffix: false }
```

この設定は次のように動作します。

- パターンに一致するリクエストをリダイレクトします `/from/path/suffix` パスに追加します `https://{default}/to`.

- 変更した場合 `append_suffix` プロパティ先 `true`、次に一致するリクエスト `/from/path/suffix`  パスにリダイレクト `https://{default}/to/path/suffix`.

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

- 最初のパスからリダイレクト（`/from`）は 1 日間キャッシュされます。

- 2 番目のパスからリダイレクトする（`/here`）は 2 週間キャッシュされます。
