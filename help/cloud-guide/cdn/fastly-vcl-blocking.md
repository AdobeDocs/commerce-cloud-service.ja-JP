---
title: リクエストをブロックするカスタム VCL
description: カスタム VCL スニペットを使用した Edge Access Control List （ACL; エッジ アクセス コントロール リスト）を使用して、IP アドレスごとに着信要求をブロックします。
feature: Cloud, Configuration, Security
exl-id: 1f637612-3858-49d0-91f7-9b8823933cc9
source-git-commit: 0e9ace747cc56808108781e42b97c86756089818
workflow-type: tm+mt
source-wordcount: '961'
ht-degree: 0%

---

# リクエストをブロックするカスタム VCL

Magento 2 の Fastly CDN モジュールを使用して、ブロックする IP アドレスのリストを含む Edge ACL を作成できます。 次に、このリストを VCL スニペットと一緒に使用して、着信リクエストをブロックできます。 このコードは、受信リクエストの IP アドレスを確認します。 ACL リストに含まれる IP アドレスと一致する場合、Fastly は、リクエストがサイトにアクセスするのをブロックし、を返します。 `403 Forbidden error`. その他すべてのクライアント IP アドレスへのアクセスが許可されます。

**前提条件：**

{{$include /help/_includes/vcl-snippet-prerequisites.md}}

- ブロックするクライアント IP アドレスのリスト

## クライアント IP アドレスをブロックするエッジ ACL を作成します

エッジ ACL を作成して、ブロックする IP アドレスのリストを定義します。 ACL を作成したら、それをカスタム VCL スニペットで使用して、ステージングまたは実稼動サイトへのアクセスを管理できます。

両方の環境で同じ名前のエッジ ACL を作成して、ステージングサイトと実稼動サイトの両方のアクセスを管理します。 VCL スニペットコードは、両方の環境に適用されます。

1. 管理者にログインします。
1. に移動します。 **ストア** > 設定 > **設定** > **詳細** > **システム** > **フルページキャッシュ** > **Fastly 設定**.
1. を展開します。 **エッジ ACL** セクション。
1. クリック **ACL を追加** リストを作成します。 この例では、リストに「ブロックリスト」という名前を付けます。
1. リストに IP アドレス値を入力します。 このリストに追加されたクライアントの IP アドレスはすべてブロックされ、サイトにアクセスできません。
1. オプションで、 **否定** 必要に応じてチェックボックスをオンにします。

VCL スニペットコードでは、エッジ ACL を名前で参照します。

## ブロックリストのカスタム VCL の作成

>[!NOTE]
>
>この例では、上級ユーザーに対して、Fastly サービスにアップロードするカスタムブロッキングルールを設定する VCL コードスニペットの作成方法を示します。 Adobe Commerce管理者が次を使用して、国に基づいてをブロックリスト許可リストに加えるまたは設定できます [ブロック](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/BLOCKING.md) Magento 2 用 Fastly CDN モジュールで使用できる機能。

エッジ ACL を定義した後、それを使用して、ACL で指定された IP アドレスへのアクセスをブロックする VCL スニペットを作成できます。 ステージング環境と実稼動環境の両方で同じ VCL スニペットを使用できますが、スニペットは各環境に個別にアップロードする必要があります。

次のカスタム VCL スニペットコード（JSON 形式）は、ブロックリスト ACL 内のアドレスと一致するクライアント IP アドレスを持つ受信リクエストをブロックするロジックを示しています。

```json
{
  "name": "blocklist",
  "dynamic": "0",
  "type": "recv",
  "priority": "5",
  "content": "if ( client.ip ~ blocklist) { error 403 \"Forbidden\"; }"
}
```

この例に基づいてスニペットを作成する前に、値を確認して、変更が必要かどうかを判断してください。

- `name`:VCL スニペットの名前。 以下の例では、という名前を使用しています。 `blocklist`.

- `priority`:VCL スニペットを実行するタイミングを指定します。 優先度は次のとおりです `5` を使用して直ちに管理リクエストが許可された IP アドレスからのリクエストであるかどうかを確認します。 このスニペットは、デフォルトのMagentoVCL スニペット（`magentomodule_*`）に優先度 50 を割り当てました。 各カスタムスニペットの優先度を、スニペットを実行するタイミングに応じて 50 より高くまたは低く設定します。 優先度の低いスニペットが最初に実行されます。

- `type`：生成された VCL コード内のスニペットの場所を決定する VCL スニペットのタイプを指定します。 この例では、を使用します `recv`:VCL コードを `vcl_recv` サブルーチン。ボイラープレート VCL の下、オブジェクトの上にあります。 を参照してください。 [Fastly VCL スニペットの参照](https://docs.fastly.com/api/config#api-section-snippet) を入力します。

- `content`：実行する VCL コードのスニペット。クライアントの IP アドレスを確認します。 IP が Edge ACL に含まれている場合は、次のアドレスからのアクセスがブロックされます。 `403 Forbidden` web サイト全体のエラー。 その他すべてのクライアント IP アドレスへのアクセスが許可されます。

環境のコードを確認して更新した後、次のいずれかの方法を使用して、カスタム VCL スニペットを Fastly サービス設定に追加します。

- [カスタム VCL スニペットを管理者から追加します。](#add-the-custom-vcl-snippet). 管理者にアクセスできる場合は、この方法をお勧めします。 （必須 [Fastly バージョン 1.2.58](fastly-configuration.md#upgrade-fastly-module) （またはそれ以降）。

- JSON コードの例をファイルに保存します（例： `blocklist.json`）および [fastly API を使用してアップロードします](fastly-vcl-custom-snippets.md#manage-custom-vcl-snippets-using-the-api). 管理者にアクセスできない場合は、この方法を使用します。

## カスタム VCL スニペットの追加

{{admin-login-step}}

1. クリック **ストア** > 設定 > **設定** > **詳細** > **システム**.

1. を展開 **フルページキャッシュ** > **Fastly 設定** > **カスタム VCL スニペット**.

1. クリック **カスタムスニペットの作成**.

1. VCL スニペットの値を追加します。

   - **名前** — `blocklist`

   - **タイプ** — `recv`

   - **優先度** — `5`

   - を追加 **VCL** スニペットコンテンツ：

     ```conf
     if ( client.ip ~ blocklist) { error 403 "Forbidden"; }
     ```

1. クリック **作成** 名前が pattern の VCL スニペット ファイルを生成するには `type_priority_name.vcl`、例： `recv_5_blocklist.vcl`

1. ページの再読み込み後、 **Fastly への VCL のアップロード** が含まれる *Fastly 設定* Fastly サービス設定にファイルを追加するためのセクションです。

1. アップロード後、ページ上部の通知に従ってキャッシュを更新します。

Fastly は、アップロードプロセス中に VCL コードの更新バージョンを検証します。 検証に失敗した場合は、カスタム VCL スニペットを編集して問題を修正します。 次に、VCL を再度アップロードします。

## リクエストをブロックする VCL の追加例

次の例は、ACL リストの代わりにインライン条件ステートメントを使用してリクエストをブロックする方法を示しています。

>[!WARNING]
>
>これらの例では、VCL コードは JSON ペイロードとしてフォーマットされ、ファイルに保存して Fastly API リクエストで送信できます。 を送信できます [管理者からの VCL スニペット](#add-the-custom-vcl-snippet)または、Fastly API を使用して JSON 文字列として返します。 JSON 文字列で Fastly API を使用する場合に検証を防ぐには、バックスラッシュを使用して特殊文字をエスケープする必要があります。

参照： [動的 VCL スニペットの使用](https://docs.fastly.com/vcl/vcl-snippets/) Fastly VCL ドキュメントを参照してください。

### VCL コード サンプル：国コードによるブロック

この例では、IP アドレスに関連付けられた国に、2 文字の ISO 3166-1 国コードを使用しています。

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
>カスタム VCL スニペットを使用する代わりに、Fastly を使用できます。 [ブロック](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/BLOCKING.md) クラウドインフラストラクチャー管理のAdobe Commerceの機能を使用して、国コードまたは国コードのリスト別のブロックを設定できます。

### VCL コード例：HTTP ユーザーエージェントリクエストヘッダーによるブロック

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
