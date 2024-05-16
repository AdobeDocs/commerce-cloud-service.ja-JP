---
title: リクエストを許可するカスタム VCL
description: Fastly Edge ACL リストとカスタム VCL スニペットを使用して、受信リクエストをフィルタリングし、によってAdobe Commerce サイトの IP アドレスでアクセスを許可します。
feature: Cloud, Configuration, Security
exl-id: a6ee958a-c3d3-47be-b2df-510707f551fc
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '848'
ht-degree: 0%

---

# リクエストを許可するカスタム VCL

カスタム VCL コードスニペットを含んだ Fastly Edge ACL リストを使用して、受信リクエストをフィルタリングし、IP アドレスによるアクセスを許可できます。 ACL リストは、許可する IP アドレスを指定します。

許可リストを作成して、ステージング環境へのアクセスを制限し、内部開発者および承認済みの外部サービス用に指定された IP アドレスからのリクエストのみが許可されるようにします。 また、ステージング環境と実稼動環境で管理者へのアクセスを保護するための許可リストを作成することもできます。

次の例は、カスタム VCL スニペットを [Fastly アクセス制御リスト（ACL）](https://docs.fastly.com/guides/access-control-lists/about-acls) Adobe Commerce on cloud infrastructure プロジェクト環境の管理者へのアクセスを保護します。 カスタム VCL スニペットをクラウド環境に追加する場合、Fastly では、ACL に含まれる IP アドレスからのリクエストのみを許可します。

>[!TIP]
>
>公開アクセスを禁止するステージング環境および統合環境の場合は、で使用可能な HTTP アクセス制御オプションを使用します [[!DNL Cloud Console]](../project/overview.md#access-the-project-web-interface) サイト全体へのアクセスを IP アドレスで管理します。

**前提条件：**


{{$include /help/_includes/vcl-snippet-prerequisites.md}}

- 許可リストに含めるクライアント IP アドレスのリスト

## クライアント IP アドレスを許可するエッジ ACL を作成する

エッジ ACL は、サイトへのアクセスを管理するための IP アドレスリストを作成します。 この例では、Edge ACL を作成して、プロジェクト環境の管理者にアクセスできるクライアント IP アドレスのリストを追加します。

{{admin-login-step}}

1. クリック **ストア** > 設定 > **設定** > **詳細** > **システム**.

1. を展開 **フルページキャッシュ** > **Fastly 設定** > **エッジ ACL**.

1. ACL コンテナを作成します。

   - クリック **ACL を追加**.

   - 日 *ACL コンテナ* ページ、を入力 **ACL 名**—`allowlist`.

   - を選択 **変更後にアクティベート** 編集中の Fastly サービス設定のバージョンに変更をデプロイする場合。

   - クリック **Upload** をクリックして、ACL を Fastly サービス設定に添付します。

1. 管理者にアクセスできる IP アドレスのリストを追加します。

   - の「設定」アイコンをクリックします `allowlist` ACL。

   - を追加して保存します。 *IP 値* （クライアント IP アドレスごとに）。

   - クリック **キャンセル** 「システム設定」ページに戻ります。

1. クリック **設定を保存**.

1. ページ上部の通知に従ってキャッシュを更新します。

## 管理アクセスを保護するためのカスタム VCL スニペットの作成

次のカスタム VCL スニペットコード（JSON 形式）は、管理者へのリクエストをフィルタリングし、クライアントの IP アドレスがのアドレスと一致した場合にアクセスを許可するロジックを示しています。 `allowlist` ACL。

```json
{
  "name": "allowlist",
  "dynamic": "0",
  "type": "recv",
  "priority": "5",
  "content": "if ((req.url ~ \"^/admin\") && !(client.ip ~ allowlist) && !req.http.Fastly-FF) { error 403 \"Forbidden\"; }"
}
```

次の前 [カスタムスニペットの作成](https://experienceleague.adobe.com/docs/commerce-cloud-service/user-guide/cdn/custom-vcl-snippets/fastly-vcl-allowlist.html#add-the-custom-vcl-snippet) この例では、値を確認して、変更が必要かどうかを判断します。 次に、各値をそれぞれのフィールド（例：）に入力します `type` 「タイプ」フィールドに移動します。 `content` 「コンテンツ」フィールドに移動します。

- `name` — VCL スニペットの名前。 この例では、 `allowlist`.

- `priority` - VCL スニペットを実行するタイミングを指定します。 優先度は次のとおりです `5` を使用して、管理者リクエストが許可された IP アドレスからのリクエストであるかどうかを直ちに実行および確認します。 このスニペットは、デフォルトのMagentoVCL スニペット（`magentomodule_*`）に優先度 50 を割り当てました。 各カスタムスニペットの優先度を、スニペットを実行するタイミングに応じて 50 より高くまたは低く設定します。 優先度の低いスニペットが最初に実行されます。

- `type` - バージョン管理された VCL コードにスニペットを挿入する場所を指定します。 この VCL は `recv` にスニペットコードを追加するスニペットタイプ `vcl_recv` デフォルトの Fastly VCL コードの下およびすべてのオブジェクトの上のサブルーチン

- `content`  – 実行する VCL コードのスニペット。 この例では、コードは管理者へのリクエストをフィルタリングし、クライアント IP アドレスがのアドレスと一致する場合はアクセスを許可します `allowlist` ACL。 アドレスが一致しない場合、リクエストは次のコードでブロックされます。 `403 Forbidden` エラー。

  管理者の URL が変更された場合は、サンプル値を `/admin` と環境の URL。 例： `/company-admin`.

このコードサンプルでは、条件は `!req.http.Fastly-FF` を使用する場合に重要 [原点シールド](fastly-custom-cache-configuration.md#configure-back-ends-and-origin-shielding). このコードを削除または編集しないでください。

環境のコードを確認して更新した後、次のいずれかの方法を使用して、カスタム VCL スニペットを Fastly サービス設定に追加します。

- [カスタム VCL スニペットを管理者から追加します。](#add-the-custom-vcl-snippet). 管理者にアクセスできる場合は、この方法をお勧めします。 （必須 [Magento 2 バージョン 1.2.58 用 Fastly CDN モジュール](fastly-configuration.md#upgrade) （またはそれ以降）。

- JSON コードの例をファイルに保存します（例： `allowlist.json`）および [fastly API を使用してアップロードします](fastly-vcl-custom-snippets.md#manage-custom-vcl-snippets-using-the-api). 管理者にアクセスできない場合は、この方法を使用します。

## カスタム VCL スニペットの追加

{{admin-login-step}}

1. クリック **ストア** > 設定 > **設定** > **詳細** > **システム**.

1. を展開 **フルページキャッシュ** > **Fastly 設定** > **カスタム VCL スニペット**.

1. クリック **カスタムスニペットの作成**.

1. VCL スニペットの値を追加します。

   - **名前** — `allowlist`

   - **タイプ** — `recv`

   - **優先度** — `5`

   - を追加 **VCL** スニペットコンテンツ：

     ```conf
     if ((req.url ~ "^/admin") && !(client.ip ~ allowlist) && !req.http.Fastly-FF) { error 403 "Forbidden";}
     ```

1. クリック **作成** 名前が pattern の VCL スニペット ファイルを生成するには `type_priority_name.vcl`、例： `recv_5_allowlist.vcl`

1. ページの再読み込み後、 **Fastly への VCL のアップロード** が含まれる *Fastly 設定* Fastly サービス設定にファイルを追加するためのセクションです。

1. アップロードが完了したら、ページ上部の通知に従ってキャッシュを更新します。

Fastly は、アップロードプロセス中に VCL コードの更新バージョンを検証します。 検証に失敗した場合は、カスタム VCL スニペットを編集して問題を修正します。 次に、VCL を再度アップロードします。

{{$include /help/_includes/vcl-snippet-modify.md}}

{{$include /help/_includes/vcl-snippet-delete.md}}
