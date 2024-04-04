---
title: リクエストを許可するカスタム VCL
description: Fastly Edge ACL リストとカスタム VCL スニペットを使用して、受信リクエストをフィルタリングし、Adobe Commerceサイトの IP アドレスでアクセスを許可します。
feature: Cloud, Configuration, Security
exl-id: a6ee958a-c3d3-47be-b2df-510707f551fc
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '848'
ht-degree: 0%

---

# リクエストを許可するカスタム VCL

カスタム VCL コードスニペットを使用して Fastly Edge ACL リストを使用し、受信リクエストをフィルタリングし、IP アドレスでアクセスを許可することができます。 ACL リストは、許可する IP アドレスを指定します。

を作成し許可リストに加えるて、内部開発者と承認済み外部サービスの指定 IP アドレスからの要求のみが許可されるように、ステージング環境へのアクセスを制限します。 また、ステージング環境および実許可リストに加える稼動環境で管理者へのアクセスを保護するためのを作成することもできます。

次の例は、カスタム VCL スニペットを [Fastly アクセス制御リスト (ACL)](https://docs.fastly.com/guides/access-control-lists/about-acls) クラウドインフラストラクチャプロジェクト環境上のAdobe Commerceの管理者へのアクセスを保護する。 カスタム VCL スニペットをクラウド環境に追加すると、Fastly は ACL に含まれる IP アドレスからのリクエストのみを許可します。

>[!TIP]
>
>公にアクセスできないステージング環境および統合環境の場合は、 [[!DNL Cloud Console]](../project/overview.md#access-the-project-web-interface) :IP アドレスでサイト全体へのアクセスを管理します。

**前提条件：**


{{$include /help/_includes/vcl-snippet-prerequisites.md}}

- に含めるクライアント IP アドレスのリ許可リストに加えるスト

## クライアント IP アドレスを許可するためのエッジ ACL を作成

エッジ ACL は、サイトへのアクセスを管理するための IP アドレスリストを作成します。 この例では、Edge ACL を作成し、プロジェクト環境の管理者にアクセスできるクライアント IP アドレスのリストを追加します。

{{admin-login-step}}

1. クリック **ストア** /設定/ **設定** > **詳細** > **システム**.

1. 展開 **フルページキャッシュ** > **Fastly 設定** > **Edge ACL**.

1. ACL コンテナを作成します。

   - クリック **ACL を追加**.

   - 次の日： *ACL コンテナ* ページに、 **ACL 名**—`allowlist`.

   - 選択 **変更後に有効化** をクリックして、編集中の Fastly サービス設定のバージョンに変更をデプロイします。

   - クリック **アップロード** ACL を Fastly サービス設定に接続します。

1. 管理者へのアクセスを許可する IP アドレスのリストを追加します。

   - 設定アイコン ( `allowlist` ACL。

   - を追加して保存します。 *IP 値* 各クライアントの IP アドレスに対して使用できます。

   - クリック **キャンセル** をクリックして、システム設定ページに戻ります。

1. クリック **設定を保存**.

1. ページ上部の通知に従ってキャッシュを更新します。

## 管理者アクセスを保護するためのカスタム VCL スニペットを作成します

次のカスタム VCL スニペットコード（JSON 形式）は、管理者に要求をフィルタリングし、クライアント IP アドレスが `allowlist` ACL。

```json
{
  "name": "allowlist",
  "dynamic": "0",
  "type": "recv",
  "priority": "5",
  "content": "if ((req.url ~ \"^/admin\") && !(client.ip ~ allowlist) && !req.http.Fastly-FF) { error 403 \"Forbidden\"; }"
}
```

前 [カスタムスニペットの作成](https://experienceleague.adobe.com/docs/commerce-cloud-service/user-guide/cdn/custom-vcl-snippets/fastly-vcl-allowlist.html#add-the-custom-vcl-snippet) この例では、値を確認して、変更を行う必要があるかどうかを判断します。 次に、各フィールドに各値 ( `type` を「タイプ」フィールドに入力します。 `content` を「コンテンツ」フィールドに入力します。

- `name` - VCL スニペットの名前。 この例の場合、 `allowlist`.

- `priority` - VCL スニペットを実行するタイミングを決定します。 優先度は次のとおりです。 `5` を即座に実行して、許可されている IP アドレスからの管理要求かどうかを確認します。 スニペットは、デフォルトのMagentoVCL スニペット (`magentomodule_*`) には優先度 50 が割り当てられていました。 スニペットを実行するタイミングに応じて、各カスタムスニペットの優先度を 50 より大きい値または小さい値に設定します。 優先度の低いスニペットが最初に実行されます。

- `type`  — バージョン管理された VCL コードにスニペットを挿入する場所を指定します。 この VCL は `recv` スニペットコードを `vcl_recv` デフォルトの Fastly VCL コードの下のサブルーチンと、任意のオブジェクトの上のサブルーチン。

- `content`  — 実行する VCL コードのスニペット。 この例では、コードは要求を管理者にフィルターし、クライアント IP アドレスが `allowlist` ACL。 アドレスが一致しない場合、要求は `403 Forbidden` エラー。

  管理者の URL が変更された場合は、サンプルの値 `/admin` を設定します。 例： `/company-admin`.

このコードサンプルでは、条件 `!req.http.Fastly-FF` を使用する際には重要です。 [原点シールド](fastly-custom-cache-configuration.md#configure-back-ends-and-origin-shielding). このコードを削除または編集しないでください。

お使いの環境のコードを確認および更新した後、次のいずれかの方法を使用して、カスタム VCL スニペットを Fastly サービス設定に追加します。

- [管理者からカスタム VCL スニペットを追加する](#add-the-custom-vcl-snippet). 管理者にアクセスできる場合は、この方法をお勧めします。 （が必要です） [Magento2 バージョン 1.2.58 の Fastly CDN モジュール](fastly-configuration.md#upgrade) またはそれ以降 )。

- JSON コードの例をファイルに保存します ( 例： `allowlist.json`) および [Fastly API を使用してアップロードする](fastly-vcl-custom-snippets.md#manage-custom-vcl-snippets-using-the-api). 管理者にアクセスできない場合は、このメソッドを使用します。

## カスタム VCL スニペットを追加する

{{admin-login-step}}

1. クリック **ストア** /設定/ **設定** > **詳細** > **システム**.

1. 展開 **フルページキャッシュ** > **Fastly 設定** > **カスタム VCL スニペット**.

1. クリック **カスタムスニペットを作成**.

1. VCL スニペット値を追加します。

   - **名前** — `allowlist`

   - **タイプ** — `recv`

   - **優先度** — `5`

   - 次を追加： **VCL** スニペットコンテンツ：

     ```conf
     if ((req.url ~ "^/admin") && !(client.ip ~ allowlist) && !req.http.Fastly-FF) { error 403 "Forbidden";}
     ```

1. クリック **作成** 名前のパターンを持つ VCL スニペットファイルを生成するには `type_priority_name.vcl`例： `recv_5_allowlist.vcl`

1. ページがリロードされたら、「 **VCL を Fastly にアップロード** （内） *Fastly 設定* 「 」セクションを開き、Fastly サービス設定にファイルを追加します。

1. アップロードが完了したら、ページ上部の通知に従ってキャッシュを更新します。

アップロードプロセス中に、VCL コードの更新バージョンを正しく検証します。 検証が失敗した場合は、カスタム VCL スニペットを編集して問題を修正します。 次に、VCL を再度アップロードします。

{{$include /help/_includes/vcl-snippet-modify.md}}

{{$include /help/_includes/vcl-snippet-delete.md}}
