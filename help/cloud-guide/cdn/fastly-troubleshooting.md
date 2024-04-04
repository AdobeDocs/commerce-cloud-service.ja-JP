---
title: Fastly トラブルシューティング
description: Adobe Commerceの Fastly CDN モジュールとサービスのトラブルシューティングと管理方法について説明します。
feature: Cloud, Configuration, Cache, Services
exl-id: e4c47035-cbad-4838-8d44-fa5eaaac42d1
source-git-commit: 1253d8357fd2554050d1775fefbc420a2097db5f
workflow-type: tm+mt
source-wordcount: '1834'
ht-degree: 0%

---

# Fastly トラブルシューティング

クラウドインフラストラクチャプロジェクト環境でのAdobe CommerceのMagento2 の Fastly CDN モジュールのトラブルシューティングと管理には、次の情報を使用します。 例えば、応答ヘッダーの値とキャッシュの動作を調べて、Fastly のサービスとパフォーマンスの問題を解決できます。

実稼動環境およびステージング環境では、 [New Relicログ](../monitor/log-management.md) を参照して、Fastly CDN と WAF ログデータを表示および分析し、エラーとパフォーマンスの問題をトラブルシューティングします。

>[!NOTE]
>
>Fastly の設定と設定について詳しくは、 [Fastly の設定](fastly.md).

## Fastly サービス ID の場所

Fastly を管理者から設定する場合、または Fastly API リクエストを送信して高度な Fastly 設定とトラブルシューティングをおこなう場合は、Fastly サービス ID が必要です。

プロジェクト環境で Fastly が有効になっている場合、管理者からサービス ID を取得できます。 詳しくは、 [Fastly 認証情報の取得](fastly-configuration.md#get-fastly-credentials).

開発者と上級の VCL ユーザーは、カスタム VCL を使用して、Fastly 変数を使用してサービス ID を取得できます `req.service_id`. 例えば、 `req.service_id` を VCL のカスタムログディレクティブに追加して、サービス ID 値を取り込みます。

```json
log {"syslog"} req.service_id {" my_logging_endpoint_name :: "}
```

実稼動環境とステージング環境で同じ VCL を使用できます。 詳しくは、 [vcl_log の設定方法](https://support.fastly.com/hc/en-us/community/posts/360040447172-How-to-configure-vcl-log).

## サイトのパフォーマンス、パージ、キャッシュの問題

次のリストを使用して、クラウドインフラストラクチャ環境上のAdobe Commerceの Fastly サービス設定に関する問題を特定し、トラブルシューティングします。

- **ストアメニューが表示されない、または機能しない** — ライブサイトの URL を使用する代わりに、元のサーバーに直接リンクまたは一時リンクを使用しているか、 `-H "host:URL"` 内 [cURL コマンド](#check-live-site-through-fastly). Fastly をオリジンサーバーにバイパスすると、メインメニューが機能せず、正しくないヘッダーが表示され、ブラウザー側でのキャッシュが可能になります。

- **トップナビゲーションが機能しません** — 上部のナビゲーションは、デフォルトのMagentoFastly VCL スニペットをアップロードすると有効になる Edge Side Includes(ESI) 処理に依存します。 ナビゲーションが機能していない場合は、 [Fastly VCL をアップロード](fastly-configuration.md#upload-vcl-to-fastly) サイトを再確認します。

- **位置情報/GeoIP が機能しません** — デフォルトのMagentoFastly VCL スニペットにより、URL に国コードが追加されます。 国コードが機能しない場合は、 [Fastly VCL をアップロード](fastly-configuration.md#upload-vcl-to-fastly) サイトを再確認します。

- **ページがキャッシュされない** — デフォルトでは、Fastly は `Set-Cookies` ヘッダー。 Adobe Commerceはキャッシュ可能なページ (TTL > 0) でも cookie を設定します。 デフォルトのMagentoFastly VCL は、キャッシュ可能なページ上でこれらの cookie を削除します。 ページがキャッシュされない場合、 [Fastly VCL をアップロード](fastly-configuration.md#upload-vcl-to-fastly) サイトを再確認します。

  この問題は、テンプレート内のページブロックがキャッシュ不可とマークされている場合にも発生する可能性があります。 この場合、この問題は、サードパーティのモジュールや、拡張機能によるAdobe Commerceヘッダーのブロックまたは削除が原因で発生している可能性が最も高くなります。 この問題を解決するには、 [X-Cache に MISS のみが含まれ、HIT は含まれない](#x-cache-contains-only-miss-no-hit).

- **パージリクエストが失敗しています** — パージリクエストを送信すると、次のエラーが返されます。

  ```text
  The purge request was not processed successfully.
  ```

  この問題は、次のいずれかの問題が原因で発生する可能性があります。

   - クラウドインフラストラクチャプロジェクト環境のAdobe Commerceの Fastly サービス設定に無効な Fastly 資格情報があります
   - カスタム VCL スニペット内の無効なコード

  この問題を解決するには、 [クラウド上の Fastly キャッシュをパージ中にエラーが発生しました](https://support.magento.com/hc/en-us/articles/115001853194-Error-purging-Fastly-cache-on-Cloud-The-purge-request-was-not-processed-successfully-) ( Adobe Commerceヘルプセンター )

## Fastly から 503 回のエラー

Fastly が 503 タイムアウトエラーを返した場合は、エラーログと 503 エラーページを確認して、根本原因を特定します。

>[!NOTE]
>
>一括操作の実行時にタイムアウトが発生した場合は、 [管理者の Fastly タイムアウトを拡張する](fastly-custom-cache-configuration.md#extend-fastly-timeout).

503 エラーが発生した場合は、実稼動またはステージング環境のエラーログと php アクセスログを確認して、問題をトラブルシューティングします。

**エラーログを確認するには**:

- [エラーログ](../test/log-locations.md#application-logs)

  ```text
  /var/log/platform/<project-ID>/error.log
  ```

  このログには、アプリケーションや PHP エンジンからのエラーが含まれます。例： `memory_limit` または `max_execution_time exceeded` エラー。 Fastly に関するエラーが見つからない場合は、PHP のアクセスログを確認してください。

- PHP アクセスログ

  ```text
  /var/log/platform/<project-ID>/php.access.log
  ```

  503 エラーを返した URL に関する HTTP 200 応答のログを検索します。 200 応答が見つかった場合、Adobe Commerceがエラーなしでページを返したことを意味します。 これは、 `first_byte_timeout` 値が Fastly サービス設定に設定されていること。

503 エラーが発生すると、Fastly はエラーとメンテナンスページに理由を返します。 のコードを追加した場合、理由が表示されない可能性があります [カスタム応答ページ](fastly-custom-response.md). デフォルトのエラーページに理由コードを表示するには、カスタムエラーページのHTMLコードを削除します。

**Fastly 503 エラーページを確認するには**:

{{admin-login-step}}

1. クリック **ストア** > **設定** > **設定** > **詳細** > **システム**.

1. 右側のウィンドウで、を展開します。 **フルページキャッシュ**.

1. Adobe Analytics の **Fastly 設定** セクション、展開 **カスタム合成ページ** を次の図に示します。

   ![カスタム 503 エラーページ](../../assets/cdn/fastly-custom-synthetic-pages-edit-html.png)

1. クリック **設定HTML**.

1. カスタムコードを削除します。 後で追加するために、テキストプログラムに保存することができます。

1. クリック **アップロード** 更新内容を Fastly に送信する場合。

1. クリック **設定を保存** をクリックします。

1. 503 エラーの原因となった URL を再度開きます。 次の例に示すように、エラーページと理由を返します。

   ![Fastly エラー](../../assets/cdn/fastly-503-example.png)

## Fastly アカウントに既に関連付けられている apex とサブドメイン

Adobe Commerce on cloud infrastructure プロジェクトの apex ドメインとサブドメインが、割り当てられたサービス ID を持つ既存の Fastly アカウントに既に関連付けられている場合、Fastly 設定を更新するまでを起動できません。

- 既存の Fastly アカウントの apex とサブドメインの設定を更新します。 詳しくは、 [複数の Fastly アカウントと割り当てられたドメイン](fastly.md#domain).

- [Fastly の有効化と設定](fastly-configuration.md#enable-fastly-caching) をクリックし、 [DNS 設定](../launch/checklist.md#update-dns-configuration-with-production-settings)

## Fastly サービスの検証またはデバッグ

クラウドインフラストラクチャサイト上のAdobe Commerceのパフォーマンスやキャッシュに関する問題をトラブルシューティングするには、サイトの URL をテストし、応答で返されるヘッダー値を調べます。

### Fastly 経由でライブサイトを確認する

Fastly API を使用して、  `Fastly-Magento-VCL-Uploaded` および `X-Cache` ライブサイトから返された応答ヘッダー。

Fastly API リクエストは、Fastly 拡張機能を通じて渡され、オリジンサーバーから応答を取得します。 応答で正しくないヘッダーが返された場合は、 [オリジンサーバー](#bypass-fastly-cache-to-check-adobe-commerce-sites).

**応答ヘッダーを確認するには、以下を実行します。**:

1. ターミナルでは、次のコードを使用します。 `curl` コマンドを使用してライブサイト URL をテストします。

   ```bash
   curl https://<live URL> -vo /dev/null -H Fastly-Debug:1
   ```

   静的ルートを設定していない場合、または実稼働サイトのドメインの DNS 設定を完了していない場合は、 `--resolve` フラグは、DNS 名の解決をバイパスします。

   ```bash
   curl -svo /dev/null --resolve '<your_hostname>:443:<IP-address-of-cache-node>' <https-URL>
   ```

   >[!NOTE]
   >
   >このコマンドを `--resolve` 」オプションを選択する場合は、SSL/TLS 証明書を使用して Fastly で TLS を有効にし、キャッシュノードの IP アドレスを探す必要があります。

1. 応答で、 [headers](#check-cache-hit-and-miss-response-headers) Fastly が機能していることを確認するために。 応答には、次の一意のヘッダーが表示されます。

   ```http
   < Fastly-Magento-VCL-Uploaded: yes
   < X-Cache: HIT, MISS
   ```

ヘッダーの値が正しくない場合は、次の情報を参照してください。

- [VCL アップロードを確認](#fastly-vcl-has-not-been-uploaded)

- [X-Cache に MISS のみが含まれ、HIT は含まれない](#x-cache-contains-only-miss-no-hit)

### Fastly キャッシュをバイパスしてAdobe Commerceサイトを確認する

Fastly サービスが誤ったヘッダーを返した場合は、Fastly キャッシュをバイパスするリクエストを送信できる VCL スニペットを作成できます。 詳しくは、 [Fastly キャッシュをバイパス](fastly-vcl-bypass-to-origin.md).

VCL スニペットを追加した後、cURL コマンドを使用して、指定した IP アドレスからオリジンサーバに要求を送信します。 次に、応答でエラーを確認します。

### キャッシュの HIT および MISS 応答ヘッダーを確認する

返された応答に次の情報が含まれていることを確認します。

- 次を含む： `X-Magento-Tags` ヘッダー

- の値 `Fastly-Module-Enabled` ヘッダーは次のいずれかです。 `Yes` または、プロジェクト環境にインストールされている CDNMagento2 用 Fastly モジュールのバージョン番号。

- [Cache-Control: max-age](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) が 0 より大きい

- [Pragma](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.32) 設定： `cache`

cURL コマンド出力の次の抜粋は、 `Pragma`, `X-Magento-Tags`、および `Fastly-Module-Enabled` headers:

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
>ヒット数とミス数について詳しくは、 [シールドされたサービスを使用したキャッシュの HIT ヘッダーと MISS ヘッダーについて](https://docs.fastly.com/guides/performance-tuning/understanding-cache-hit-and-miss-headers-with-shielded-services) Fastly のドキュメントで。

### 応答ヘッダーで見つかったエラーの解決

この節では、Fastly API を使用して応答ヘッダーをチェックする際に返されるエラーの解決方法を示します。

#### Fastly モジュールが有効になっていません

Fastly モジュールが有効になっていない場合 (`Fastly-Module-Enabled: no`) またはヘッダーがない場合、 [SSH を使用してログイン](../development/secure-connections.md#connect-to-a-remote-environment) をプロジェクトに追加します。 次に、次のコマンドを実行して、モジュールのステータスを確認します。

```bash
php bin/magento module:status Fastly_Cdn
```

返されたステータスに応じて、次の手順に従って Fastly 設定を更新します。

- `Module does not exist` — モジュールが存在しない場合 [インストールと設定](https://github.com/fastly/fastly-magento2/blob/master/Documentation/INSTALLATION.md) 統合ブランチのMagento2 の Fastly CDN Module。 インストールが完了したら、モジュールを有効にして設定します。 詳しくは、 [Fastly の設定](fastly-configuration.md).

- `Module is disabled`—Fastly モジュールが無効な場合は、 `integration` ローカル環境でブランチして有効にします。 次に、変更を「ステージング」と「実稼動」にプッシュします。 詳しくは、 [拡張機能の管理](../store/extensions.md#install-an-extension).

  次を使用する場合、 [設定管理](../store/store-settings.md#configure-store)」で、 Fastly CDN モジュールのステータスを確認します。 `app/etc/config.php` 設定ファイルを使用して、変更を実稼動またはステージング環境にプッシュする必要があります。

  モジュールが有効になっていない場合 (`Fastly_CDN => 0`) を `config.php` ファイルを削除し、次のコマンドを実行して更新します。 `config.php` を最新の設定に置き換えます。

  ```bash
  bin/magento magento-cloud:scd-dump
  ```

#### Fastly VCL がアップロードされていません

Fastly VCL がアップロードされていない場合 (`Fastly-Magento-VCL-Uploaded`: `false`) を使用する場合は、 *VCL をアップロード* 」オプションを使用して、アップロードできます。 詳しくは、 [Fastly VCL スニペットをアップロード](fastly-configuration.md#upload-vcl-to-fastly).

#### X-Cache に MISS のみが含まれ、HIT は含まれない

次の場合、 `X-Cache` ヘッダーに含む `HIT` (`HIT, HIT` または `HIT, MISS`) の場合、Fastly がキャッシュされたコンテンツを正常に返すことを示しています。

次の場合、 `X-Cache` ヘッダーは次のとおりです。 `MISS, MISS` およびに含まれない `HIT`を実行し、 `curl` 」コマンドを再度使用して、ページがキャッシュから最近パージされなかったことを確認します。

同じ結果が得られた場合は、 [`curl` コマンド](#check-live-site-through-fastly) そして [応答ヘッダー](#check-cache-hit-and-miss-response-headers):

- `Pragma` 次に該当 `cache`
- `X-Magento-Tags` 存在する
- `Cache-Control: max-age` が 0 より大きい

問題が解決しない場合は、別の拡張機能がこれらのヘッダーをリセットしている可能性があります。 ステージング環境で次の手順を繰り返します。すべての拡張機能を無効にし、各拡張機能を再度有効にして、ヘッダーをリセットする拡張機能を決定します。 問題の原因となっている拡張機能を特定したら、実稼動環境で無効にする必要があります。

**応答ヘッダーをリセットする拡張機能を識別するには：**

{{admin-login-step}}

1. に移動します。 **ストア** > **設定** > **設定** > **詳細** > **詳細**.

1. Adobe Analytics の *モジュール出力の無効化* 「 」セクションの右側のウィンドウで、すべての拡張機能を見つけて無効にします。

1. クリック **設定を保存**.

1. クリック **システム** > **ツール** > **キャッシュ管理**.

1. クリック **フラッシュMagentoキャッシュ**.

1. Fastly ヘッダーで問題が発生する可能性がある各拡張機能について、以下の手順を実行します。

   - 一度に 1 つの拡張機能を有効にし、設定を保存して、Adobe Commerceキャッシュをフラッシュします。

   - を実行します。 [`curl` コマンド](#check-live-site-through-fastly) 検証する [応答ヘッダー](#check-cache-hit-and-miss-response-headers).

   拡張機能ごとに、この手順を繰り返します。 Fastly の応答ヘッダーが表示されなくなった場合は、Fastly に問題を引き起こしている拡張機能を識別しました。

Fastly ヘッダーをリセットしている拡張機能を特定したら、拡張機能の開発者に問い合わせてください。 サードパーティの拡張機能を Fastly キャッシュで動作させるための修正や更新を提供することはできません。

## Rollback Fastly 設定

カスタム VCL スニペットの更新やその他の Fastly 設定の変更により、クラウドインフラストラクチャサイト上のAdobe Commerceがエラーを壊したり返したりする場合は、Fastly API を使用してください [アクティブ化](https://docs.fastly.com/api/config#version_0b79ae1ba6aee61d64cc4d43fed1e0d5) コマンドを使用して、以前の VCL バージョンにロールバックします。 管理者から VCL バージョンをロールバックすることはできません。

**VCL バージョンをロールバックするには**:

1. サービスで使用可能な VCL バージョンのリストを取得するには、次のコマンドを実行します

   ```bash
   curl -H "Fastly-Key: <FASTLY_API_TOKEN>" -H "Accept: application/json" https://api.fastly.com/service/<FASTLY_SERVICE_ID>/version
   ```

1. 次のコマンドを実行して、アクティブな VCL バージョンを指定したバージョンに変更します。

   ```bash
   curl -H "Fastly-Key: <FASTLY_API_TOKEN>" -H "Content-Type: application/x-www-form-urlencoded" -H "Accept: application/json" -X PUT https://api.fastly.com/service/<FASTLY_SERVICE_ID>/version/<VERSION_ID>/activate
   ```

Fastly API を使用した VCL の確認と管理について詳しくは、 [API を使用して VCL を管理](fastly-vcl-custom-snippets.md#manage-custom-vcl-snippets-using-the-api).
