---
title: エラーページとメンテナンスページのカスタマイズ
description: Fastly オリジンサーバーへの要求が失敗した場合に表示されるデフォルトのエラーページをカスタマイズする方法を説明します。
feature: Cloud, Configuration, Security
exl-id: 16722821-b928-4872-8cef-7f049e600f0d
source-git-commit: 1253d8357fd2554050d1775fefbc420a2097db5f
workflow-type: tm+mt
source-wordcount: '797'
ht-degree: 0%

---

# エラーページとメンテナンスページのカスタマイズ

Fastly オリジンへのリクエストが失敗した場合、Fastly は、ユーザーにとって混乱を招く可能性のある、基本的な形式と一般的なメッセージを含むデフォルトの応答ページを返します。 例えば、Fastly オリジンへのリクエストが 503 エラーのために失敗した場合、Fastly は次のデフォルトのエラーページを返します。

![デフォルトのエラーページ](../../assets/cdn/fastly-503-example.png)

Adobe Commerceストア設定を更新して、一部のデフォルトの応答ページを、よりわかりやすいメッセージを含むページに置き換え、次の例に示すようにHTMLのスタイルを改善できます。

![Fastly カスタムエラーページ](../../assets/cdn/fastly-new-error-page.png)

現在、クラウドインフラストラクチャプロジェクト上のAdobe Commerceに関する、次の Fastly 応答ページをカスタマイズできます。

- [サーバーエラー — 内部サーバーエラー、タイムアウト、またはサイトメンテナンスの停止（エラーコード 500 以降）](#customize-the-503-error-page)
- [WAF が疑わしいリクエストトラフィックを検出したときに発生する WAF ブロックイベント (403 Forbidden)](#customize-the-waf-error-page)

**HTMLのコーディング要件：**

カスタムページのHTMLコードは、次の要件を満たしている必要があります。

- コンテンツには、65,535 文字まで含めることができます。
- HTMLソース内のすべての CSS をインラインで指定する。
- Fastly がオフラインの場合でもHTMLが表示されるように、base64 を使用して画像ページにバンドルします。 詳しくは、 [css-tricks サイトのデータ URI](https://css-tricks.com/data-uris/).

## 503 エラーページのカスタマイズ

次の場合は、デフォルトの 503 エラーページが表示されます。

- Fastly オリジンへのリクエストが 500 を超える応答ステータスを返す場合
- タイムアウト、メンテナンスアクティビティ、ヘルスの問題など、Fastly 接触チャネルが停止した場合。

デフォルトのページをカスタマイズするには、次のHTMLコードを適応させて、Adobe Commerceストアテーマに合ったスタイル設定を含め、必要に応じてタイトルとメッセージを変更します。

```html
<!DOCTYPE html>
<html>
   <head>
      <meta charset="UTF-8">
         <title>503</title>
   </head>
   <body>
      <p>Service unavailable</p>
   </body></html>
```

変更したソースがブラウザーに正しく表示されることを確認します。 次に、カスタマイズしたHTMLコードを Fastly 設定に追加します。

カスタム応答ページを Fastly 設定に追加するには：

{{admin-login-step}}

1. 選択 **ストア** > **設定** > **設定** > **詳細** > **システム**.

1. 右側のウィンドウで、を展開します。 **フルページキャッシュ** > **Fastly 設定** > **カスタム合成ページ**.

   ![503 エラーページを編集](../../assets/cdn/fastly-custom-synthetic-pages-edit-html.png)

1. 選択 **設定HTML**.

1. カスタム応答ページのソースコードをコピーして、「HTML」フィールドに貼り付けます。

   ![503 エラーページを更新](../../assets/cdn/fastly-customize-503-response.png)

1. 選択 **アップロード** をクリックして、カスタマイズしたHTMLソースを Fastly サーバーにアップロードします。

1. 選択 **設定を保存** をクリックして、更新した設定ファイルを保存します。

1. キャッシュを更新します。

   - ページ上部の通知で、 *キャッシュ管理* リンク。

   - キャッシュ管理ページで、「 」を選択します。 **フラッシュMagentoキャッシュ**.

## WAF エラーページのカスタマイズ

Fastly オリジンへのリクエストが失敗し、 `403 Forbidden` ～に起因するエラー [WAF](fastly-waf-service.md) ブロックイベント。

![WAF エラーページ](../../assets/cdn/fastly-waf-403-error.png)

次のコードサンプルは、デフォルトのHTMLのソースを示しています。

```html
<html>
  <head>
    <title>Magento 403 Forbidden</title>
  </head>
  <body>
    <p>The requested URL was rejected.</p>
    <p>For additional information, please contact support and provide this reference ID:</p>
    <p>"} req.http.x-request-id {"</p>
    <p><button onclick='history.back();'>Go Back</button></p>
  </body>
</html>
```

以下を使用すると、 **カスタム合成ページ** > **WAF ページを編集** 」オプションを使用して、Adobe Commerce on cloud infrastructure プロジェクトのデフォルトコードをカスタマイズできます。 コードを編集する際は、WAF ブロックイベントの参照 ID を示す次の行を保持します。

```html
<p>"} req.http.x-request-id {"</p>
```

>[!NOTE]
>
>「 WAF を編集」オプションは、Managed Cloud WAF サービスがクラウドインフラストラクチャプロジェクト上のAdobe Commerceに対して有効になっている場合にのみ使用できます。

**WAF エラーページを編集するには**:

1. [管理者にログインします。](../../get-started/onboarding.md#access-your-admin-panel).

1. 選択 **ストア** > **設定** > **設定** > **詳細** > **システム**.

1. 右側のウィンドウで、を展開します。 **フルページキャッシュ** > **Fastly 設定** > **カスタム合成ページ**.

   ![WAF エラーページオプションを編集](../../assets/cdn/fastly-custom-synthetic-pages-edit-waf.png)

1. 選択 **WAF ページを編集**.

1. フィールドに入力し、HTMLを更新します。

   ![WAF エラーページを更新](../../assets/cdn/fastly-edit-waf-html.png)

   - **ステータス**  — を選択します。 `403 Forbidden` ステータス。
   - **MIME タイプ**  — タイプ `text/html`.
   - **コンテンツ**  — デフォルトのHTML応答を編集し、カスタム CSS を追加し、必要に応じてタイトルとメッセージを更新します。

1. 選択 **アップロード** をクリックして、カスタマイズしたHTMLソースを Fastly サーバーにアップロードします。

1. 選択 **設定を保存** をクリックして、更新した設定ファイルを保存します。

1. キャッシュを更新します。

   - ページ上部の通知で、 **キャッシュ管理** リンク。

   - キャッシュ管理ページで、「 」を選択します。 **フラッシュMagentoキャッシュ**.

## エラーレポート番号を表示

デフォルトでは、Fastly は、 *503 サービスを利用できません* エラー。 エラーログのレポート番号を表示して、ログ内のエラーの詳細を見つけて確認するには、次の手順を使用せずに Web サイトを Fastly で開きます。

1. ストアの IP アドレスを取得します。

   - Pro ステージング環境および実稼動環境の場合：

     ```bash
     nslookup {your_project_id}.ent.magento.cloud
     ```

   - Pro 統合環境およびスターター環境の場合：

     ```bash
     nslookup gw.{your_region}.magentosite.cloud
     ```

1. ローカルワークステーション上の hosts ファイルに、アプリケーションドメインと IP アドレスを追加します。

   ```text
   {server_IP} {store_domain}
   ```

1. ブラウザーのキャッシュと cookie をクリアします（または匿名モードに切り替えます）。

1. ストアの Web サイトを再度開いて、エラーコードを表示します。

1. エラーコードを使用して、エラーレポートファイルの詳細を見つけます。

   - [SSH を使用して、影響を受ける環境に接続](../development/secure-connections.md#connect-to-a-remote-environment)

   - 次を見つけます。 `./var/report/{error_number}` ファイル。
