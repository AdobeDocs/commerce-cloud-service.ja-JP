---
title: サイトマップと検索エンジンのロボットの追加
description: クラウドインフラストラクチャ上のAdobe Commerceにサイトマップと検索エンジンのロボットを追加する方法を説明します。
feature: Cloud, Configuration, Search, Site Navigation
exl-id: b98f43fa-1878-466d-8ea0-1e7207af8b60
source-git-commit: ee1db75c73c086e0ea54e1a7591ca7f2b4d2b36d
workflow-type: tm+mt
source-wordcount: '517'
ht-degree: 0%

---

# サイトマップと検索エンジンのロボットの追加

を生成し、 `sitemap.xml` ファイルをルートディレクトリに格納すると、次のエラーが発生します。

```terminal
Please make sure that "/" is writable by the web-server.
```

クラウドインフラストラクチャ上のAdobe Commerceを使用すると、次のような特定のディレクトリにのみ書き込むことができます。 `var`, `pub/media`, `pub/static`または `app/etc`. 次の場合に `sitemap.xml` ファイルを管理パネルで指定するには、 `/media/` パス。

必ずしも `robots.txt` ファイルを生成する理由 `robots.txt` コンテンツはオンデマンドで保存し、データベースに保存します。 ブラウザーでコンテンツを表示するには、 `<domain.your.project>/robots.txt` または `<domain.your.project>/robots` リンク。

これには、ECE-Tools バージョン 2002.0.12 以降が必要です。 `.magento.app.yaml` ファイル。 これらのルールの例については、 [magento-cloud リポジトリ](https://github.com/magento/magento-cloud/blob/master/.magento.app.yaml#L43-L49).

**次の手順で `sitemap.xml` バージョン 2.2 以降のファイル**:

1. 管理者にアクセスします。
1. 次の日： _マーケティング_ メニュー、クリック **サイトマップ** （内） _SEO と検索_ 」セクションに入力します。
1. Adobe Analytics の _サイトマップ_ 表示、クリック **サイトマップを追加**.
1. Adobe Analytics の _新しいサイトマップ_ ビューで、次の値を入力します。

   - **ファイル名**:`sitemap.xml`
   - **パス**:`/media/`

1. クリック **保存して生成**. 新しいサイトマップが _サイトマップ_ グリッド。
1. パスをクリックします。 _Googleのリンク_ 列。

**コンテンツを `robots.txt` ファイル**:

1. 管理者にアクセスします。
1. 次の日： _コンテンツ_ メニュー、クリック **設定** （内） _デザイン_ 」セクションに入力します。
1. Adobe Analytics の _デザイン設定_ 表示、クリック **編集** (Web サイトの _アクション_ 列。
1. Adobe Analytics の _メイン Web サイト_ 表示、クリック **検索エンジンロボット**.
1. を更新します。 **robots.txt のカスタム命令を編集** フィールドに入力します。
1. クリック **設定を保存**.
1. を確認します。 `<domain.your.project>/robots.txt` ファイルまたは `<domain.your.project>/robots` ブラウザーの URL。

>[!NOTE]
>
>次の場合、 `<domain.your.project>/robots.txt` ファイルが `404 error`, [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) リダイレクトを削除するには `/robots.txt` から `/media/robots.txt`.

## Fastly VCL スニペットを使用した書き換え

異なるドメインがあり、別のサイトマップが必要な場合は、VCL を作成して適切なサイトマップにルーティングできます。 を生成する `sitemap.xml` ファイルを管理パネルで作成し、リダイレクトを管理するためのカスタム Fastly VCL スニペットを作成します。 詳しくは、 [カスタム Fastly VCL スニペット](../cdn/fastly-vcl-custom-snippets.md).

>[!NOTE]
>
> カスタム VCL スニペットは、Admin UI または Fastly API を使用してアップロードできます。 詳しくは、 [カスタム VCL スニペットの例とチュートリアル](../cdn/fastly-vcl-custom-snippets.md#example-vcl-snippet-code).

### リダイレクトに Fastly VCL スニペットを使用

カスタム VCL スニペットを作成して、のパスを書き換えます。 `sitemap.xml` から `/media/sitemap.xml` の使用 `type` および `content` キーと値のペアとして渡すことができます。

```json
{
  "name": "sitemapxml_rewrite",
  "dynamic": "0",
  "type": "recv",
  "priority": "90",
  "content": "if ( req.url.path ~ \"^/?sitemap.xml$\" ) { set req.url = \"/media/sitemap.xml\"; }"
}
```

次の例は、 `robots.txt` および `sitemap.xml` から `/media/robots.txt` および `/media/sitemap.xml`

```json
{
  "name": "sitemaprobots_rewrite",
  "dynamic": "0",
  "type": "recv",
  "priority": "90",
  "content": "if ( req.url.path ~ \"^/?sitemap.xml$\" ) { set req.url = \"/media/sitemap.xml\"; } else if (req.url.path ~ \"^/?robots.txt$\") { set req.url = \"/media/robots.txt\";}"
}
```

**特定のドメインリダイレクトに Fastly VCL スニペットを使用するには**:

の作成 `pub/media/domain_robots.txt` ファイル ( ドメインは `domain.com`をクリックし、次の VCL スニペットを使用します。

```json
{
  "name": "domain_robots",
  "dynamic": "0",
  "type": "recv",
  "priority": "90",
  "content": "if ( req.url.path == \"/robots.txt\" ) { if ( req.http.host ~ \"(domain).com$\" ) { set req.url = \"/media/\" re.group.1 \"_robots.txt\"; }}"
}
```

VCL スニペットルート `http://domain.com/robots.txt` とは、 `pub/media/domain_robots.txt` ファイル。

のリダイレクトを設定するには、以下を実行します。 `robots.txt` および `sitemap.xml` 単一のスニペットで、 `pub/media/domain_robots.txt` および `pub/media/domain_sitemap.xml` ファイル ( ドメインは `domain.com` 次の VCL スニペットを使用します。

```json
{
  "name": "domain_sitemaprobots",
  "dynamic": "0",
  "type": "recv",
  "priority": "90",
  "content": "if ( req.url.path == \"/robots.txt\" ) { if ( req.http.host ~ \"(domain).com$\" ) { set req.url = \"/media/\" re.group.1 \"_robots.txt\"; }} else if ( req.url.path == \"/sitemap.xml\" ) { if ( req.http.host ~ \"(domain).com$\" ) {  set req.url = \"/media/\" re.group.1 \"_sitemap.xml\"; }}"
}
```

Adobe Analytics の `sitemap` admin config を使用する場合は、 `pub/media/` ではなく `/`.

### 検索エンジンによるインデックス作成の設定

有効化するには `robots.txt` カスタマイズの場合は、 **検索エンジンによるインデックス作成は、`<environment-name>`** 」オプションを使用して、プロジェクト設定で設定できます。

![以下を使用します。 [!DNL Cloud Console] 環境を管理するには](../../assets/robots-indexing-by-search-engine.png)

>[!NOTE]
>
>PWA Studioを使用していて、設定した `robots.txt` ファイル、追加 `robots.txt` から [フロント名の許可リストに加える](https://github.com/magento/magento2-upward-connector#front-name-allowlist) 時刻 **ストア** /設定 > **一般** > **Web** > 上向きPWA設定
