---
title: Web プロパティ
description: で web プロパティを設定する方法の例を参照してください [!DNL Commerce] アプリケーション設定ファイル。
feature: Cloud, Configuration
exl-id: 2ca94908-6fe1-42fd-bc3b-ef2dd473f1bb
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '410'
ht-degree: 0%

---

# Web プロパティ

この `web` プロパティは、アプリケーションが web に公開される方法（HTTP 単位）を定義し、web アプリケーションがコンテンツを提供する方法を決定し、各場所にルールを設定することによって受信リクエストに対するアプリケーションコンテナの応答を制御します _ブロック_. ブロックは、スラッシュ（`/`）に設定します。

```yaml
web:
    locations:
        "/":
            # The public directory of the app, relative to its root.
```

を微調整できます `locations` 次の各キー値を使用した設定 `locations` ブロック :

| 属性 | 説明 |
| :--- | :--- |
| `allow` | 「ルール」に一致しないファイルを提供する。 デフォルト値= `true` |
| `expires` | ブラウザーでコンテンツをキャッシュする秒数を設定します。 このキーは、 `cache-control` および `expires` 静的コンテンツのヘッダー。 この値が設定されていない場合、 `expires` 静的コンテンツファイルを提供する際に、ディレクティブと結果ヘッダーは含まれません。 負の値 1 （`-1`）の値の場合、キャッシュは行われず、がデフォルト値になります。 時間値は、次の単位で表すことができます。  `ms` （ミリ秒）、 `s` （秒）、 `m` （分）、 `h` （時間）、 `d` （日間）、 `w` （週間）、 `M` （months, 30d）、 `y` （年、365d） |
| `headers` | などのカスタムヘッダーの設定 `X-Frame-Options`この場所から提供される静的コンテンツ用。 |
| `index` | アプリケーションに提供する静的ファイル（など）を一覧表示します。 `index.html` ファイル。 このキーにはコレクションが必要です。 これは、がファイルへのアクセスを「許可」している場合にのみ機能します。 `allow` または `rules` この場所のキー。 |
| `rules` | 場所の上書きを指定します。 リクエストに一致する正規表現を使用します。 受信リクエストがルールに一致する場合、リクエストの通常の処理はルールで使用されたキーによって上書きされます。 |
| `passthru` | 静的ファイルまたは PHP ファイルが見つからない場合に使用する URL を設定します。 通常、この URL は、次のようなアプリケーションのフロントコントローラーです `/index.php` または `/app.php`. |
| `root` | Web に公開するアプリケーションのルートを基準とした相対パスを設定します。 クラウドプロジェクトのパブリックディレクトリ（場所「/」）は、デフォルトで「pub」に設定されています。 |
| `scripts` | この場所にスクリプトを読み込むことができます。 値をに設定します `true` スクリプトを許可します。 |

デフォルトの設定では、次のことが可能です。

- ルート （`/`） パスを使用します。Web およびメディアにのみアクセスできます
- から `~/pub/static` および `~/pub/media` パス、任意のファイルにアクセス可能

次の例は、のデフォルト設定を示しています `.magento.app.yaml` 内のエントリに関連付けられた一連の web アクセス可能な場所のファイル  [`mounts` プロパティ](properties.md#mounts):

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
>この例は、単一のドメインをサポートするように設定されたクラウドプロジェクトのデフォルトの web 設定を示しています。 複数の web サイトやストアのサポートが必要なプロジェクトの場合、 `web` 共有ドメインをサポートするには、構成を設定する必要があります。 参照： [共有ドメインの場所の設定](../store/multiple-sites.md#configure-locations-for-shared-domains).
