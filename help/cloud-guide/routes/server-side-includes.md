---
title: サーバーサイドインクルード
description: クラウドインフラストラクチャー上のAdobe Commerceでサーバーサイドインクルードを使用する方法について説明します。
feature: Cloud, Routes
exl-id: 34a38cb5-5f0e-49ac-9dba-bb581a06aeed
source-git-commit: 649c11b111aa9c9105e54908bf9c6f48741f10e4
workflow-type: tm+mt
source-wordcount: '172'
ht-degree: 0%

---

# サーバーサイドインクルード

[サーバーサイドインクルード](https://nginx.org/en/docs/http/ngx_http_ssi_module.html) （SSI）は、ページのレンダリング中にサーバーで評価される、HTMLページ内のディレクティブです。 SSI を使用すると、ページ全体を提供することなく、動的に生成されたコンテンツを既存のHTMLページに追加できます。

でルートごとに SSI を有効または無効にすることができます `.magento/routes.yaml`。例：

```yaml
    "http://{default}/":
        type: upstream
        upstream: "myapp:php"
        cache:
            enabled: false
            ssi:
                enabled: true
    "http://{default}/time.php":
        type: upstream
        upstream: "myapp:php"
        cache:
            enabled: true
```

SSI を使用すると、既存の情報に従ってサーバーがHTMLの一部を入力するHTMLレスポンスディレクティブを含めることができます [キャッシュ設定](caching.md).

次の例では、ページの上部に動的日付コントロールを挿入し、下部に 600 秒ごとに更新される別の日付コントロールを挿入する方法を示します。

次のような任意のページに次を追加します `/index.php`:

```php?start_inline=1
echo date(DATE_RFC2822);
<!--#include virtual="time.php" -->
```

に次の内容を追加します `time.php`:

```php?start_inline=1
header("Cache-Control: max-age=600");
echo date(DATE_RFC2822);
```

コントロールを追加したページを参照します。 ページを数回更新すると、ページの上部の時間は変更されますが、下部の時間は 600 秒ごとにのみ変更されます。
