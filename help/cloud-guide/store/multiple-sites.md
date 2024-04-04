---
title: 複数の Web サイトまたはストアを設定する
description: クラウドインフラストラクチャ上のAdobe Commerce用に複数の Web サイトまたはストアを設定する方法について説明します。
feature: Cloud, Configuration, Routes, Site Navigation
exl-id: 16e932ef-f083-44d7-977f-0c78899e151a
source-git-commit: 85aa54af10e7ea44adde5403b69ff03d4a0c622f
workflow-type: tm+mt
source-wordcount: '1013'
ht-degree: 0%

---

# 複数の Web サイトまたはストアを設定する

英語ストア、フランス語ストア、ドイツ語ストアなど、複数の Web サイトまたはストアを持つようにAdobe Commerceを設定できます。 詳しくは、 [Web サイト、ストア、ストア表示について](best-practices.md#store-views).

>[!WARNING]
>
>Web サイトや店舗の数が増えると、カタログデータが展開します。 プロジェクトのアーキテクチャによっては、追加のストアを使用すると、インデックス作成プロセスが長くなり、キャッシュされていないカタログページの応答時間が長くなる場合があります。 Adobeでは、サイトのパフォーマンスを詳細に監視することをお勧めします。

複数のストアを設定するプロセスは、一意のドメインを使用するか共有ドメインを使用するかによって異なります。

一意のドメインを持つ複数のストア：

```terminal
https://first.store.com/
https://second.store.com/
```

同じドメインの複数のストア：

```terminal
https://store.com/first/
https://store.com/second/
```

>[!TIP]
>
>ストア表示をサイトのベース URL に追加する場合、複数のディレクトリを作成する必要はありません。 詳しくは、 [ストアコードをベース URL に追加する](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/multi-sites/ms-admin.html) （内） _設定ガイド_.

## ドメインを追加

カスタムドメインは、Pro Staging および任意の実稼動環境に追加できます。統合環境に追加することはできません。

ドメインを追加するプロセスは、Cloud アカウントの種類に応じて異なります。

- Pro ステージングおよび実稼動用

  新しいドメインを Fastly に追加します。詳しくは、 [ドメインの管理](../cdn/fastly-custom-cache-configuration.md#manage-domains)またはサポートチケットを開いてサポートをリクエストしてください。 さらに、 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) 新しいドメインをクラスターに追加するように要求する場合。

- スターター生産のみ

  新しいドメインを Fastly に追加します。詳しくは、 [ドメインの管理](../cdn/fastly-custom-cache-configuration.md#manage-domains)または [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) 援助を要請する。 さらに、新しいドメインを **ドメイン** 」タブをクリックします。 [!DNL Cloud Console]: `https://<zone>.magento.cloud/projects/<project-ID>/edit`

## ローカルインストールの設定

複数のストアを使用するようにローカルインストールを設定するには、 [複数の Web サイトまたはストア][config-multiweb] （内） _設定ガイド_.

複数のストアを使用するようにローカルインストールを正常に作成およびテストした後、統合環境を準備する必要があります。

1. **ルートまたは場所の設定**—Adobe Commerceでの受信 URL の処理方法を指定します。

   - [別のドメイン用のルート](#configure-routes-for-separate-domains)
   - [共有ドメインの場所](#configure-locations-for-shared-domains)

1. **Web サイト、ストア、ストアの表示を設定する**— Adobe Commerce Admin UI を使用して設定
1. **変数の変更**— `MAGE_RUN_TYPE` および `MAGE_RUN_CODE` 変数 `magento-vars.php` ファイル
1. **環境のデプロイとテスト** — デプロイしてテストします。 `integration` 分岐

>[!TIP]
>
>ローカル環境を使用して、複数の Web サイトまたはストアを設定できます。 Cloud Docker の手順については、 [複数の Web サイトまたはストアを設定する](https://developer.adobe.com/commerce/cloud-tools/docker/configure/multiple-sites/).

### Pro 環境の設定を更新しました。

{{pro-self-service-warning}}

### 別のドメイン用のルートの設定

ルートは、受信 URL の処理方法を定義します。 一意のドメインを持つ複数の店舗では、 `routes.yaml` ファイル。 ルートの設定方法は、サイトの動作方法によって異なります。

**統合環境でルートを設定するには**:

1. ローカルのワークステーションで、 `.magento/routes.yaml` ファイルを編集します。

1. ドメインとサブドメインを定義します。 The `mymagento` upstream の値は、 `.magento.app.yaml` ファイル。

   ```yaml
   "http://{default}/":
       type: upstream
       upstream: "mymagento:http"
   
   "http://<second-site>.{default}/":
       type: upstream
       upstream: "mymagento:http"
   ```

1. 変更を `routes.yaml` ファイル。

1. 続行 [Web サイト、ストア、ストアの表示を設定する](#set-up-websites-stores-and-store-views).

### 共有ドメインの場所の設定

ルート設定では、URL の処理方法を定義します。 `web` プロパティを `.magento.app.yaml` ファイルは、アプリケーションが Web にどのように公開されるかを定義します。 Web _場所_ 受信リクエストの詳細を許可します。 例えば、ドメインが `store.com`を使用する場合、 `/first` （デフォルトのサイト）および `/second` ドメインを共有する 2 つの異なる店舗へのリクエストに対して。

**新しい Web の場所を設定するには**:

1. ルートのエイリアスを作成します (`/`) をクリックします。 この例では、エイリアスは `&app` を 3 行目に設定します。

   ```yaml
   web:
       locations:
           "/": &app
               # The public directory of the app, relative to its root.
               root: "pub"
               passthru: "/index.php"
               index:
               - index.php
               ...
   ```

1. Web サイトのパススルーを作成します (`/website`) をクリックし、前の手順で作成したエイリアスを使用してルートを参照します。

   エイリアスで `website` をクリックして、ルートの場所から値にアクセスします。 この例では、Web サイトです。 `passthru` は 21 行目にあります。

   ```yaml
   web:
       locations:
           "/": &app
               # The public directory of the app, relative to its root.
               root: "pub"
               passthru: "/index.php"
               index:
               - index.php
               ...
           "/media":
               root: "pub/media"
               ...
           "/static":
               root: "pub/static"
               allow: true
               scripts: false
               passthru: "/front-static.php"
               rules:
                   ^/static/version\d+/(?<resource>.*)$:
                       passthru: "/static/$resource"
           "/<website>": *app
             ...
   ```

**別のディレクトリで場所を設定するには**:

1. ルートのエイリアスを作成します (`/`) および ( 静的な (`/static`) の場所です。

   ```yaml
   web:
       locations:
           "/": &root
               # The public directory of the app, relative to its root.
               root: "pub"
               passthru: "/index.php"
               index:
               - index.php
               ...
           "/static": &static
               root: "pub/static"
   ```

1. Web サイトのサブディレクトリをの下に作成します。 `pub` ディレクトリ： `pub/<website>`

1. をコピーします。 `pub/index.php` ～に入る `pub/<website>` ディレクトリに移動し、 `bootstrap` パス (`/../../app/bootstrap.php`) をクリックします。

   ```
   try {
       require __DIR__ . '/../../app/bootstrap.php';
   } catch (\Exception $e) { 
   ```

1. のパススルーの作成 `index.php` ファイル。

   ```yaml
   web:
       locations:
           "/": &root
               # The public directory of the app, relative to its root.
               root: "pub"
               passthru: "/index.php"
               index:
               - index.php
               ...
           "/media":
               root: "pub/media"
               ...
           "/static": &static
               root: "pub/static"
               allow: true
               scripts: false
               passthru: "/front-static.php"
               rules:
                   ^/static/version\d+/(?<resource>.*)$:
                       passthru: "/static/$resource"
           "/<website>":
               <<: *root
               passthru: "<website>/index.php"
           "/<website>/static": *static
             ...
   ```

1. 変更したファイルをコミットしてプッシュします。

   - `pub/<website>/index.php` ( このファイルが `.gitignore`（プッシュには強制オプションが必要な場合があります）
   - `.magento.app.yaml`

### Web サイト、ストア、ストアの表示を設定する

Adobe Analytics の _管理 UI_、 Adobe Commerceのセットアップ **Web サイト**, **ストア**、および **ストアビュー数**. 詳しくは、 [複数の Web サイト、ストア、管理者での表示の設定](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/multi-sites/ms-admin.html) （内） _設定ガイド_.

ローカルインストールを設定する際には、管理者から Web サイト、ストア、ビューの保存に同じ名前とコードを使用することが重要です。 これらの値は、 `magento-vars.php` ファイル。

### 変数の変更

NGINX 仮想ホストを設定する代わりに、 `MAGE_RUN_CODE` および `MAGE_RUN_TYPE` 変数を `magento-vars.php` ファイルをプロジェクトのルートディレクトリに保存します。

**を使用して変数を渡すには `magento-vars.php` ファイル**:

1. を開きます。 `magento-vars.php` ファイルを編集します。

   The [デフォルト `magento-vars.php` ファイル](https://github.com/magento/magento-cloud/blob/master/magento-vars.php) は次のようになります。

   ```php
   <?php
   // enable, adjust and copy this code for each store you run
   // Store #0, default one
   //if (isHttpHost("example.com")) {
   //    $_SERVER["MAGE_RUN_CODE"] = "default";
   //    $_SERVER["MAGE_RUN_TYPE"] = "store";
   //}
   function isHttpHost($host)
   {
       if (!isset($_SERVER['HTTP_HOST'])) {
           return false;
       }
       return $_SERVER['HTTP_HOST'] === $host;
   }
   ```

1. コメントを移動 `if` それが _次より後_ の `function` ブロックし、コメントを追加しなくなりました。

   ```php
   <?php
   // enable, adjust and copy this code for each store you run
   // Store #0, default one
   
   function isHttpHost($host)
   {
       if (!isset($_SERVER['HTTP_HOST'])) {
           return false;
       }
       return $_SERVER['HTTP_HOST'] ===  $host;
   }
   
   if (isHttpHost("example.com")) {
       $_SERVER["MAGE_RUN_CODE"] = "default";
       $_SERVER["MAGE_RUN_TYPE"] = "store";
   }
   ```

1. 次の値を `if (isHttpHost("example.com"))` ブロック：
   - `example.com` — のベース URL を使用します。 _web サイト_
   - `default` — に、 _web サイト_ または _ストア表示_
   - `store` — 次の値のいずれかを使用します。
      - `website`—load _web サイト_ 店頭で
      - `store` — を読み込みます。 _ストア表示_ 店頭で

   一意のドメインを使用する複数のサイトの場合：

   ```php
   <?php
   function isHttpHost($host)
   {
       if (!isset($_SERVER['HTTP_HOST'])) {
       return false;
       }
       return $_SERVER['HTTP_HOST'] ===  $host;
   }
   
   if (isHttpHost("second.store.com")) {
       $_SERVER["MAGE_RUN_CODE"] = "<second-site>";
       $_SERVER["MAGE_RUN_TYPE"] = "website";
   }elseif (isHttpHost("store.com")){
       $_SERVER["MAGE_RUN_CODE"] = "base";
       $_SERVER["MAGE_RUN_TYPE"] = "website";
   }
   ```

   同じドメインを持つ複数のサイトの場合、 _ホスト_ そして _URI_:

   ```php
   <?php
   function isHttpHost($host)
   {
       if (!isset($_SERVER['HTTP_HOST'])) {
       return false;
       }
       return $_SERVER['HTTP_HOST'] ===  $host;
   }
   
   if (isHttpHost("store.com")) {
      $code = "base";
      $type = "website";
   
      $uri = explode('/', $_SERVER['REQUEST_URI']);
      if (isset($uri[1]) && $uri[1] == 'second') {
        $code = '<second-site>';
        $type = 'website';
      }
      $_SERVER["MAGE_RUN_CODE"] = $code;
      $_SERVER["MAGE_RUN_TYPE"] = $type;
   }
   ```

1. 変更を `magento-vars.php` ファイル。

### 統合サーバーでのデプロイとテスト

変更をクラウドインフラストラクチャの統合環境上のAdobe Commerceにプッシュし、サイトをテストします。

1. コードの変更をリモートブランチに追加、コミット、およびプッシュします。

   ```bash
   git add -A && git commit -m "Implement multiple sites" && git push origin <branch-name>
   ```

1. デプロイメントが完了するまで待ちます。

1. デプロイ後、Web ブラウザーでストア URL を開きます。

   一意のドメインの場合は、次の形式を使用します。 `http://<magento-run-code>.<site-URL>`

   例：`http://french.master-name-projectID.us.magentosite.cloud/`

   共有ドメインの場合は、次の形式を使用します。 `http://<site-URL>/<magento-run-code>`

   例：`http://master-name-projectID.us.magentosite.cloud/french/`

1. サイトを十分にテストし、コードを `integration` ブランチを使用して、さらにデプロイします。

## ステージング環境および実稼動環境へのデプロイ

次のデプロイメントプロセスに従います。 [ステージングおよび実稼動へのデプロイ](../deploy/staging-production.md). Starter 環境と Pro 環境の場合、 [!DNL Cloud Console] を使用して、複数の環境にコードをプッシュします。

Adobeでは、実稼動環境にプッシュする前に、ステージング環境で完全にテストすることをお勧めします。 統合環境でコードを変更し、環境をまたいでデプロイするプロセスを再開します。

<!-- link definitions -->

[config-multiweb]: https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/multi-sites/ms-overview.html
