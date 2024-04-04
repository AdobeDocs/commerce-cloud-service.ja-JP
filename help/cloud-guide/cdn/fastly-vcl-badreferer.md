---
title: 紹介スパムをブロック
description: Fastly Edge 辞書とカスタム VCL スニペットを使用して、サイトからの紹介スパムをブロックします。
feature: Cloud, Configuration, Security
exl-id: 665bac93-75db-424f-be2c-531830d0e59a
source-git-commit: 7a181af2149eef7bfaed4dd4d256b8fa19ae1dda
workflow-type: tm+mt
source-wordcount: '684'
ht-degree: 0%

---

# 紹介スパムをブロック

次の例は、 [Fastly Edge Dictionary](https://docs.fastly.com/guides/edge-dictionaries/working-with-dictionaries-using-the-api) クラウドインフラストラクチャサイト上のAdobe Commerceからの紹介スパムをブロックするカスタム VCL スニペットを使用します。

>[!NOTE]
>
>カスタム VCL 設定をステージング環境に追加し、実稼動環境に対して実行する前にテストすることをお勧めします。

**前提条件：**

{{$include /help/_includes/vcl-snippet-prerequisites.md}}

- サイトログで偽のリファラル URL を確認し、ブロックするドメインのリストを作成します。

## リファラーのブロックリストに加える作成

エッジ辞書は、VCL スニペットの処理中に VCL 関数がアクセスできるキーと値のペアを作成します。 この例では、ブロックするリファラー Web サイトのリストを提供するエッジディクショナリを作成します。

{{admin-login-step}}

1. クリック **ストア** > **設定** > **設定** > **詳細** > **システム**.

1. 展開 **フルページキャッシュ** > **Fastly 設定** > **エッジ辞書**.

1. 辞書コンテナを作成します。

   - クリック **コンテナを追加**.

   - 次の日： *コンテナ* ページに、 **辞書名**—`referrer_blocklist`.

   - 選択 **変更後に有効化** をクリックして、編集中の Fastly サービス設定のバージョンに変更をデプロイします。

   - クリック **アップロード** 辞書を Fastly サービス設定に添付する場合。

1. ブロックするドメイン名のリストをに追加 `referrer_blocklist` 辞書：

   - 設定アイコン ( `referrer_blocklist` 辞書。

   - 新しい辞書にキーと値のペアを追加して保存します。 この例では、 **キー** は、ブロックするリファラー URL のドメイン名で、および **値** 次に該当 `true`.

     ![無効なリファラー辞書項目を追加します](../../assets/cdn/fastly-referrer-blocklist-dictionary.png)

   - クリック **キャンセル** をクリックして、システム設定ページに戻ります。

1. クリック **設定を保存**.

1. ページ上部の通知に従ってキャッシュを更新します。

Edge Dictionaries について詳しくは、 [エッジディクショナリの作成と使用](https://docs.fastly.com/guides/edge-dictionaries/working-with-dictionaries-using-the-api) および [カスタム VCL スニペット](https://docs.fastly.com/guides/edge-dictionaries/working-with-dictionaries-using-the-api#custom-vcl-examples) Fastly のドキュメントで。

## リファラースパムをブロックするカスタム VCL スニペットを作成

次のカスタム VCL スニペットコード（JSON 形式）は、リクエストを確認およびブロックするロジックを示しています。 VCL スニペットは、リファラー Web サイトのホストをヘッダーに取り込み、ホスト名と `referrer_blocklist` 辞書。 ホスト名が一致する場合、要求は `403 Forbidden` エラー。

```json
{
  "name": "block_bad_referrer",
  "dynamic": "0",
  "type": "recv",
  "priority": "5",
  "content": "set req.http.Referer-Host = regsub(req.http.Referer, \"^https?:\/\/?([^:\/s]+).*$\", \"\\1\"); if (table.lookup(referrer_blocklist, req.http.Referer-Host)) { error 403 \"Forbidden\"; }"
}
```

この例に基づいてスニペットを作成する前に、値を確認して、変更を加える必要があるかどうかを判断します。

- `name` - VCL スニペットの名前。 この例では、 `block_bad_referrer`.

- `dynamic`  — 値 0 は、 [通常のスニペット](https://docs.fastly.com/en/guides/using-regular-vcl-snippets) Fastly 設定用にバージョン管理された VCL にアップロードする場合。

- `priority` - VCL スニペットを実行するタイミングを決定します。 優先度は次のとおりです。 `5` デフォルトのMagentoVCL スニペット (`magentomodule_*`) には優先度 50 が割り当てられていました。 スニペットを実行するタイミングに応じて、各カスタムスニペットの優先度を 50 より大きい値または小さい値に設定します。 優先度の低いスニペットが最初に実行されます。

- `type` - VCL バージョンでスニペットを挿入する場所を指定します。 この例では、VCL スニペットは `recv` スニペット。 スニペットが VCL バージョンに挿入されると、そのスニペットが `vcl_recv` サブルーチン、既定の Fastly VCL コードの下、および任意のオブジェクトの上に配置します。

- `content` - 1 行で実行する VCL コードのスニペット（改行なし）。

お使いの環境のコードを確認および更新した後、次のいずれかの方法を使用して、カスタム VCL スニペットを Fastly サービス設定に追加します。

- [管理者からカスタム VCL スニペットを追加する](#add-the-custom-vcl-snippet). 管理者にアクセスできる場合は、この方法をお勧めします。 （が必要です） [Fastly バージョン 1.2.58](fastly-configuration.md#upgrade) またはそれ以降 )。

- JSON コードの例をファイルに保存します ( 例： `allowlist.json`) および [Fastly API を使用してアップロードする](fastly-vcl-custom-snippets.md#manage-custom-vcl-snippets-using-the-api). 管理者にアクセスできない場合は、このメソッドを使用します。

## カスタム VCL スニペットを追加する

{{admin-login-step}}

1. クリック **ストア** /設定/ **設定** > **詳細** > **システム**.

1. 展開 **フルページキャッシュ** > **Fastly 設定** > **カスタム VCL スニペット**.

1. クリック **カスタムスニペットを作成**.

1. VCL スニペット値を追加します。

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

   ![カスタムリファラーブロック VCL スニペットを作成](/help/assets/cdn/fastly-create-referrer-block-snippet.png)

1. ページがリロードされたら、「 **VCL を Fastly にアップロード** （内） *Fastly 設定* 」セクションに入力します。

1. アップロードが完了したら、ページ上部の通知に従ってキャッシュを更新します。

アップロードプロセス中に、更新された VCL バージョンを正しく検証します。 検証が失敗した場合は、カスタム VCL スニペットを編集して問題を修正します。 次に、VCL を再度アップロードします。

{{automate-vcl-snippet-deployment}}

{{$include /help/_includes/vcl-snippet-modify.md}}

{{$include /help/_includes/vcl-snippet-delete.md}}
