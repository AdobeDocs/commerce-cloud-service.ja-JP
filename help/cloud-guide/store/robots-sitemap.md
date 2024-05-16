---
title: サイトマップと検索エンジンロボットを追加
description: クラウドインフラストラクチャー上でサイトマップと検索エンジンロボットをAdobe Commerceに追加する方法を説明します。
feature: Cloud, Configuration, Search, Site Navigation
exl-id: b98f43fa-1878-466d-8ea0-1e7207af8b60
source-git-commit: ee1db75c73c086e0ea54e1a7591ca7f2b4d2b36d
workflow-type: tm+mt
source-wordcount: '517'
ht-degree: 0%

---

# サイトマップと検索エンジンロボットを追加

を生成して書き込もうとします `sitemap.xml` ファイルをルートディレクトリに移動すると、次のエラーが発生します。

```terminal
Please make sure that "/" is writable by the web-server.
```

クラウドインフラストラクチャー上のAdobe Commerceでは、次のような特定のディレクトリにのみ書き込むことができます `var`, `pub/media`, `pub/static`、または `app/etc`. を生成する場合 `sitemap.xml` ファイルを管理パネルを使用して、を指定する必要があります `/media/` パス。

を生成する必要はありません `robots.txt` ファイルが生成されるのは、 `robots.txt` コンテンツはオンデマンドでデータベースに保存されます。 ブラウザーでコンテンツを表示するには、 `<domain.your.project>/robots.txt` または `<domain.your.project>/robots` リンク。

これには、ECE-Tools バージョン 2002.0.12 以降が必要で、 `.magento.app.yaml` ファイル。 これらのルールの例については、を参照してください [magento-cloud リポジトリ](https://github.com/magento/magento-cloud/blob/master/.magento.app.yaml#L43-L49).

**を生成するには `sitemap.xml` バージョン 2.2 以降のファイル**:

1. 管理者にアクセスします。
1. 日 _Marketing_ メニュー、クリック **サイトマップ** が含まれる _SEO と検索_ セクション。
1. が含まれる _サイトマップ_ 表示、クリック **サイトマップを追加**.
1. が含まれる _新しいサイトマップ_ 表示するには、次の値を入力します。

   - **ファイル名**:`sitemap.xml`
   - **パス**:`/media/`

1. クリック **保存して生成**. 新しいサイトマップは、で使用できるようになります。 _サイトマップ_ グリッド。
1. 内のパスをクリックします _Googleへのリンク_ 列。

**コンテンツをに追加するには `robots.txt` ファイル**:

1. 管理者にアクセスします。
1. 日 _コンテンツ_ メニュー、クリック **設定** が含まれる _デザイン_ セクション。
1. が含まれる _デザイン設定_ 表示、クリック **編集** の web サイトの場合 _アクション_ 列。
1. が含まれる _メイン Web サイト_ 表示、クリック **検索エンジンロボット**.
1. を更新 **robots.txt のカスタム命令の編集** フィールド。
1. クリック **設定を保存**.
1. を確認 `<domain.your.project>/robots.txt` ファイルまたは `<domain.your.project>/robots` ブラウザーの URL。

>[!NOTE]
>
>次の場合 `<domain.your.project>/robots.txt` ファイルは、 `404 error`, [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) リダイレクトの削除元 `/robots.txt` 対象： `/media/robots.txt`.

## Fastly VCL スニペットを使用した書き換え

異なるドメインがあり、個別のサイトマップが必要な場合は、適切なサイトマップにルーティングする VCL を作成できます。 生成： `sitemap.xml` 上記のように Admin パネルのファイルを開き、カスタム Fastly VCL スニペットを作成してリダイレクトを管理します。 参照： [カスタム Fastly VCL スニペット](../cdn/fastly-vcl-custom-snippets.md).

>[!NOTE]
>
> 管理 UI から、または Fastly API を使用して、カスタム VCL スニペットをアップロードできます。 参照： [カスタム VCL スニペットの例とチュートリアル](../cdn/fastly-vcl-custom-snippets.md#example-vcl-snippet-code).

### リダイレクトに Fastly VCL スニペットを使用

パスを書き換えるカスタム VCL スニペットを作成する `sitemap.xml` 対象： `/media/sitemap.xml` の使用 `type` および `content` キーと値のペア

```json
{
  "name": "sitemapxml_rewrite",
  "dynamic": "0",
  "type": "recv",
  "priority": "90",
  "content": "if ( req.url.path ~ \"^/?sitemap.xml$\" ) { set req.url = \"/media/sitemap.xml\"; }"
}
```

次の例は、のパスを書き換える方法を示しています `robots.txt` および `sitemap.xml` 対象： `/media/robots.txt` および `/media/sitemap.xml`

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

を作成 `pub/media/domain_robots.txt` ファイル（ドメインは） `domain.com`次の VCL スニペットを使用します。

```json
{
  "name": "domain_robots",
  "dynamic": "0",
  "type": "recv",
  "priority": "90",
  "content": "if ( req.url.path == \"/robots.txt\" ) { if ( req.http.host ~ \"(domain).com$\" ) { set req.url = \"/media/\" re.group.1 \"_robots.txt\"; }}"
}
```

VCL スニペット ルート `http://domain.com/robots.txt` そして、次の項目を提示します `pub/media/domain_robots.txt` ファイル。

リダイレクトの設定方法 `robots.txt` および `sitemap.xml` 単一のスニペットで、を作成します。 `pub/media/domain_robots.txt` および `pub/media/domain_sitemap.xml` ファイル（ドメインは） `domain.com` 次の VCL スニペットを使用します。

```json
{
  "name": "domain_sitemaprobots",
  "dynamic": "0",
  "type": "recv",
  "priority": "90",
  "content": "if ( req.url.path == \"/robots.txt\" ) { if ( req.http.host ~ \"(domain).com$\" ) { set req.url = \"/media/\" re.group.1 \"_robots.txt\"; }} else if ( req.url.path == \"/sitemap.xml\" ) { if ( req.http.host ~ \"(domain).com$\" ) {  set req.url = \"/media/\" re.group.1 \"_sitemap.xml\"; }}"
}
```

が含まれる `sitemap` 管理設定。を使用してファイルの場所を指定する必要があります。 `pub/media/` むしろ `/`.

### 検索エンジンによるインデックス作成の設定

アクティベートするには `robots.txt` カスタマイズの場合は、 **検索エンジンによるインデックス作成はオンになっています（）`<environment-name>`** プロジェクト設定の「」オプションを選択します。

![の使用 [!DNL Cloud Console] 環境を管理するには](../../assets/robots-indexing-by-search-engine.png)

>[!NOTE]
>
>PWA Studioを使用していて、設定済みのにアクセスできない場合 `robots.txt` ファイル、追加 `robots.txt` に [フロント名^許可リスト](https://github.com/magento/magento2-upward-connector#front-name-allowlist) 時刻 **ストア** > 設定 > **一般** > **Web** > UPWARD PWAの設定。
