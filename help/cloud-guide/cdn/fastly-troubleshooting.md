---
title: Fastly のトラブルシューティング
description: Adobe Commerce用の Fastly CDN モジュールおよびサービスのトラブルシューティングと管理の方法について説明します。
feature: Cloud, Configuration, Cache, Services
exl-id: e4c47035-cbad-4838-8d44-fa5eaaac42d1
source-git-commit: 1253d8357fd2554050d1775fefbc420a2097db5f
workflow-type: tm+mt
source-wordcount: '1834'
ht-degree: 0%

---

# Fastly のトラブルシューティング

以下の情報を使用して、クラウドインフラストラクチャプロジェクト環境のAdobe CommerceのMagento 2 用 Fastly CDN モジュールのトラブルシューティングと管理を行います。 例えば、応答ヘッダー値とキャッシュ動作を調査して、Fastly のサービスとパフォーマンスの問題を解決できます。

実稼動環境およびステージング環境では、を使用できます [New Relic ログ](../monitor/log-management.md) fastly CDN および WAF ログデータを表示および分析して、エラーとパフォーマンスの問題のトラブルシューティングを行います。

>[!NOTE]
>
>Fastly の設定について詳しくは、以下を参照してください。 [Fastly の設定](fastly.md).

## Fastly サービス ID を見つけます

管理者から Fastly を設定したり、高度な Fastly 設定とトラブルシューティングのために Fastly API リクエストを送信したりするには、Fastly サービス ID が必要です。

プロジェクト環境で Fastly が有効になっている場合は、管理者からサービス ID を取得できます。 参照： [Fastly 資格情報の取得](fastly-configuration.md#get-fastly-credentials).

開発者と高度な VCL ユーザーは、カスタム VCL を使用して、Fastly 変数を使用してサービス ID を取得できます `req.service_id`. 例えば、 `req.service_id` VCL のカスタム ログ ディレクティブに対して、サービス ID 値をキャプチャするには、次の手順を実行します。

```json
log {"syslog"} req.service_id {" my_logging_endpoint_name :: "}
```

実稼動環境とステージング環境に同じ VCL を使用できます。 参照： [vcl_log の設定方法](https://support.fastly.com/hc/en-us/community/posts/360040447172-How-to-configure-vcl-log).

## サイトのパフォーマンス、パージ、キャッシュの問題

以下のリストを使用して、クラウドインフラストラクチャー上のAdobe Commerceの Fastly サービス設定に関する問題を特定し、トラブルシューティングを行います。

- **ストアメニューが表示されない、または機能しない** – ライブサイト URL を使用する代わりに、オリジンサーバーへのリンクまたは一時リンクを直接使用している場合や、を使用した場合があります `-H "host:URL"` in a [cURL コマンド](#check-live-site-through-fastly). Fastly をオリジンサーバーにバイパスすると、メインメニューが機能せず、ブラウザー側でのキャッシュを許可する誤ったヘッダーが表示されます。

- **上部ナビゲーションが機能しない**- トップナビゲーションは、エッジサイドインクルード （ESI）処理に依存しています。これは、デフォルトMagentoの Fastly VCL スニペットをアップロードする際に有効になります。 ナビゲーションが機能しない場合は、 [fastly VCL のアップロード](fastly-configuration.md#upload-vcl-to-fastly) そしてサイトを再確認してください。

- **位置情報/位置情報 IP が機能しません**— デフォルトMagentoの Fastly VCL スニペットは、国コードを URL に付加します。 国コードが機能していない場合、 [fastly VCL のアップロード](fastly-configuration.md#upload-vcl-to-fastly) そしてサイトを再確認してください。

- **ページがキャッシュされない**— デフォルトでは、Fastly は以下を使用してページをキャッシュしません。 `Set-Cookies` ヘッダー。 Adobe Commerceは、キャッシュ可能なページ（TTL > 0）でも cookie を設定します。 デフォルトのMagentoである Fastly VCL では、キャッシュ可能なページにこれらの Cookie が削除されます。 ページがキャッシュされない場合、 [fastly VCL のアップロード](fastly-configuration.md#upload-vcl-to-fastly) そしてサイトを再確認してください。

  この問題は、テンプレートのページブロックがキャッシュ不可とマークされている場合にも発生する可能性があります。 その場合、問題はサードパーティのモジュールまたは拡張機能がAdobe Commerce ヘッダーをブロックまたは削除したことが原因で発生する可能性が高くなります。 この問題を解決するには、を参照してください。 [X-Cache には MISS のみが含まれ、ヒットは含まれない](#x-cache-contains-only-miss-no-hit).

- **消去リクエストが失敗する**- パージリクエストを送信すると、Fastly は次のエラーを返します。

  ```text
  The purge request was not processed successfully.
  ```

  この問題は、次のいずれかの問題が原因で発生する可能性があります。

   - クラウドインフラストラクチャプロジェクト環境のAdobe Commerce用 Fastly サービス設定の Fastly 資格情報が無効です
   - カスタム VCL スニペットのコードが無効です

  この問題を解決するには、を参照してください。 [クラウド上の Fastly キャッシュのパージエラー](https://support.magento.com/hc/en-us/articles/115001853194-Error-purging-Fastly-cache-on-Cloud-The-purge-request-was-not-processed-successfully-) Adobe Commerce ヘルプセンター

## Fastly の 503 エラー

Fastly が 503 タイムアウトエラーを返す場合は、エラーログと 503 エラーページを確認して、根本原因を特定します。

>[!NOTE]
>
>一括操作の実行時にタイムアウトが発生した場合は、次のことができます [管理者の Fastly タイムアウトの拡張](fastly-custom-cache-configuration.md#extend-fastly-timeout).

503 エラーが発生した場合は、実稼動環境またはステージング環境のエラーログと PHP アクセスログを確認して、問題のトラブルシューティングを行ってください。

**エラーログを確認するには**:

- [エラーログ](../test/log-locations.md#application-logs)

  ```text
  /var/log/platform/<project-ID>/error.log
  ```

  このログには、アプリケーションまたは PHP エンジンからのエラーが含まれます。例： `memory_limit` または `max_execution_time exceeded` エラー。 Fastly 関連のエラーが見つからない場合は、PHP アクセスログを確認します。

- PHP アクセスログ

  ```text
  /var/log/platform/<project-ID>/php.access.log
  ```

  503 エラーを返した URL のログで HTTP 200 応答を検索します。 200 の応答が見つかった場合は、Adobe Commerceがエラーなくページを返したことを意味します。 は、を超える間隔の後に問題が発生した可能性があることを示します `first_byte_timeout` fastly サービス設定で設定された値。

503 エラーが発生した場合、Fastly はエラーとメンテナンスページで理由を返します。 のコードを追加した場合、その理由を確認できないことがあります。 [カスタム応答ページ](fastly-custom-response.md). デフォルトのエラーページで理由コードを確認するには、カスタムエラーページのHTMLコードを削除します。

**Fastly 503 エラーページを確認するには**:

{{admin-login-step}}

1. クリック **ストア** > **設定** > **設定** > **詳細** > **システム**.

1. 右側のパネルで、を展開します **フルページキャッシュ**.

1. が含まれる **Fastly 設定** セクション、展開 **カスタム合成ページ** 次の図に示すように。

   ![カスタム 503 エラーページ](../../assets/cdn/fastly-custom-synthetic-pages-edit-html.png)

1. クリック **HTMLを設定**.

1. カスタムコードを削除します。 後で追加し直すために、テキストプログラムで保存することができます。

1. クリック **Upload** をクリックして、Fastly に更新を送信します。

1. クリック **設定を保存** ページの上部

1. 503 エラーの原因となった URL を再度開きます。 Fastly は、次の例に示す理由を含むエラーページを返します。

   ![Fastly エラー](../../assets/cdn/fastly-503-example.png)

## Apex とサブドメインは既に Fastly アカウントに関連付けられています

クラウドインフラストラクチャプロジェクトのAdobe Commerceの apex ドメインとサブドメインが、割り当てられたサービス ID を持つ既存の Fastly アカウントに既に関連付けられている場合、Fastly 設定を更新するまで起動できません。

- 既存の Fastly アカウントの apex とサブドメインの設定を更新します。 参照： [複数の Fastly アカウントと割り当てられたドメイン](fastly.md#domain).

- [Fastly の有効化と設定](fastly-configuration.md#enable-fastly-caching) を実行して、 [DNS 設定](../launch/checklist.md#update-dns-configuration-with-production-settings)

## Fastly サービスの検証またはデバッグ

サイト URL をテストし、応答で返されるヘッダー値を調べることで、クラウドインフラストラクチャサイト上のAdobe Commerceのパフォーマンスやキャッシュの問題をトラブルシューティングできます。

### Fastly でのライブサイトの確認

Fastly API を使用して、  `Fastly-Magento-VCL-Uploaded` および `X-Cache` ライブサイトから返された応答ヘッダー。

Fastly API リクエストは、Fastly 拡張機能を通じて渡され、オリジンサーバーから応答を取得します。 応答から誤ったヘッダーが返された場合は、 [オリジンサーバーを直接公開](#bypass-fastly-cache-to-check-adobe-commerce-sites).

**応答ヘッダーを確認するには**:

1. ターミナルでは、次を使用します。 `curl` ライブサイト URL をテストするコマンド：

   ```bash
   curl https://<live URL> -vo /dev/null -H Fastly-Debug:1
   ```

   静的ルートを設定していない場合や、ライブサイト上のドメインの DNS 設定が完了している場合は、を使用します `--resolve` DNS の名前解決をバイパスするフラグ。

   ```bash
   curl -svo /dev/null --resolve '<your_hostname>:443:<IP-address-of-cache-node>' <https-URL>
   ```

   >[!NOTE]
   >
   >このコマンドを `--resolve` オプションとして、SSL/TLS 証明書を介して Fastly で TLS を有効にし、キャッシュノードの IP アドレスを見つける必要があります。

1. 応答で、を検証します [ヘッダー](#check-cache-hit-and-miss-response-headers) を使用して、Fastly が機能していることを確認します。 応答に次の一意のヘッダーが表示されます。

   ```http
   < Fastly-Magento-VCL-Uploaded: yes
   < X-Cache: HIT, MISS
   ```

ヘッダーに正しい値がない場合は、次の情報を参照してください。

- [VCL アップロードのチェック](#fastly-vcl-has-not-been-uploaded)

- [X-Cache には MISS のみが含まれ、ヒットは含まれない](#x-cache-contains-only-miss-no-hit)

### Fastly キャッシュをバイパスしてAdobe Commerce サイトをチェック

Fastly サービスが誤ったヘッダーを返す場合、Fastly キャッシュをバイパスするリクエストを送信できる VCL スニペットを作成できます。 参照： [Fastly キャッシュのバイパス](fastly-vcl-bypass-to-origin.md).

VCL スニペットを追加した後、cURL コマンドを使用して、指定した IP アドレスから発信元サーバーに要求を送信します。 次に、応答にエラーがないか確認します。

### キャッシュヒットおよびミス応答ヘッダーを確認します

返された応答に次の情報が含まれていることを確認します。

- 次を含む `X-Magento-Tags` ヘッダー

- の値 `Fastly-Module-Enabled` ヘッダーは `Yes` または、プロジェクト環境にインストールされている Fastly for CDN Magento 2 モジュールのバージョン番号

- [キャッシュコントロール : max-age](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) が 0 より大きい

- [プラグマ](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.32) 設定はです `cache`

次の cURL コマンド出力からの抜粋は、の正しい値を示しています `Pragma`, `X-Magento-Tags`、および `Fastly-Module-Enabled` ヘッダー：

```terminal
* STATE: INIT => CONNECT handle 0x600057800; line 1402 (connection #-5000)
* Rebuilt URL to: https://www.mymagento.biz.c.sv7gVom4qrpek.ent.magento.cloud/
* Added connection 0. The cache now contains 1 members
* Trying 192.0.2.31...
* STATE: CONNECT => WAITCONNECT handle 0x600057800; line 1455 (connection #0)

% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0* Connected to www.mymagento.biz.c.sv7gVom4qrpek.ent.magento.cloud (54.229.163.31) port 443 (#0)

* STATE: WAITCONNECT => SENDPROTOCONNECT handle 0x600057800; line 1562 (connection #0)
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0* ALPN, offering h2

... portion omitted for brevity ...

< Set-Cookie: mage-messages=%5B%5D; expires=Wed, 22-Nov-2017 17:39:58 GMT; Max-Age=31536000; path=/
< Pragma: cache
< Expires: Wed, 23 Nov 2016 17:39:56 GMT
< Cache-Control: max-age=86400, public, s-maxage=86400, stale-if-error=5, stale-while-revalidate=5
< X-Magento-Tags: cb_welcome_popup store cb cb_store_info_mobile cb_header_promotional_bar cb_store_info cb_discount-promo-bar cpg_2 cb_83 cb_81 cb_84 cb_85 cb_86 cb_87 cb_88 cb_89 p5646 catalog_product p5915 p6040 p6197 p6227 p7095 p6109 p6122 p6331 p7592 p7651 p7690
< Fastly-Module-Enabled: yes
< Strict-Transport-Security: max-age=31536000
    < Content-Security-Policy: upgrade-insecure-requests
    < X-Content-Type-Options: nosniff
    < X-XSS-Protection: 1; mode=block
    < X-Frame-Options: SAMEORIGIN
    < X-Platform-Server: i-dff64b52
    <
    * STATE: PERFORM => DONE handle 0x600057800; line 1955 (connection #0)
    * multi_done
      0     0    0     0    0     0      0      0 --:--:--  0:00:02 --:--:--     0
    * Connection #0 to host www.mymagento.biz.c.sv7gVom4qrpek.ent.magento.cloud left intact
```

>[!NOTE]
>
>ヒットとミスの詳細については、を参照してください。 [シールド サービスのキャッシュ HIT および MISS ヘッダーについて](https://docs.fastly.com/guides/performance-tuning/understanding-cache-hit-and-miss-headers-with-shielded-services) を Fastly のドキュメントで確認できます。

### 応答ヘッダーで見つかったエラーの解決

この節では、Fastly API を使用して応答ヘッダーを確認する際に返されるエラーを解決するための推奨事項を説明します。

#### Fastly モジュールが有効になっていません

Fastly モジュールが有効になっていない場合（`Fastly-Module-Enabled: no`）に設定するか、ヘッダーがない場合は、 [ssh を使用したログイン](../development/secure-connections.md#connect-to-a-remote-environment) をプロジェクトに追加します。 次に、次のコマンドを実行して、モジュールのステータスを確認します。

```bash
php bin/magento module:status Fastly_Cdn
```

返されたステータスに基づいて、次の手順を使用して Fastly 設定を更新します。

- `Module does not exist`— モジュールが存在しない場合 [インストールと設定](https://github.com/fastly/fastly-magento2/blob/master/Documentation/INSTALLATION.md) 統合ブランチのMagento 2 用 Fastly CDN モジュール。 インストールが完了したら、モジュールを有効にして設定します。 参照： [Fastly の設定](fastly-configuration.md).

- `Module is disabled`—Fastly モジュールが無効になっている場合は、で環境設定を更新します `integration` ローカル環境で分岐して有効にします。 次に、変更をステージング環境および実稼動環境にプッシュします。 参照： [拡張機能の管理](../store/extensions.md#install-an-extension).

  を使用する場合 [設定の管理](../store/store-settings.md#configure-store)で Fastly CDN モジュールのステータスを確認します `app/etc/config.php` 実稼動環境またはステージング環境に変更をプッシュする前に、設定ファイルをアップロードします。

  モジュールが有効になっていない場合（`Fastly_CDN => 0`） `config.php` ファイルを開き、ファイルを削除して、次のコマンドを実行して更新します `config.php` と最新の設定を使用します。

  ```bash
  bin/magento magento-cloud:scd-dump
  ```

#### Fastly VCL はアップロードされていません

Fastly VCL がアップロードされていない場合（`Fastly-Magento-VCL-Uploaded`: `false`）、を使用する *VCL のアップロード* 管理者が「」オプションを選択してアップロードします。 参照： [Fastly VCL スニペットのアップロード](fastly-configuration.md#upload-vcl-to-fastly).

#### X-Cache には MISS のみが含まれ、ヒットは含まれない

次の場合 `X-Cache` ヘッダーにを含める `HIT` （`HIT, HIT` または `HIT, MISS`）、Fastly がキャッシュされたコンテンツを正常に返すことを示します。

次の場合 `X-Cache` ヘッダー： `MISS, MISS` およびが次を含まない `HIT`、を実行します `curl` 再度コマンドを実行して、ページが最近キャッシュからパージされていないことを確認します。

同じ結果が得られる場合は、を使用します [`curl` コマンド](#check-live-site-through-fastly) を確認します。 [応答ヘッダー](#check-cache-hit-and-miss-response-headers):

- `Pragma` 等しい `cache`
- `X-Magento-Tags` が存在する
- `Cache-Control: max-age` が 0 より大きい

問題が解決しない場合は、別の拡張機能がこれらのヘッダーをリセットしている可能性があります。 ステージング環境で次の手順を繰り返し、すべての拡張機能を無効にし、各拡張機能を再度有効にして、ヘッダーをリセットしている拡張機能を特定します。 問題の原因となっている拡張機能を特定したら、実稼動環境でその拡張機能を無効にする必要があります。

**応答ヘッダーをリセットする拡張機能を識別するには：**

{{admin-login-step}}

1. に移動します。 **ストア** > **設定** > **設定** > **詳細** > **詳細**.

1. が含まれる *モジュール出力の無効化* セクションの右側のペインで、すべての拡張機能を見つけて無効にします。

1. クリック **設定を保存**.

1. クリック **システム** > **ツール** > **キャッシュ管理**.

1. クリック **Magentoキャッシュのフラッシュ**.

1. Fastly ヘッダーで問題を引き起こす可能性がある拡張機能ごとに、次の手順を実行します。

   - 一度に 1 つの拡張機能を有効にし、設定を保存して、Adobe Commerce キャッシュをフラッシュします。

   - を実行 [`curl` コマンド](#check-live-site-through-fastly) を検証します [応答ヘッダー](#check-cache-hit-and-miss-response-headers).

   各拡張機能に対して、このプロセスを繰り返します。 Fastly 応答ヘッダーが表示されなくなった場合は、Fastly で問題を引き起こしている拡張機能を特定しました。

Fastly ヘッダーをリセットしている拡張機能を特定したら、拡張機能の開発者に問い合わせてください。 サードパーティの拡張機能を Fastly キャッシュで機能させるための修正や更新を提供することはできません。

## Fastly 設定をロールバックする

カスタム VCL スニペットの更新やその他の Fastly 設定の変更により、クラウドインフラストラクチャサイト上のAdobe Commerceでエラーが発生した場合は、Fastly API を使用します [アクティベート](https://docs.fastly.com/api/config#version_0b79ae1ba6aee61d64cc4d43fed1e0d5) 以前の VCL バージョンにロールバックするコマンド。 管理者から VCL バージョンをロールバックすることはできません。

**VCL のバージョンをロールバックするには**:

1. サービスで使用可能な VCL バージョンのリストを取得するには、次のコマンドを実行します

   ```bash
   curl -H "Fastly-Key: <FASTLY_API_TOKEN>" -H "Accept: application/json" https://api.fastly.com/service/<FASTLY_SERVICE_ID>/version
   ```

1. 次のコマンドを実行して、アクティブな VCL バージョンを指定のバージョンに変更します。

   ```bash
   curl -H "Fastly-Key: <FASTLY_API_TOKEN>" -H "Content-Type: application/x-www-form-urlencoded" -H "Accept: application/json" -X PUT https://api.fastly.com/service/<FASTLY_SERVICE_ID>/version/<VERSION_ID>/activate
   ```

Fastly API を使用した VCL のレビューと管理について詳しくは、を参照してください。 [API を使用した VCL の管理](fastly-vcl-custom-snippets.md#manage-custom-vcl-snippets-using-the-api).
