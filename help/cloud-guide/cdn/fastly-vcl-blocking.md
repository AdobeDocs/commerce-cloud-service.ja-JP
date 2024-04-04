---
title: リクエストをブロックするためのカスタム VCL
description: カスタム VCL スニペットを使用して、Edge Access Control List(ACL) を使用して IP アドレスによる受信リクエストをブロックします。
feature: Cloud, Configuration, Security
exl-id: 1f637612-3858-49d0-91f7-9b8823933cc9
source-git-commit: 0e9ace747cc56808108781e42b97c86756089818
workflow-type: tm+mt
source-wordcount: '961'
ht-degree: 0%

---

# リクエストをブロックするためのカスタム VCL

Magento2 の Fastly CDN モジュールを使用して、ブロックする IP アドレスのリストを含む Edge ACL を作成できます。 その後、VCL スニペットと共にそのリストを使用して、受信リクエストをブロックできます。 コードは、受信リクエストの IP アドレスを確認します。 ACL リストに含まれる IP アドレスと一致する場合、Fastly はリクエストがサイトへのアクセスをブロックし、 `403 Forbidden error`. その他のすべてのクライアント IP アドレスへのアクセスが許可されます。

**前提条件：**

{{$include /help/_includes/vcl-snippet-prerequisites.md}}

- ブロックするクライアント IP アドレスのリスト

## クライアント IP アドレスをブロックするための Edge ACL を作成

Edge ACL を作成して、ブロックする IP アドレスのリストを定義します。 ACL を作成した後、カスタム VCL スニペットで使用して、ステージングサイトまたは実稼動サイトへのアクセスを管理できます。

両方の環境で同じ名前の Edge ACL を作成して、ステージングサイトと実稼動サイトの両方のアクセスを管理します。 VCL スニペットコードは、両方の環境に適用されます。

1. 管理者にログインします。
1. に移動します。 **ストア** /設定/ **設定** > **詳細** > **システム** > **フルページキャッシュ** > **Fastly 設定**.
1. を展開します。 **Edge ACL** 」セクションに入力します。
1. クリック **ACL を追加** をクリックして、リストを作成します。 この例では、リストに「ブロックリストに加える」という名前を付けます。
1. リストに IP アドレスの値を入力します。 このリストに追加されたクライアントの IP アドレスはブロックされ、サイトにアクセスできません。
1. 必要に応じて、 **否定** 必要に応じて、「 」チェックボックスをオンにします。

VCL スニペットコード内で、名前で Edge ACL を参照します。

## のカスタム VCL を作成しブロックリストに加えるます

>[!NOTE]
>
>この例は、上級ユーザーが VCL コードスニペットを作成して、Fastly サービスにアップロードするカスタムブロックルールを設定する方法を示しています。 Adobe Commerce管理者ブロックリストに加えるは、国に基許可リストに加えるづいてまたは設定を行う場合、 [ブロック](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/BLOCKING.md) 機能は、Fastly CDN のMagento2 モジュールで利用できます。

Edge ACL を定義したら、それを使用して VCL スニペットを作成し、ACL で指定された IP アドレスへのアクセスをブロックできます。 ステージング環境と実稼動環境の両方で同じ VCL スニペットを使用できますが、各環境に個別にアップロードする必要があります。

次のカスタム VCL スニペットコード（JSON 形式）は、受信 ACL 内のアドレスと一致するクライアント IP アドレスを持つ受信要求をブロックするロジックを示していまブロックリストに加えるす。

```json
{
  "name": "blocklist",
  "dynamic": "0",
  "type": "recv",
  "priority": "5",
  "content": "if ( client.ip ~ blocklist) { error 403 \"Forbidden\"; }"
}
```

この例に基づいてスニペットを作成する前に、値を確認して、変更を加える必要があるかどうかを判断します。

- `name`:VCL スニペットの名前。 この例では、 `blocklist`.

- `priority`:VCL スニペットを実行するタイミングを決定します。 優先度は次のとおりです。 `5` を即座に実行して、管理要求が許可された IP アドレスから送信されたかどうかを確認します。 スニペットは、デフォルトのMagentoVCL スニペット (`magentomodule_*`) には優先度 50 が割り当てられていました。 スニペットを実行するタイミングに応じて、各カスタムスニペットの優先度を 50 より大きい値または小さい値に設定します。 優先度の低いスニペットが最初に実行されます。

- `type`：生成された VCL コード内のスニペットの場所を決定する VCL スニペットのタイプを指定します。 この例では、 `recv`:VCL コードを `vcl_recv` サブルーチン、ボイラープレート VCL の下、および任意のオブジェクトの上。 詳しくは、 [VCL スニペットリファレンス](https://docs.fastly.com/api/config#api-section-snippet) スニペットタイプのリスト。

- `content`：実行する VCL コードのスニペット。このスニペットは、クライアントの IP アドレスを確認します。 IP が Edge ACL にある場合、 `403 Forbidden` エラーが発生しました。 その他のすべてのクライアント IP アドレスへのアクセスが許可されます。

お使いの環境のコードを確認および更新した後、次のいずれかの方法を使用して、カスタム VCL スニペットを Fastly サービス設定に追加します。

- [管理者からカスタム VCL スニペットを追加する](#add-the-custom-vcl-snippet). 管理者にアクセスできる場合は、この方法をお勧めします。 （が必要です） [Fastly バージョン 1.2.58](fastly-configuration.md#upgrade-fastly-module) またはそれ以降 )。

- JSON コードの例をファイルに保存します ( 例： `blocklist.json`) および [Fastly API を使用してアップロードする](fastly-vcl-custom-snippets.md#manage-custom-vcl-snippets-using-the-api). 管理者にアクセスできない場合は、このメソッドを使用します。

## カスタム VCL スニペットを追加する

{{admin-login-step}}

1. クリック **ストア** /設定/ **設定** > **詳細** > **システム**.

1. 展開 **フルページキャッシュ** > **Fastly 設定** > **カスタム VCL スニペット**.

1. クリック **カスタムスニペットを作成**.

1. VCL スニペット値を追加します。

   - **名前** — `blocklist`

   - **タイプ** — `recv`

   - **優先度** — `5`

   - 次を追加： **VCL** スニペットコンテンツ：

     ```conf
     if ( client.ip ~ blocklist) { error 403 "Forbidden"; }
     ```

1. クリック **作成** 名前のパターンを持つ VCL スニペットファイルを生成するには `type_priority_name.vcl`例： `recv_5_blocklist.vcl`

1. ページがリロードされたら、「 **VCL を Fastly にアップロード** （内） *Fastly 設定* 「 」セクションを開き、Fastly サービス設定にファイルを追加します。

1. アップロード後に、ページ上部の通知に従ってキャッシュを更新します。

アップロードプロセス中に、VCL コードの更新バージョンを正しく検証します。 検証が失敗した場合は、カスタム VCL スニペットを編集して問題を修正します。 次に、VCL を再度アップロードします。

## リクエストをブロックするその他の VCL の例

次の例は、ACL リストの代わりにインライン条件ステートメントを使用して要求をブロックする方法を示しています。

>[!WARNING]
>
>この例では、VCL コードは JSON ペイロードとしてフォーマットされ、ファイルに保存して Fastly API リクエストで送信できます。 次の項目を送信できます。 [管理者からの VCL スニペット](#add-the-custom-vcl-snippet)、または Fastly API を使用する JSON 文字列として。 Fastly API を JSON 文字列と共に使用する際の検証を防ぐには、バックスラッシュを使用して特殊文字をエスケープする必要があります。

詳しくは、 [動的 VCL スニペットの使用](https://docs.fastly.com/vcl/vcl-snippets/) Fastly VCL のドキュメントの

### VCL コードのサンプル：国コードでブロック

この例では、IP アドレスに関連付けられている国に対して、2 文字の ISO 3166-1 国コードを使用します。

```json
{
  "name": "blockbycountrycode",
  "dynamic": "0",
  "type": "recv",
  "priority": "5",
  "content": "if ( client.geo.country_code == \"HK\" ) { error 405 \"Not allowed\";}"
}
```

>[!NOTE]
>
>カスタム VCL スニペットを使用する代わりに、Fastly [ブロック](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/BLOCKING.md) クラウドインフラストラクチャ管理上のAdobe Commerceの機能を使用して、国コードまたは国コードのリストによるブロックを設定できます。

### VCL コードのサンプル： HTTP User-Agent リクエストヘッダーによるブロック

```json
{
  "name": "blockbyuseragent",
  "dynamic": "0",
  "type": "recv",
  "priority": "5",
  "content": "if ( req.http.User-Agent ~ \"(UCBrowser|MQQBrowser|LieBaoFast|Mb2345Browser)\" ) {error 405 \"Not allowed\";}"
}
```

{{automate-vcl-snippet-deployment}}

{{$include /help/_includes/vcl-snippet-modify.md}}

{{$include /help/_includes/vcl-snippet-delete.md}}
