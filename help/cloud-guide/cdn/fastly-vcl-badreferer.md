---
title: リファラルスパムをブロック
description: Fastly Edge 辞書とカスタム VCL スニペットを使用して、サイトからリファラルスパムをブロックします。
feature: Cloud, Configuration, Security
exl-id: 665bac93-75db-424f-be2c-531830d0e59a
source-git-commit: 7a181af2149eef7bfaed4dd4d256b8fa19ae1dda
workflow-type: tm+mt
source-wordcount: '684'
ht-degree: 0%

---

# リファラルスパムをブロック

次の例は、の設定方法を示しています [Fastly エッジディクショナリ](https://docs.fastly.com/guides/edge-dictionaries/working-with-dictionaries-using-the-api) カスタム VCL スニペットを使用して、クラウドインフラストラクチャサイト上のAdobe Commerceからの紹介スパムをブロックします。

>[!NOTE]
>
>カスタム VCL 設定をステージング環境に追加して、実稼動環境に対して実行する前にテストできるようにすることをお勧めします。

**前提条件：**

{{$include /help/_includes/vcl-snippet-prerequisites.md}}

- サイトのログで偽のリファラル URL を確認し、ブロックするドメインのリストを作成します。

## リファラーブロックリストの作成

Edge Dictionaries は、VCL スニペットの処理中に、VCL 関数からアクセス可能なキーと値のペアを作成します。 この例では、ブロックするリファラー web サイトのリストを提供するエッジ辞書を作成します。

{{admin-login-step}}

1. クリック **ストア** > **設定** > **設定** > **詳細** > **システム**.

1. を展開 **フルページキャッシュ** > **Fastly 設定** > **エッジ辞書**.

1. 辞書コンテナの作成：

   - クリック **コンテナを追加**.

   - 日 *コンテナ* ページ、a を入力 **辞書名**—`referrer_blocklist`.

   - を選択 **変更後にアクティベート** 編集中の Fastly サービス設定のバージョンに変更をデプロイする場合。

   - クリック **Upload** をクリックして、Fastly サービス設定に辞書を添付します。

1. ブロックするドメイン名のリストをに追加 `referrer_blocklist` 辞書：

   - の「設定」アイコンをクリックします `referrer_blocklist` 辞書。

   - キーと値のペアを新しい辞書に追加して保存します。 この例では、 **キー** は、ブロックするリファラー URL のドメイン名です。 **値** 等しい `true`.

     ![不正なリファラー辞書項目を追加](../../assets/cdn/fastly-referrer-blocklist-dictionary.png)

   - クリック **キャンセル** 「システム設定」ページに戻ります。

1. クリック **設定を保存**.

1. ページ上部の通知に従ってキャッシュを更新します。

Edge 辞書について詳しくは、 [Edge 辞書の作成と使用](https://docs.fastly.com/guides/edge-dictionaries/working-with-dictionaries-using-the-api) および [カスタム VCL スニペット](https://docs.fastly.com/guides/edge-dictionaries/working-with-dictionaries-using-the-api#custom-vcl-examples) を Fastly のドキュメントで確認できます。

## リファラースパムをブロックするカスタム VCL スニペットの作成

次のカスタム VCL スニペットコード（JSON 形式）は、リクエストをチェックしてブロックするロジックを示しています。 VCL スニペットは、リファラー web サイトのホストをヘッダーに取り込み、ホスト名をの中の URL のリストと比較します。 `referrer_blocklist` 辞書。 ホスト名が一致する場合、リクエストは次のコードでブロックされます。 `403 Forbidden` エラー。

```json
{
  "name": "block_bad_referrer",
  "dynamic": "0",
  "type": "recv",
  "priority": "5",
  "content": "set req.http.Referer-Host = regsub(req.http.Referer, \"^https?:\/\/?([^:\/s]+).*$\", \"\\1\"); if (table.lookup(referrer_blocklist, req.http.Referer-Host)) { error 403 \"Forbidden\"; }"
}
```

この例に基づいてスニペットを作成する前に、値を確認して、変更が必要かどうかを判断してください。

- `name` — VCL スニペットの名前。 この例では、を使用します `block_bad_referrer`.

- `dynamic`  – 値 0 はを示します。 [標準スニペット](https://docs.fastly.com/en/guides/using-regular-vcl-snippets) を Fastly 設定用のバージョン付き VCL にアップロードします。

- `priority` - VCL スニペットを実行するタイミングを指定します。 優先度は次のとおりです `5` デフォルトのMagentoVCL スニペットの前にこのコードを実行するには、次の手順に従います（`magentomodule_*`）に優先度 50 を割り当てました。 各カスタムスニペットの優先度を、スニペットを実行するタイミングに応じて 50 より高くまたは低く設定します。 優先度の低いスニペットが最初に実行されます。

- `type` - VCL バージョンにスニペットを挿入する場所を指定します。 この例では、VCL スニペットは `recv` スニペット。 スニペットが VCL バージョンに挿入されると、に追加されます。 `vcl_recv` デフォルトの Fastly VCL コードの下および任意のオブジェクトの上のサブルーチン。

- `content`  – 改行なしで 1 行で実行する VCL コードのスニペット。

環境のコードを確認して更新した後、次のいずれかの方法を使用して、カスタム VCL スニペットを Fastly サービス設定に追加します。

- [カスタム VCL スニペットを管理者から追加します。](#add-the-custom-vcl-snippet). 管理者にアクセスできる場合は、この方法をお勧めします。 （必須 [Fastly バージョン 1.2.58](fastly-configuration.md#upgrade) （またはそれ以降）。

- JSON コードの例をファイルに保存します（例： `allowlist.json`）および [fastly API を使用してアップロードします](fastly-vcl-custom-snippets.md#manage-custom-vcl-snippets-using-the-api). 管理者にアクセスできない場合は、この方法を使用します。

## カスタム VCL スニペットの追加

{{admin-login-step}}

1. クリック **ストア** > 設定 > **設定** > **詳細** > **システム**.

1. を展開 **フルページキャッシュ** > **Fastly 設定** > **カスタム VCL スニペット**.

1. クリック **カスタムスニペットの作成**.

1. VCL スニペットの値を追加します。

   - **名前** — `block_bad_referrer`

   - **タイプ** — `recv`

   - **優先度** — `5`

   - **VCL** スニペットコンテンツ —

     ```conf
     set req.http.Referer-Host = regsub(req.http.Referer,
     "^https?://?([^:/\s]+).*$", "1");
     if (table.lookup(referrer_blocklist, req.http.Referer-Host)) {
       error 403 "Forbidden";
     }
     ```

1. クリック **作成**.

   ![カスタム リファラーブロック VCL スニペットの作成](/help/assets/cdn/fastly-create-referrer-block-snippet.png)

1. ページの再読み込み後、 **Fastly への VCL のアップロード** が含まれる *Fastly 設定* セクション。

1. アップロードが完了したら、ページ上部の通知に従ってキャッシュを更新します。

Fastly は、アップロード処理中に更新された VCL バージョンを検証します。 検証に失敗した場合は、カスタム VCL スニペットを編集して問題を修正します。 次に、VCL を再度アップロードします。

{{automate-vcl-snippet-deployment}}

{{$include /help/_includes/vcl-snippet-modify.md}}

{{$include /help/_includes/vcl-snippet-delete.md}}
