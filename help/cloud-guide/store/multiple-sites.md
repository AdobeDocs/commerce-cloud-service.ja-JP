---
title: 複数の web サイトまたはストアを設定
description: クラウドインフラストラクチャー上でAdobe Commerce用に複数の web サイトまたはストアを設定する方法について説明します。
feature: Cloud, Configuration, Routes, Site Navigation
exl-id: 16e932ef-f083-44d7-977f-0c78899e151a
source-git-commit: 85aa54af10e7ea44adde5403b69ff03d4a0c622f
workflow-type: tm+mt
source-wordcount: '1013'
ht-degree: 0%

---

# 複数の web サイトまたはストアを設定

英語のストア、フランス語のストア、ドイツ語のストアなど、複数の web サイトやストアを持つようにAdobe Commerceを設定できます。 参照： [Web サイト、ストア、ストア表示について](best-practices.md#store-views).

>[!WARNING]
>
>Web サイトやストアの数を増やすと、カタログデータは大きくなります。 プロジェクトアーキテクチャによっては、追加のストアによって、インデックス作成プロセスが長くなり、キャッシュされていないカタログページの応答時間が遅くなる可能性があります。 Adobeでは、サイトのパフォーマンスを詳細に監視することをお勧めします。

複数のストアを設定するプロセスは、一意のドメインと共有ドメインのどちらを使用するかによって異なります。

一意のドメインを持つ複数のストア：

```terminal
https://first.store.com/
https://second.store.com/
```

同じドメインを持つ複数のストア：

```terminal
https://store.com/first/
https://store.com/second/
```

>[!TIP]
>
>ストアビューをサイトベース URL に追加する場合、複数のディレクトリを作成する必要はありません。 参照： [ベース URL にストアコードを追加します](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/multi-sites/ms-admin.html) が含まれる _設定ガイド_.

## ドメインの追加

カスタムドメインは、ステージング環境および実稼動環境に追加できます。統合環境に追加することはできません。

ドメインを追加するプロセスは、クラウドアカウントのタイプによって異なります。

- ステージングおよび実稼動用

  Fastly に新しいドメインを追加するには、を参照してください。 [ドメインの管理](../cdn/fastly-custom-cache-configuration.md#manage-domains)または、サポートチケットを開いてサポートをリクエストします。 さらに、次の操作が必要です [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) 新しいドメインを要求してクラスターに追加します。

- スターター実稼動用のみ

  Fastly に新しいドメインを追加するには、を参照してください。 [ドメインの管理](../cdn/fastly-custom-cache-configuration.md#manage-domains)、または [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) お問い合わせください。 また、新しいドメインをに追加する必要があります **ドメイン** タブ [!DNL Cloud Console]: `https://<zone>.magento.cloud/projects/<project-ID>/edit`

## ローカルインストールの設定

複数のストアを使用するようにローカル インストールを設定するには、を参照してください。 [複数の web サイトまたはストア][config-multiweb] が含まれる _設定ガイド_.

ローカルインストールを正常に作成およびテストして複数のストアを使用したら、統合環境の準備を行う必要があります。

1. **ルートまたは場所の設定** – 受信 URL がAdobe Commerceでどのように処理されるかを指定する

   - [個別のドメインのルート](#configure-routes-for-separate-domains)
   - [共有ドメインの場所](#configure-locations-for-shared-domains)

1. **Web サイト、ストア、ストアビューの設定**—Adobe Commerce管理 UI を使用してを設定します
1. **変数の変更** – の値を指定します。 `MAGE_RUN_TYPE` および `MAGE_RUN_CODE` の変数 `magento-vars.php` ファイル
1. **環境のデプロイとテスト** – をデプロイしてテストします。 `integration` 分岐

>[!TIP]
>
>ローカル環境を使用して、複数の web サイトやストアを設定できます。 詳しくは、Cloud Docker の手順を参照してください [複数の web サイトまたはストアを設定](https://developer.adobe.com/commerce/cloud-tools/docker/configure/multiple-sites/).

### Pro 環境の設定アップデート

{{pro-self-service-warning}}

### 個別のドメインのルートの設定

ルートは、受信 URL の処理方法を定義します。 一意のドメインを持つ複数のストアでは、 `routes.yaml` ファイル。 ルートを設定する方法は、サイトの動作方法によって異なります。

**統合環境でルートを設定するには**:

1. ローカルワークステーションで、を開きます `.magento/routes.yaml` ファイルをテキストエディターで開きます。

1. ドメインとサブドメインを定義します。 この `mymagento` アップストリームの値は、の名前プロパティと同じ `.magento.app.yaml` ファイル。

   ```yaml
   "http://{default}/":
       type: upstream
       upstream: "mymagento:http"
   
   "http://<second-site>.{default}/":
       type: upstream
       upstream: "mymagento:http"
   ```

1. 変更をに保存します。 `routes.yaml` ファイル。

1. 続行 [Web サイト、ストア、ストアビューの設定](#set-up-websites-stores-and-store-views).

### 共有ドメインの場所の設定

routes 設定で URL の処理方法が定義されている場合、 `web` のプロパティ `.magento.app.yaml` ファイルは、アプリケーションを web に公開する方法を定義します。 Web _の場所_ 受信リクエストの精度を高めることができます。 例えば、ドメインがの場合 `store.com`、以下を使用できます `/first` （デフォルトサイト）と `/second` 1 つのドメインを共有する 2 つの異なるストアへのリクエスト。

**新しい Web の場所を構成するには**:

1. ルートのエイリアス（`/`）に設定します。 この例では、エイリアスはです。 `&app` 3 行目。

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

1. Web サイトのパススルーを作成します（`/website`）を選択し、前の手順で取得したエイリアスを使用してルートを参照します。

   エイリアスは `website` を使用して、ルートの場所から値にアクセスします。 この例では、web サイトです `passthru` は 21 行目です。

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

**別のディレクトリを使用して場所を構成するには**:

1. ルートのエイリアス（`/`）と、静的（`/static`）の場所。

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

1. 以下に web サイトのサブディレクトリを作成します。 `pub` ディレクトリ： `pub/<website>`

1. をコピーします `pub/index.php` にファイルを送信 `pub/<website>` ディレクトリを更新し、 `bootstrap` パス （`/../../app/bootstrap.php`）に設定します。

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

   - `pub/<website>/index.php` （このファイルの場所 `.gitignore`（プッシュには、力オプションが必要な場合があります）。
   - `.magento.app.yaml`

### Web サイト、ストア、ストアビューの設定

が含まれる _管理 UI_、Adobe Commerceを設定します **Web サイト**, **ストア**、および **ストアの表示**. 参照： [管理での複数の web サイト、ストア、ストア表示の設定](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/multi-sites/ms-admin.html) が含まれる _設定ガイド_.

ローカルインストールを設定する際には、管理者が web サイト、ストア、ストアビューと同じ名前とコードを使用することが重要です。 これらの値は、 `magento-vars.php` ファイル。

### 変数の変更

NGINX 仮想ホストを設定する代わりに、を渡します `MAGE_RUN_CODE` および `MAGE_RUN_TYPE` を使用した変数 `magento-vars.php` ファイルをプロジェクトのルートディレクトリに置きます。

**を使用して変数を渡すには： `magento-vars.php` ファイル**:

1. を開きます `magento-vars.php` ファイルをテキストエディターで開きます。

   この [default `magento-vars.php` ファイル](https://github.com/magento/magento-cloud/blob/master/magento-vars.php) 次のようになります。

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

1. コメントを移動 `if` ブロックする _後_ この `function` ブロックし、コメントを終了しました。

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

1. で次の値を `if (isHttpHost("example.com"))` ブロック :
   - `example.com` – のベース URL _web サイト_
   - `default` – 固有のコードを _web サイト_ または _ストア表示_
   - `store` – 次のいずれかの値を使用します。
      - `website` – をロードする _web サイト_ 店頭で
      - `store` – をロードする _ストア表示_ 店頭で

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

   同じドメインを持つ複数のサイトの場合は、 _host_ および _URI_:

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

1. 変更をに保存します。 `magento-vars.php` ファイル。

### 統合サーバーへのデプロイとテスト

変更内容をAdobe Commerce on cloud infrastructure integration environment にプッシュし、サイトをテストします。

1. コードの変更をリモートブランチに追加、コミットおよびプッシュします。

   ```bash
   git add -A && git commit -m "Implement multiple sites" && git push origin <branch-name>
   ```

1. デプロイメントが完了するまで待ちます。

1. デプロイメント後、Web ブラウザーでストア URL を開きます。

   一意のドメインでは、次の形式を使用します。 `http://<magento-run-code>.<site-URL>`

   例：`http://french.master-name-projectID.us.magentosite.cloud/`

   共有ドメインでは、次の形式を使用します。 `http://<site-URL>/<magento-run-code>`

   例：`http://master-name-projectID.us.magentosite.cloud/french/`

1. サイトを徹底的にテストし、コードをに結合します `integration` 分岐してさらにデプロイします。

## ステージング環境および実稼動環境にデプロイ

のデプロイメントプロセスに従います [ステージング環境および実稼動環境へのデプロイ](../deploy/staging-production.md). Starter 環境と Pro 環境の場合は、 [!DNL Cloud Console] 環境全体にコードをプッシュします。

Adobeでは、実稼動環境にプッシュする前に、ステージング環境で完全にテストすることをお勧めします。 統合環境でコードを変更し、環境全体にデプロイするプロセスを再度開始します。

<!-- link definitions -->

[config-multiweb]: https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/multi-sites/ms-overview.html
