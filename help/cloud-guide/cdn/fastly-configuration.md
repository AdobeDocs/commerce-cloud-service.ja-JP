---
title: Fastly サービスの設定
description: Adobe Commerceプロジェクト用に Fastly サービスを設定する方法を説明します。
feature: Cloud, Configuration, Iaas, Cache, Security
exl-id: c53ff3bd-3df2-45fb-933e-d3b29f7edf4e
source-git-commit: 8a0523f1714b6ea41887e99b5c31294cf5e5255e
workflow-type: tm+mt
source-wordcount: '1961'
ht-degree: 0%

---

# Fastly サービスの設定

Fastly は、クラウドインフラストラクチャのステージング環境と実稼動環境のAdobe Commerceに必要です。

Vanish との連携により、高速なキャッシュ機能と [コンテンツ配信ネットワーク](https://glossary.magento.com/content-delivery-network) (CDN) を使用します。 また、Fastly は、サイトおよびクラウドインフラストラクチャを保護する Web アプリケーションファイアウォール (WAF) も提供しています。 悪意のあるトラフィックや攻撃からサイトやクラウドインフラストラクチャを保護するには、Fastly を通じてすべての着信サイトトラフィックをルーティングします。

>[!NOTE]
>
>Fastly は、統合環境では使用できません。

サイトの開発プロセスの早い段階で Fastly を有効にし、設定し、テストを行って、サイトへのセキュアなアクセスを有効にします。

- ステージング環境および実稼動環境の Fastly 資格情報の取得
- Fastly CDN キャッシュを有効にする
- Fastly VCL スニペットをアップロード
- トラフィックを Fastly サービスにルーティングするための DNS 設定の更新
- Fastly キャッシュのテスト

>[!NOTE]
>
>初期の Fastly 設定を有効にして確認した後、設定をカスタマイズできます。 例えば、画像の最適化、エッジモジュール、カスタム VCL コードなどの追加のオプションを有効にできます。 詳しくは、 [キャッシュ設定のカスタマイズ](fastly-custom-cache-configuration.md).

## Fastly 認証情報の取得

プロジェクトのプロビジョニング中に、Adobeはプロジェクトを [Fastly サービスアカウント](fastly.md#fastly-service-account-and-credentials) クラウドインフラストラクチャ上のAdobe Commerceの場合は、Starter の Fastly アカウント資格情報を作成します。 `master` 実稼動環境および実稼動環境に対応しています。 各環境には固有の資格情報があります。

管理者から Fastly CDN サービスを設定し、Fastly API リクエストを送信するには、Fastly 資格情報が必要です。

>[!NOTE]
>
>クラウドインフラストラクチャ上のAdobe Commerceを使用すると、Fastly Admin に直接アクセスできません。 管理者を使用して、環境の Fastly 設定を確認および更新します。 管理者の Fastly 機能を使用して問題を解決できない場合は、 [Adobe Commerceサポートチケット](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket).

次の方法を使用して、環境の Fastly サービス ID と API トークンを検索し、保存します。

**Fastly 資格情報を表示するには**:

認証情報の表示方法は、Pro プロジェクトと Starter プロジェクトで異なります。

- IaaS マウントの共有ディレクトリ —Pro プロジェクトでは、SSH を使用してサーバーに接続し、Fastly 認証情報を `/mnt/shared/fastly_tokens.txt` ファイル。 ステージング環境と実稼動環境には、一意の資格情報が割り当てられます。 各環境の資格情報を取得する必要があります。

- ローカルワークスペース — コマンドラインから、 `magento-cloud` CLI で [リストとレビュー](../environment/variables-cloud.md#viewing-environment-variables) 基本的な環境変数。

  ```bash
  magento-cloud variable:get -e <environment-ID>
  ```

- [!DNL Cloud Console]— [環境の設定](../project/overview.md#configure-environment).

   - `CONFIG__DEFAULT__SYSTEM__FULL_PAGE_CACHE__FASTLY__FASTLY_API_KEY`

   - `CONFIG__DEFAULT__SYSTEM__FULL_PAGE_CACHE__FASTLY__FASTLY_SERVICE_ID`

>[!NOTE]
>
>ステージング環境または実稼動環境の Fastly 資格情報が見つからない場合は、Adobeのカスタマーテクニカルアドバイザー (CTA) にお問い合わせください。

## Fastly キャッシュを有効にする

Fastly サービスを有効にして設定するには、次のコンポーネントが必要です。

- の最新バージョン [Magento2 モジュールの Fastly CDN](fastly.md#fastly-cdn-module-for-magento-2) ステージング環境および実稼動環境にインストールされます。 詳しくは、 [Fastly のアップグレード](#upgrade-the-fastly-module).

- [Fastly 認証情報](#get-fastly-credentials) クラウドインフラストラクチャのステージング環境および実稼動環境のAdobe Commerce

**ステージングおよび実稼動環境で Fastly CDN キャッシュを有効にするには**:

{{admin-login-step}}

1. クリック **ストア** /設定/ **設定** > **詳細** > **システム** を展開します。 **フルページキャッシュ**.

   ![展開して Fastly を選択します。](../../assets/cdn/fastly-menu.png)

1. Adobe Analytics の _Caching Application_ セクションで、次の場所から選択を削除します。 **システム値を使用**&#x200B;を選択し、 **Fastly CDN** 」をドロップダウンリストから選択します。

   ![Fastly を選択](../../assets/cdn/fastly-enable-admin.png)

1. 展開 **Fastly 設定** および [キャッシュオプションを選択](https://github.com/fastly/fastly-magento2/blob/master/Documentation/CONFIGURATION.md#configure-the-module).

1. キャッシュオプションを設定したら、 **設定を保存** をクリックします。

1. 通知に従ってキャッシュをクリアします。

1. に戻って Fastly の設定を続行します。 **ストア** > **設定** > **設定** > **詳細** > **システム** > **Fastly 設定**.

### Fastly 認証情報のテスト

1. 管理者で、に移動します。 **ストア** /設定/ **設定** > **詳細** > **システム** > **Fastly 設定**.

1. 必要に応じて、 **Fastly サービス ID** および **API トークン** プロジェクト環境の値。

   ![Fastly 認証情報管理者](../../assets/cdn/fastly-credentials-admin-ui.png)

   >[!NOTE]
   >
   >リンクを選択して Fastly API トークンを作成しないでください。 代わりに、 [Adobeが提供する Fastly 資格情報（サービス ID と API トークン）](#get-fastly-credentials) Adobeが提供

1. クリック **認証情報のテスト**.

1. テストが成功した場合は、「 **設定を保存**&#x200B;キャッシュをクリアします。

   テストが失敗した場合は、正しいサービス ID と API トークンの値が、現在の環境の資格情報と一致していることを確認します。

   テストが再び失敗した場合は、Adobe Commerceサポートチケットを送信するか、Adobeアカウント担当者にお問い合わせください。 Pro プロジェクトの場合は、実稼動サイトとステージングサイトの URL を含めます。 スタータープロジェクトの場合は、 `Master` ステージングサイト

>[!NOTE]
>
>ステージング環境または実稼動環境の Fastly API トークン資格情報を変更する手順については、 [Fastly 資格情報の変更](fastly.md#change-fastly-api-token).

### VCL を Fastly にアップロード

Fastly モジュールを有効にした後、デフォルトの [VCL コード](https://github.com/fastly/fastly-magento2/tree/master/etc/vcl_snippets) Fastly サーバーに送信します。 このコードは、クラウドインフラストラクチャ上のAdobe Commerceに対するキャッシュおよびその他の Fastly CDN サービスを有効にする設定を指定する一連の VCL スニペットを提供します。

>[!NOTE]
>
>Fastly キャッシュサービスは、Fastly VCL コードのAdobe Commerceのステージングおよび実稼動サイトへの最初のアップロードが完了するまでは機能しません。

**Fastly VCL をアップロードするには**:

1. Adobe Analytics の _Fastly 設定_ セクションで、 **VCL を Fastly にアップロード** を次の図に示します。

   ![Fastly にMagentoVCL をアップロード](../../assets/cdn/fastly-upload-vcl-admin.png)

1. アップロードが完了したら、ページ上部の通知に従ってキャッシュを更新します。

## SSL/TLS 証明書のプロビジョニング

Adobeがドメイン検証済みの証明書を提供します。 Fastly からの安全な HTTPS トラフィックを提供するには、SSL/TLS 証明書を暗号化します。 Adobeは、Pro Production、Staging、および Starter Production 環境ごとに 1 つの証明書を提供し、その環境内のすべてのドメインを保護します。 提供された証明書について詳しくは、 [クラウドインフラストラクチャ上のAdobe Commerce用のAdobeSSL (TLS) 証明書](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/how-to/ssl-tls-certificates-for-magento-commerce-cloud-faq.html).

>[!NOTE]
>
>「Let&#39;s Encrypt certificate provided by」Adobeを使用する代わりに、独自の TLS または SSL 証明書を指定できます。 ただし、このプロセスでは、設定と保守をおこなうために追加の作業が必要です。 このオプションを選択するには、Adobe Commerceサポートチケットを送信するか、Adobeと協力して、クラウドインフラストラクチャ環境上のAdobe Commerceにカスタムのホスト証明書を追加します。

Adobe Commerce環境で SSL/TLS 証明書を有効にするには、Adobeの自動化で次の手順を実行します。

- ドメインの所有権を検証します
- ストアの指定されたトップレベルドメインとサブドメインをカバーする SSL/TLS 証明書を暗号化しよう
- サイトがライブになっている場合に、証明書をクラウド環境にアップロードします

この自動化では、ドメインの検証情報を提供するために、サイトの DNS 構成を更新する必要があります。 用途 **1 つ** 次のメソッドのいずれかを指定します。

- **DNS 検証** — ライブサイトの場合、Fastly サービスを指す CNAME レコードを使用して DNS 設定を更新します
- **ACME チャレンジ CNAME レコード** — 環境内の各ドメインに対してAdobeが提供する ACME チャレンジ CNAME レコードを使用して、DNS 設定を更新します。

>[!TIP]
>
>アクティブでない実稼動ドメインがある場合は、ドメイン検証に ACME チャレンジ CNAME レコードを使用します。 DNS 設定にレコードを早期に追加すると、Adobeは、サイトを起動する前に、SSL/TLS 証明書に正しいドメインをプロビジョニングできます。 実稼動環境に移行する前に、これらのプレースホルダーレコードを、Adobeから提供された CNAME レコードに置き換える必要があります。

ドメインの検証が完了すると、Adobeは「 Let&#39;s Encrypt TLS/SSL 証明書」をプロビジョニングし、実稼働ステージング環境または実稼働環境にアップロードします。 この処理には最大 12 時間かかる場合があります。 サイト開発やサイトの立ち上げの遅延を防ぐため、数日前に DNS 設定の更新を完了することをお勧めします。

## 開発設定で DNS 構成を更新する

初回 Fastly セットアッププロセスの間、以下の URL を使用して、ステージング環境および実稼動環境で Fastly キャッシュを設定およびテストできます。

- Pro Staging および Production の場合：

   - `mcprod.<your-domain>.com`
   - `mcstaging.<your-domain>.com`

- スタータープロダクションの場合のみ：

   - `mcprod.<your-domain>.com`

これらのデフォルトの実稼動前 URL は、プロジェクトのプロビジョニング後に使用できます。 の値 `"your-domain"` は、オンボーディングプロセス中に指定したドメイン名です。

>[!NOTE]
>
>スタータープロジェクトの実稼動環境以外の環境に対しては、カスタムドメインを指定できません。

ストア URL から Fastly サービスにトラフィックをルーティングするには、DNS 設定を更新します。 設定を更新すると、Adobeは必要な SSL/TLS 証明書を自動的にプロビジョニングし、クラウド環境にアップロードします。 このプロビジョニングには、最大で 12 時間かかる場合があります。

>[!NOTE]
>
>実稼動サイトを起動する準備が整ったら、実稼動ドメインが Fastly サービスを指すように DNS 設定を再度更新し、追加の設定タスクを完了する必要があります。 詳しくは、 [起動チェックリスト](../launch/checklist.md).

**前提条件：**

- Fastly モジュールを有効にします。
- デフォルトの Fastly VCL コードをアップロードします。
- Adobeに各環境のトップレベルドメインとサブドメインのリストを提供するか、Adobe Commerceサポートチケットを送信します。
- 指定したドメインがクラウド環境に追加されたことを確認します。
- スタータープロジェクトで、ドメインを Fastly サービス設定に追加します。 詳しくは、 [ドメインの管理](fastly-custom-cache-configuration.md#manage-domains).
- DNS 設定の更新について詳しくは、 [DNS レジストラ](https://lookup.icann.org/) を参照してください。

**開発用に DNS 設定を更新するには**:

1. CNAME レコードを追加して、実稼動前の URL を Fastly サービスにポイントします。 `prod.magentocloud.map.fastly.net`例：

   | ドメインまたはサブドメイン | CNAME |
   |---------------------------|----------------------------------|
   | mcprod.your-domain.com | prod.magentocloud.map.fastly.net |
   | mcstaging.your-domain.com | prod.magentocloud.map.fastly.net |

   CNAME レコードがライブになると、Adobeは証明書をプロビジョニングし、SSL/TLS 証明書をアップロードします。

   >[!NOTE]
   >
   >apex ドメイン (`your-domain.com`) を実稼動サイトで使用する場合、Fastly サーバーの IP アドレスを指すように DNS アドレスレコード（A レコード）を設定する必要があります。 詳しくは、 [実稼動設定で DNS 構成を更新する](../launch/checklist.md#to-update-dns-configuration-for-site-launch).


1. ACME チャレンジ CNAME レコードを追加して、実稼動 SSL/TLS 証明書のドメイン検証および事前プロビジョニングをおこないます。例：

   | ドメインまたはサブドメイン | CNAME |
   |-------------------------------------------|-------------------------------------------|
   | _acme-challenge.your-domain.com | 0123456789abcdef.validation.magento.cloud |
   | _acme-challenge.www.your-domain.com | 9573186429stuvwx.validation.magento.com |
   | _acme-challenge.mystore.your-domain.com | 1234567898zxywvu.validation.magento.cloud |
   | _acme-challenge.subdomain.your-domain.com | 1098765743lmnopq.validation.magento.cloud |

   >[!NOTE]
   >
   >この例の ACME チャレンジレコードは、Adobe Commerceのステージングサイトおよび実稼動サイトをプロビジョニングすることを意図していないプレースホルダーです。 Adobeに問い合わせて、プロジェクトの正しい ACME チャレンジレコード情報を取得します。

   CNAME レコードを追加した後、Adobeはドメインを検証し、環境用に SSL/TLS 証明書をプロビジョニングします。 DNS 設定を更新して、これらのドメインから Fastly サービスにトラフィックをルーティングすると、Adobeは証明書を環境にアップロードします。

1. Adobe Commerce Base URL を更新します。

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
   >Cloud CLI を使用する代わりに、 [管理者](https://experienceleague.adobe.com/docs/commerce-admin/stores-sales/site-store/store-urls.html)

1. Web ブラウザーを再起動します。

1. Web サイトをテストします。

## Fastly キャッシュのテスト

DNS 構成の変更が完了したら、 [cURL](https://curl.se/) コマンドラインツールを使用して、Fastly キャッシュが動作していることを確認します。

**応答ヘッダーを確認するには、以下を実行します。**:

1. ターミナルでは、次のコードを使用します。 `curl` コマンドを使用してライブサイト URL をテストします。

   ```bash
   curl -vo /dev/null -H Fastly-Debug:1 https://<live-URL>
   ```

   静的ルートを設定していない場合、または実稼働サイトのドメインの DNS 設定を完了していない場合は、 `--resolve` フラグは、DNS 名の解決をバイパスします。

   ```bash
   curl -vo /dev/null -H Fastly-Debug:1 --resolve <live-URL-hostname>:443:<live-IP-address>
   ```

1. 応答で、 [headers](fastly-troubleshooting.md#check-cache-hit-and-miss-response-headers) Fastly が機能していることを確認するために。 応答には、次の一意のヘッダーが表示されます。

   ```http
   < Fastly-Magento-VCL-Uploaded: yes
   < X-Cache: HIT, MISS
   ```

ヘッダーの値が正しくない場合は、 [応答ヘッダーで見つかったエラーを解決します](fastly-troubleshooting.md#curl) 」を参照してください。

## Fastly モジュールのアップグレード

Fastly CDN をMagento2 モジュール用に更新し、問題の解決、パフォーマンスの向上、新機能の提供をおこないました。
ステージング環境および実稼動環境の Fastly モジュールを、 [最新バージョン](https://github.com/fastly/fastly-magento2/blob/master/VERSION).

モジュールを更新した後、VCL コードをアップロードして、変更を Fastly サービス設定に適用する必要があります。

>[!WARNING]
>
> デフォルトの Fastly VCL コードをカスタムバージョンでカスタマイズした場合、Fastly モジュールをアップグレードすると変更内容が上書きされます。 一意の名前を持つカスタム VCL スニペットを追加した場合、これらの変更はアップグレードプロセス中に保持されます。 ベストプラクティスとして、ステージング環境をアップグレードし、変更を実稼動環境に適用する前に変更を検証します。

**Magento2 の Fastly CDN モジュールのバージョンを確認するには**:

1. クラウド環境のルートディレクトリに移動します。

1. Composer を使用して、インストールされているバージョンを確認します。

   ```bash
   composer show *fastly*
   ```

1. 次の場合、 [最新リリース](https://github.com/fastly/fastly-magento2/releases) がインストールされていない場合は、Fastly モジュールをアップグレードする手順を実行します。

**Fastly モジュールをアップグレードするには**:

1. ローカル統合環境で、次のモジュール情報を使用して、 [Fastly モジュールをアップグレードする](../store/extensions.md#upgrade-an-extension).

   ```text
   module name: fastly/magento2
   repository: https://github.com/fastly/fastly-magento2.git
   ```

1. 更新をステージング環境にプッシュします。

1. にステージング環境の管理者にログインします。 [VCL コードのアップロード](#upload-vcl-to-fastly).

1. [Fastly サービスの検証](fastly-troubleshooting.md#verify-or-debug-fastly-services) をAdobe Commerceステージングサイトに貼り付けます。

ステージングサイトで Fastly サービスを確認した後、実稼動環境でアップグレードプロセスを繰り返します。

>[!TIP]
>
> Adobe Commerce環境で Fastly サービスに問題がある場合は、 [Adobe Commerce Fastly のトラブルシューティングツール](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/magento-fastly-troubleshooter.html).
