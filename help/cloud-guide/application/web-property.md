---
title: Web プロパティ
description: Web プロパティを設定する方法については、 [!DNL Commerce] アプリケーション設定ファイル。
feature: Cloud, Configuration
exl-id: 2ca94908-6fe1-42fd-bc3b-ef2dd473f1bb
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '410'
ht-degree: 0%

---

# Web プロパティ

The `web` プロパティは、アプリケーションを web に公開する方法 (HTTP) を定義し、web アプリケーションでのコンテンツの提供方法を定義します。また、各場所でルールを設定して、受信要求に対するアプリケーションコンテナの応答方法を制御します _ブロック_. ブロックは、先頭にスラッシュ (`/`) をクリックします。

```yaml
web:
    locations:
        "/":
            # The public directory of the app, relative to its root.
```

このページでは、 `locations` 次の各キー値を使用した設定 `locations` ブロック：

| 属性 | 説明 |
| :--- | :--- |
| `allow` | 「rules」と一致しないファイルを提供する。 デフォルト値= `true` |
| `expires` | ブラウザーでコンテンツをキャッシュする時間（秒）を設定します。 このキーは、 `cache-control` および `expires` 静的コンテンツのヘッダー。 この値が設定されていない場合、 `expires` 静的コンテンツファイルを提供する際に、ディレクティブと結果のヘッダーは含まれません。 負の 1 (`-1`) の値を指定すると、キャッシュが実行されず、がデフォルト値になります。 次の単位で時間値を表すことができます。  `ms` （ミリ秒）、 `s` （秒）、 `m` （分）, `h` （時間） `d` （日）, `w` （週）、 `M` （月、30 日）、または `y` （年、365 日） |
| `headers` | 次のようなカスタムヘッダーを設定する。 `X-Frame-Options`（この場所から提供される静的コンテンツの場合） |
| `index` | アプリケーションで使用する静的ファイル ( `index.html` ファイル。 このキーは、コレクションを想定しています。 これは、1 つまたは複数のファイルへのアクセスが `allow` または `rules` この場所のキー。 |
| `rules` | 場所の上書きを指定します。 リクエストの照合には、正規表現を使用します。 受信リクエストがルールと一致する場合、リクエストの通常の処理は、ルールで使用されるキーで上書きされます。 |
| `passthru` | 静的ファイルまたは PHP ファイルが見つからない場合に使用する URL を設定します。 通常、この URL は、アプリケーションのフロントコントローラです（例： ）。 `/index.php` または `/app.php`. |
| `root` | Web 上で公開されるアプリケーションのルートを基準とした相対パスを設定します。 クラウドプロジェクトのパブリックディレクトリ（ロケーション「/」）は、デフォルトで「pub」に設定されています。 |
| `scripts` | この場所でスクリプトの読み込みを許可します。 値をに設定します。 `true` スクリプトを許可する場合。 |

デフォルトの設定では、次のことが可能です。

- ルート (`/`) パスの場合は、web およびメディアのみにアクセスできます。
- 次から： `~/pub/static` および `~/pub/media` パス、任意のファイルにアクセス可能

次の例は、 `.magento.app.yaml` ファイル内のエントリに関連付けられた、Web でアクセス可能な一連の場所の  [`mounts` プロパティ](properties.md#mounts):

```yaml
 # The configuration of app when it is exposed to the web.
web:
    locations:
        "/":
            # The public directory of the app, relative to its root.
            root: "pub"
            # The front-controller script to send non-static requests to.
            passthru: "/index.php"
            index:
                - index.php
            expires: -1
            scripts: true
            allow: false
            rules:
                \.(css|js|map|hbs|gif|jpe?g|png|tiff|wbmp|ico|jng|bmp|svgz|midi?|mp?ga|mp2|mp3|m4a|ra|weba|3gpp?|mp4|mpe?g|mpe|ogv|mov|webm|flv|mng|asx|asf|wmv|avi|ogx|swf|jar|ttf|eot|woff|otf|html?)$:
                    allow: true
                ^/sitemap(.*)\.xml$:
                    passthru: "/media/sitemap$1.xml"
        "/media":
            root: "pub/media"
            allow: true
            scripts: false
            expires: 1y
            passthru: "/get.php"
        "/static":
            root: "pub/static"
            allow: true
            scripts: false
            expires: 1y
            passthru: "/front-static.php"
            rules:
                ^/static/version\d+/(?<resource>.*)$:
                    passthru: "/static/$resource"
```

>[!NOTE]
>
>この例では、単一のドメインをサポートするように設定されたクラウドプロジェクトのデフォルト Web 設定を示します。 複数の Web サイトまたはストアのサポートを必要とするプロジェクトの場合、 `web` 共有ドメインをサポートするには、設定を行う必要があります。 詳しくは、 [共有ドメインの場所の設定](../store/multiple-sites.md#configure-locations-for-shared-domains).
