---
title: Fastly サービスの設定
description: Adobe Commerce プロジェクトに Fastly サービスをセットアップし設定する方法について説明します。
feature: Cloud, Configuration, Iaas, Cache, Security
exl-id: c53ff3bd-3df2-45fb-933e-d3b29f7edf4e
source-git-commit: 8a0523f1714b6ea41887e99b5c31294cf5e5255e
workflow-type: tm+mt
source-wordcount: '1961'
ht-degree: 0%

---

# Fastly サービスの設定

Fastly は、クラウドインフラストラクチャー上のAdobe Commerceのステージング環境および実稼動環境で必要です。

Fastly は Varnish と連携して高速なキャッシュ機能と [コンテンツ配信ネットワーク](https://glossary.magento.com/content-delivery-network) （CDN）静的アセットの場合。 また、Fastly は、サイトとクラウドインフラストラクチャを保護する web アプリケーションファイアウォール（WAF）も提供しています。 悪意のあるトラフィックや攻撃からサイトやクラウドインフラストラクチャを保護するには、すべての受信サイトトラフィックを Fastly 経由でルーティングします。

>[!NOTE]
>
>Fastly は、統合環境では使用できません。

サイト開発プロセスの早い段階で Fastly を有効にし、設定し、テストして、サイトへの安全なアクセスを可能にするには、次の手順を実行します。

- ステージング環境と実稼動環境での Fastly 資格情報の取得
- Fastly CDN キャッシュの有効化
- Fastly VCL スニペットのアップロード
- Fastly サービスにトラフィックをルーティングするように DNS 設定を更新します。
- Fastly キャッシュのテスト

>[!NOTE]
>
>Fastly の初期設定を有効にして確認したら、設定をカスタマイズできます。 例えば、画像の最適化、エッジモジュール、カスタム VCL コードなどの追加オプションを有効にできます。 参照： [キャッシュ設定のカスタマイズ](fastly-custom-cache-configuration.md).

## Fastly 資格情報の取得

プロジェクトのプロビジョニング時に、Adobeはプロジェクトをプロジェクトに追加します [Fastly サービスアカウント](fastly.md#fastly-service-account-and-credentials) クラウドインフラストラクチャ上のAdobe Commerceの場合、およびスターターの Fastly アカウント資格情報を作成します `master` およびステージング環境と実稼動環境 各環境には一意の資格情報があります。

管理者から Fastly CDN サービスを設定し、Fastly API リクエストを送信するには、Fastly 資格情報が必要です。

>[!NOTE]
>
>クラウドインフラストラクチャー上のAdobe Commerceを使用して、Fastly Admin に直接アクセスすることはできません。 管理者を使用して、お使いの環境の Fastly 設定を確認および更新します。 管理者の Fastly 機能を使用して問題を解決できない場合は、 [Adobe Commerce サポートチケット](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket).

以下の方法を使用して、お使いの環境の Fastly サービス ID と API トークンを検索して保存します。

**Fastly 資格情報を表示するには**:

資格情報の表示方法は、Pro プロジェクトと Starter プロジェクトで異なります。

- IaaS でマウントされた共有ディレクトリ - Pro プロジェクトでは、SSH を使用してサーバーに接続し、から Fastly 資格情報を取得します `/mnt/shared/fastly_tokens.txt` ファイル。 ステージング環境と実稼動環境には、一意の資格情報があります。 各環境の資格情報を取得する必要があります。

- ローカルワークスペース – コマンドラインから、を使用します。 `magento-cloud` CLI からへ [リストとレビュー](../environment/variables-cloud.md#viewing-environment-variables) Fastly 環境変数。

  ```bash
  magento-cloud variable:get -e <environment-ID>
  ```

- [!DNL Cloud Console] – 以下の環境変数を [環境設定](../project/overview.md#configure-environment).

   - `CONFIG__DEFAULT__SYSTEM__FULL_PAGE_CACHE__FASTLY__FASTLY_API_KEY`

   - `CONFIG__DEFAULT__SYSTEM__FULL_PAGE_CACHE__FASTLY__FASTLY_SERVICE_ID`

>[!NOTE]
>
>ステージング環境または実稼動Adobeの Fastly 資格情報が見つからない場合は、環境のカスタマーテクニカルアドバイザー（CTA）にお問い合わせください。

## Fastly キャッシュの有効化

Fastly サービスを有効にして設定するには、次のコンポーネントが必要です。

- の最新バージョン [Magento 2 用 Fastly CDN モジュール](fastly.md#fastly-cdn-module-for-magento-2) ステージング環境と実稼動環境にインストールされます。 参照： [Fastly へのアップグレード](#upgrade-the-fastly-module).

- [Fastly 資格情報](#get-fastly-credentials) クラウドインフラストラクチャー上のAdobe Commerceの場合ステージング環境と実稼動環境

**ステージング環境と実稼動環境で Fastly CDN キャッシュを有効にするには**:

{{admin-login-step}}

1. クリック **ストア** > 設定 > **設定** > **詳細** > **システム** を展開します **フルページキャッシュ**.

   ![展開して Fastly を選択](../../assets/cdn/fastly-menu.png)

1. が含まれる _キャッシュアプリケーション_ セクションで、から選択を削除します **システム値を使用**&#x200B;を選択してから、 **Fastly CDN** ドロップダウンリストから選択します。

   ![Fastly を選択](../../assets/cdn/fastly-enable-admin.png)

1. を展開 **Fastly 設定** および [キャッシュオプションを選択](https://github.com/fastly/fastly-magento2/blob/master/Documentation/CONFIGURATION.md#configure-the-module).

1. キャッシュオプションを設定したら、 **設定を保存** ページの上部

1. 通知に従ってキャッシュをクリアします。

1. に戻って、Fastly の設定を続けます。 **ストア** > **設定** > **設定** > **詳細** > **システム** > **Fastly 設定**.

### Fastly 資格情報のテスト

1. 管理者で、に移動します **ストア** > 設定 > **設定** > **詳細** > **システム** > **Fastly 設定**.

1. 必要に応じて、を追加します **Fastly サービス ID** および **API トークン** プロジェクト環境の値。

   ![Fastly 資格情報管理者](../../assets/cdn/fastly-credentials-admin-ui.png)

   >[!NOTE]
   >
   >Fastly API トークンを作成するには、リンクを選択しないでください。 代わりに、を使用します [Adobeから提供された Fastly 資格情報（サービス ID および API トークン）](#get-fastly-credentials) Adobeが提供。

1. クリック **テスト資格情報**.

1. テストが成功した場合は、 **設定を保存**&#x200B;次に、キャッシュをクリアします。

   テストが失敗した場合は、正しいサービス ID と API トークンの値が、現在の環境の資格情報と一致することを確認します。

   テストが再び失敗した場合は、Adobe Commerce サポートチケットを送信するか、Adobeアカウント担当者にお問い合わせください。 Pro プロジェクトの場合は、実稼動サイトとステージングサイトの URL を含めます。 スタータープロジェクトの場合は、の URL を含めます `Master` およびステージングサイト。

>[!NOTE]
>
>ステージング環境または実稼動環境用の Fastly API トークン資格情報を変更する手順については、を参照してください。 [Fastly 資格情報の変更](fastly.md#change-fastly-api-token).

### Fastly への VCL のアップロード

Fastly モジュールを有効にしたら、デフォルトの [VCL コード](https://github.com/fastly/fastly-magento2/tree/master/etc/vcl_snippets) Fastly サーバーに送信します。 このコードは、クラウドインフラストラクチャ上のAdobe Commerceのキャッシュおよびその他の Fastly CDN サービスを有効にするための設定を指定する一連の VCL スニペットを提供します。

>[!NOTE]
>
>Fastly キャッシュサービスは、Adobe Commerceのステージングサイトおよび実稼動サイトへの Fastly VCL コードの最初のアップロードを完了するまで機能しません。

**Fastly VCL をアップロードするには**:

1. が含まれる _Fastly 設定_ セクションで、をクリック **Fastly への VCL のアップロード** 次の図に示すように。

   ![Fastly へのMagento VCL のアップロード](../../assets/cdn/fastly-upload-vcl-admin.png)

1. アップロードが完了したら、ページ上部の通知に従ってキャッシュを更新します。

## SSL/TLS 証明書のプロビジョニング

Adobeは、ドメインで検証された Let’s Encrypt SSL/TLS 証明書を提供し、Fastly から安全な HTTPS トラフィックを提供します。 Adobeでは、Pro Production （実稼動）、Staging （ステージング）、Starter Production （スターター）の各環境に対して 1 つの証明書を提供し、その環境内のすべてのドメインを保護します。 提供される証明書の詳細については、を参照してください [クラウドインフラストラクチャー上のAdobe CommerceのAdobe SSL （TLS）証明書](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/how-to/ssl-tls-certificates-for-magento-commerce-cloud-faq.html).

>[!NOTE]
>
>Adobeが提供する Let’s Encrypt 証明書を使用する代わりに、独自の TLS または SSL 証明書を指定できます。 ただし、このプロセスでは、の設定と保守に追加の作業が必要です。 このオプションを選択するには、Adobe Commerce サポートチケットを送信するか、Adobeと連携してクラウドインフラストラクチャ環境のAdobe Commerceにカスタムのホスト型証明書を追加します。

Adobe CommerceAdobeで SSL/TLS 証明書を有効にするには、環境の自動処理で次の手順が実行されます。

- ドメイン所有権を検証します
- ストアに対して指定されたトップレベルとサブドメインに対応する Let’s Encrypt SSL/TLS 証明書をプロビジョニングします
- サイトが稼動しているときに証明書をクラウド環境にアップロードします

この自動処理では、ドメイン検証情報を提供するために、サイトの DNS 設定を更新する必要があります。 使用方法 **1** 次のいずれかの方法を使用します。

- **DNS 検証** – ライブサイトの場合、Fastly サービスを指す CNAME レコードで DNS 設定を更新します
- **ACME チャレンジ CNAME レコード** – 環境内のドメインごとに、Adobeから提供される ACME チャレンジ CNAME レコードを使用して DNS 構成を更新します

>[!TIP]
>
>アクティブでない実稼動ドメインがある場合は、ACME チャレンジ CNAME レコードをドメインの検証に使用します。 レコードを DNS 設定の早期段階に追加すると、Adobeは、サイトの起動前に、SSL/TLS 証明書を正しいドメインにプロビジョニングできます。 実稼動環境に起動する前に、これらのプレースホルダーレコードをAdobeから提供された CNAME レコードに置き換える必要があります。

ドメインの検証が完了すると、Adobeによって Let’s Encrypt TLS/SSL 証明書がプロビジョニングされ、実稼働のステージング環境または実稼動環境にアップロードされます。 この処理には、最大 12 時間かかる場合があります。 サイトの開発とサイトの立ち上げの遅延を防ぐために、DNS 設定の更新は数日前に行うことをお勧めします。

## 開発設定で DNS 設定を更新します

Fastly の初期セットアッププロセス中に、次の URL を使用して、ステージング環境と実稼動環境で Fastly キャッシュを設定およびテストできます。

- ステージング環境および実稼動環境を想定した場合：

   - `mcprod.<your-domain>.com`
   - `mcstaging.<your-domain>.com`

- スターター実稼動の場合のみ：

   - `mcprod.<your-domain>.com`

これらのデフォルトの実稼動前 URL は、プロジェクトのプロビジョニング後に使用できます。 の値 `"your-domain"` は、オンボーディングプロセス中に指定したドメイン名です。

>[!NOTE]
>
>スタータープロジェクトでは、実稼動以外の環境にカスタムドメインを指定することはできません。

ストア URL から Fastly サービスにトラフィックをルーティングするには、DNS 設定を更新します。 設定を更新すると、Adobeによって必要な SSL/TLS 証明書が自動的にプロビジョニングされ、クラウド環境にアップロードされます。 このプロビジョニングには最大 12 時間かかることがあります。

>[!NOTE]
>
>実稼動サイトを起動する準備が整ったら、DNS 設定を再度更新して、実稼動ドメインを Fastly サービスに指定し、追加の設定タスクを実行する必要があります。 参照： [Launch チェックリスト](../launch/checklist.md).

**前提条件：**

- Fastly モジュールを有効にします。
- デフォルトの Fastly VCL コードをアップロードします。
- Adobeする各環境の最上位ドメインとサブドメインのリストを指定するか、Adobe Commerce サポートチケットを送信します。
- 指定したドメインがクラウド環境に追加されたことを確認するまで待ちます。
- スタータープロジェクトでは、ドメインを Fastly サービス設定に追加します。 参照： [ドメインの管理](fastly-custom-cache-configuration.md#manage-domains).
- DNS 設定の更新については、次の URL にお問い合わせください。 [DNS レジストラー](https://lookup.icann.org/) （ドメインサービスの正しい方法用）。

**開発用に DNS 設定を更新するには**:

1. CNAME レコードを追加して、実稼動前の URL を Fastly サービスに指定します。 `prod.magentocloud.map.fastly.net`例：

   | ドメインまたはサブドメイン | CNAME |
   |---------------------------|----------------------------------|
   | mcprod.your-domain.com | prod.magentocloud.map.fastly.net |
   | mcstaging.your-domain.com | prod.magentocloud.map.fastly.net |

   CNAME レコードがライブの場合、Adobeは証明書をプロビジョニングし、SSL/TLS 証明書をアップロードします。

   >[!NOTE]
   >
   >apex ドメインを使用する予定の場合（`your-domain.com`）を使用して、Fastly サーバーの IP アドレスを指すように DNS アドレスレコード（A レコード）を設定する必要があります。 参照： [実稼働設定で DNS 設定を更新します](../launch/checklist.md#to-update-dns-configuration-for-site-launch).


1. ドメインの検証と実稼動 SSL/TLS 証明書の事前プロビジョニングのために、ACME チャレンジ CNAME レコードを追加します。次に例を示します。

   | ドメインまたはサブドメイン | CNAME |
   |-------------------------------------------|-------------------------------------------|
   | _acme-challenge.your-domain.com | 0123456789abcdef.validation.magento.cloud |
   | _acme-challenge.www.your-domain.com | 9573186429stuvwx.validation.magento.com |
   | _acme-challenge.mystore.your-domain.com | 1234567898zxywvu.validation.magento.cloud |
   | _acme-challenge.subdomain.your-domain.com | 1098765743lmnopq.validation.magento.cloud |

   >[!NOTE]
   >
   >この例の ACME チャレンジ レコードは、Adobe Commerceのステージングサイトと実稼動サイトをプロビジョニングするためのプレースホルダーではありません。 Adobeに連絡して、プロジェクトの正しい ACME チャレンジ記録情報を取得してください。

   CNAME レコードを追加した後、Adobeはドメインを検証し、環境用の SSL/TLS 証明書をプロビジョニングします。 これらのドメインから Fastly サービスにトラフィックをルーティングするように DNS コンフィギュレーションを更新すると、Adobeは証明書をアップロードします。

1. Adobe Commerceのベース URL を更新します。

   - SSH を使用して実稼動環境にログインします。

     ```bash
     magento-cloud ssh
     ```

   - Cloud CLI を使用して、ストアのベース URL を変更します。

     ```bash
     php bin/magento setup:store-config:set --base-url="https://mcstaging.your-domain.com/"
     ```

   >[!NOTE]
   >
   >Cloud CLI を使用する代わりに、からベース URL を更新できます。 [Admin](https://experienceleague.adobe.com/docs/commerce-admin/stores-sales/site-store/store-urls.html)

1. Web ブラウザーを再起動します。

1. Web サイトをテストします。

## Fastly キャッシュのテスト

DNS 設定の変更が完了したら、 [cURL](https://curl.se/) fastly キャッシュが機能していることを確認するコマンドラインツール。

**応答ヘッダーを確認するには**:

1. ターミナルでは、次を使用します。 `curl` ライブサイト URL をテストするコマンド：

   ```bash
   curl -vo /dev/null -H Fastly-Debug:1 https://<live-URL>
   ```

   静的ルートを設定していない場合や、ライブサイト上のドメインの DNS 設定が完了している場合は、を使用します `--resolve` DNS の名前解決をバイパスするフラグ。

   ```bash
   curl -vo /dev/null -H Fastly-Debug:1 --resolve <live-URL-hostname>:443:<live-IP-address>
   ```

1. 応答で、を検証します [ヘッダー](fastly-troubleshooting.md#check-cache-hit-and-miss-response-headers) を使用して、Fastly が機能していることを確認します。 応答に次の一意のヘッダーが表示されます。

   ```http
   < Fastly-Magento-VCL-Uploaded: yes
   < X-Cache: HIT, MISS
   ```

ヘッダーに正しい値がない場合は、を参照してください。 [応答ヘッダーで見つかったエラーの解決](fastly-troubleshooting.md#curl) トラブルシューティングのヘルプ。

## Fastly モジュールのアップグレード

Fastly は、Magento 2 用 Fastly CDN モジュールを更新して、問題の解決、パフォーマンスの向上、新機能の提供を行います。
ステージング環境および実稼動環境での Fastly モジュールをに更新することをお勧めします [最新バージョン](https://github.com/fastly/fastly-magento2/blob/master/VERSION).

モジュールを更新したら、VCL コードをアップロードして、変更を Fastly サービス設定に適用する必要があります。

>[!WARNING]
>
> デフォルトの Fastly VCL コードをカスタムバージョンでカスタマイズしている場合、Fastly モジュールをアップグレードすると、変更内容が上書きされます。 一意の名前を持つカスタム VCL スニペットを追加した場合、その変更はアップグレード プロセス中も保持されます。 ベストプラクティスとしては、ステージング環境をアップグレードし、変更を検証してから実稼動環境に適用します。

**Magento 2 の Fastly CDN モジュールのバージョンを確認するには**:

1. をクラウド環境のルートディレクトリに変更します。

1. Composer を使用して、インストールされているバージョンをチェックします。

   ```bash
   composer show *fastly*
   ```

1. 次の場合 [最新リリース](https://github.com/fastly/fastly-magento2/releases) がインストールされていない場合は、Fastly モジュールをアップグレードする手順を実行します。

**Fastly モジュールをアップグレードするには**:

1. ローカル統合環境では、次のモジュール情報を使用して以下を行います。 [fastly モジュールのアップグレード](../store/extensions.md#upgrade-an-extension).

   ```text
   module name: fastly/magento2
   repository: https://github.com/fastly/fastly-magento2.git
   ```

1. 更新内容をステージング環境にプッシュします。

1. ステージング環境用の管理者にログインして、次の操作を行います [vcl コードのアップロード](#upload-vcl-to-fastly).

1. [Fastly サービスの検証](fastly-troubleshooting.md#verify-or-debug-fastly-services) Adobe Commerce ステージングサイトで、

ステージングサイトで Fastly サービスを確認したら、実稼動環境でアップグレードプロセスを繰り返します。

>[!TIP]
>
> Adobe Commerce環境で Fastly サービスに関する問題が発生した場合は、以下を参照してください [Adobe Commerce Fastly に関するトラブルシューティング](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/magento-fastly-troubleshooter.html).
