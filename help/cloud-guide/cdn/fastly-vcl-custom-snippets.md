---
title: カスタム VCL スニペットの基本を学ぶ
description: Varnish Control Language コードスニペットを使用して、Adobe Commerce用の Fastly サービス設定をカスタマイズする方法について説明します。
feature: Cloud, Configuration, Services
exl-id: df0f0906-8ffa-41a1-a31c-d36deb5a6a31
source-git-commit: 89c57a486545d6165e0407f913f4fa4cf6c95abe
workflow-type: tm+mt
source-wordcount: '1947'
ht-degree: 0%

---

# カスタム VCL の概要

Fastly は、Varnish Configuration Language （VCL）のカスタマイズバージョンをサポートし、お客様の要件に合わせて Fastly サービス設定をカスタマイズします。

カスタム VCL スニペットは、Adobe Commerce サイトにアップロードされたアクティブな VCL バージョンに追加される VCL ロジックのブロックです。 カスタム VCL スニペットは、リクエストトラフィックに対する Fastly キャッシュサービスの応答方法を変更します。 例えば、カスタム VCL スニペットを追加して、指定されたクライアント IP アドレスからの要求トラフィックのみを許可することができます。 または、Adobe Commerce サイトに紹介スパムを送信することで知られている web サイトからのトラフィックをブロックするスニペットを作成します。

カスタム VCL スニペット（生成、コンパイル、すべての Fastly キャッシュに送信）が、サーバーを停止させることなく読み込みおよびアクティブ化されます。

>[!NOTE]
>
>カスタム VCL コード、エッジ辞書、ACL を Fastly モジュール設定に追加する前に、Fastly キャッシュサービスがデフォルト設定で機能することを確認してください。 参照： [Fastly サービスの設定](fastly-configuration.md).

Fastly では、次の 2 種類のカスタム VCL スニペットをサポートしています。

- [通常のスニペット](https://docs.fastly.com/en/guides/about-vcl-snippets) – カスタムの通常の VCL スニペットは、特定の VCL バージョンに対してコード化されます。 管理者または Fastly API から、通常の VCL スニペットを作成、変更、およびデプロイできます。

- [動的スニペット](https://docs.fastly.com/en/guides/using-dynamic-vcl-snippets)- Fastly API を使用して作成された VCL スニペット サービスの Fastly VCL バージョンを更新しなくても、動的スニペットを変更およびデプロイできます。

カスタム VCL スニペットとエッジ辞書およびアクセス制御リスト（ACL）を使用して、カスタム コードで使用するデータを保存することをお勧めします。

- [**エッジディクショナリ**](https://docs.fastly.com/guides/edge-dictionaries/about-edge-dictionaries)- カスタム VCL スニペットから参照できるディクショナリコンテナに、キーと値のペアとしてデータを格納します。

- [**エッジ ACL**](https://docs.fastly.com/guides/access-control-lists/about-acls)- カスタム VCL スニペットを使用して実装されたブロックまたは許可ルールのアクセス制御リストを定義するクライアント IP アドレス データを格納します

辞書と ACL データは、ネットワーク地域全体でアクセス可能な Fastly Edge ノードにデプロイされます。 また、データは、ステージング環境または実稼動環境用に VCL コードを再デプロイしなくても、ネットワーク全体で動的に更新できます。

>[!NOTE]
>
>ステージング環境または実稼動環境に追加できるのは、次の場合のみです [設定済みの Fastly サービス](fastly-configuration.md) その環境の場合。

## チュートリアル

このチュートリアルと例では、Edge ディクショナリと Edge ACL を含んだ通常のカスタム VCL スニペットを使用して、Adobe Commerce用の Fastly サービス設定をカスタマイズする方法について説明します。 詳しくは、Fastly のドキュメントを参照してください。

- [Fastly VCL のガイド](https://docs.fastly.com/guides/vcl/guide-to-vcl)- Fastly Varnish の実装、Fastly VCL の拡張機能、および Varnish と VCL の詳細を学習するためのリソースに関する情報です。
- [Fastly VCL 参照](https://docs.fastly.com/guides/vcl/)- Fastly カスタム VCL およびカスタム VCL スニペットの開発とトラブルシューティングに関する詳細なプログラミング リファレンス。

カスタム VCL スニペットは、Adobe Commerce管理者から、または Fastly API を使用して作成および管理できます。

- [Adobe Commerce管理者](#manage-custom-vcl-from-admin)—VCL の変更内容の検証、アップロード、Fastly サービス設定への適用を自動化するため、Adobe Commerce管理を使用してカスタム VCL スニペットを管理することをお勧めします。 また、Fastly サービス設定に追加されたカスタム VCL スニペットを、管理者で表示して編集することもできます。

- [Fastly API](#manage-vcl-using-the-api) – 管理者にアクセスできない場合は、Fastly API を使用してカスタム VCL スニペットを管理します。 例えば、API を使用して、サイトがダウンしたときに Fastly サービス設定をトラブルシューティングしたり、カスタム VCL スニペットを追加したりします。 また、API を使用してのみ完了できる操作もあります。 たとえば、API を使用して、古い VCL バージョンを再アクティブ化したり、指定した VCL バージョンに含まれるすべての VCL スニペットを表示する必要があります。 参照： [VCL スニペットの API クイックリファレンス](#api-quick-reference-for-vcl-snippets).

### VCL スニペットコードの例

次の例は、クライアントの IP アドレスでトラフィックをフィルタリングするカスタム VCL スニペット（JSON 形式）を示しています。

```json
{
  "service_id": "FASTLY_SERVICE_ID",
  "version": "{Editable Version #}",
  "name": "apply_acl",
  "priority": "100",
  "dynamic": "1",
  "type": "hit",
  "content": "if ((client.ip ~ {ACLNAME}) && !req.http.Fastly-FF){ error 403; }"
}
```

>[!WARNING]
>
>この例では、VCL コードは JSON ペイロードとしてフォーマットされ、ファイルに保存して Fastly API リクエストで送信できます。 スニペットを API リクエストの JSON として送信する際に JSON 検証エラーが発生しないようにするには、バックスラッシュを使用してコード内の特殊文字をエスケープします。 参照： [動的 VCL スニペットの使用](https://docs.fastly.com/vcl/vcl-snippets/) Fastly VCL ドキュメントを参照してください。 管理者から VCL スニペットを送信する場合、特殊文字をエスケープする必要はありません。

の VCL ロジック `content` フィールドは、次のアクションを実行します。

- 受信 IP アドレスをチェックします。 `client.ip` リクエストごとに

- に含まれる IP アドレスを持つすべてのリクエストをブロックします *ACLNAME* エッジ ACL、を返す `403 Forbidden` エラー

次の表に、カスタム VCL スニペットのキーデータの詳細を示します。 詳しくは、を参照してください [VCL スニペット](https://docs.fastly.com/api/config#api-section-snippet) Fastly のドキュメントのを参照してください。

| 値 | 説明 |
|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `API_KEY` | Fastly アカウントにアクセスするための API キー。 参照： [資格情報の取得](fastly-configuration.md). |
| `active` | スニペットまたはバージョンのアクティブなステータス。 戻り値 `true` または `false`. true の場合、スニペットまたはバージョンは使用中です。 バージョン番号を使用してアクティブなスニペットのクローンを作成します。 |
| `content` | 実行する VCL コードのスニペット。 Fastly では、すべての VCL 言語機能をサポートしているわけではありません。 また、Fastly は、カスタム機能を備えた拡張機能を提供します。 サポートされる機能について詳しくは、 [Fastly VCL プログラミングリファレンス](https://docs.fastly.com/vcl/reference/). |
| `dynamic` | スニペットの動的ステータス。 戻り値 `false` （用） [通常のスニペット](https://docs.fastly.com/en/guides/about-vcl-snippets) fastly サービス設定用のバージョン管理された VCL に含まれる。 戻り値 `true` の場合 [動的スニペット](https://docs.fastly.com/vcl/vcl-snippets/using-dynamic-vcl-snippets/) これは、新しい VCL バージョンを必要とせずに変更およびデプロイできます。 |
| `number` | スニペットが含まれている VCL バージョン番号。 Fastly での使用 *編集可能なバージョン #* の値の例です。 API からカスタムスニペットを追加する場合は、API リクエストにバージョン番号を含めてください。 管理者からカスタム VCL を追加すると、のバージョンが提供されます。 |
| `priority` | 次の範囲の数値 `1` 対象： `100` カスタム VCL スニペット コードを実行するタイミングを指定します。 優先度の値が小さいスニペットが最初に実行されます。 指定しない場合、 `priority` デフォルト値： `100`.<p>優先順位の値がのカスタム VCL スニペット `5` これは、リクエストルーティング（ブロックおよび許可リストとリダイレクト）を実装する VCL コードに最適です。 優先度 `100` は、デフォルトの VCL スニペットコードを上書きする場合に最適です。<p>すべて [デフォルトの VCL スニペット](fastly-configuration.md#upload-vcl-snippets) Magento-Fastly モジュールに含まれる `priority=50`.<ul><li>次のような高い優先度を割り当てます `100` 他のすべての VCL 関数の後にカスタム VCL コードを実行し、既定の VCL コードをオーバーライドします。</li></ul> |
| `service_id` | 特定のステージング環境または実稼動環境の Fastly サービス ID。 この ID は、プロジェクトがクラウドインフラストラクチャー上のAdobe Commerceに追加されたときに割り当てられます [Fastly サービスアカウント](fastly.md#fastly-service-account-and-credentials). |
| `type` | 生成されたスニペットを挿入する場所（例：）を指定します。 `init` （サブルーチンの上）と `recv` （サブルーチン内）。 詳しくは、Fastly を参照してください [VCL スニペット](https://docs.fastly.com/api/config#api-section-snippet) 参照。 |

## 管理者からのカスタム VCL の管理

次のことができます [カスタム VCL スニペットを追加する](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/CUSTOM-VCL-SNIPPETS.md) から *Fastly 設定* > *カスタム VCL スニペット* 」セクションを選択します。

![カスタム VCL スニペットの管理](../../assets/cdn/fastly-edit-snippets.png)

この *カスタム VCL スニペット* 表示には、管理者を通じて追加されたスニペットのみが表示されます。 Fastly API を使用してスニペットを追加する場合は、API を使用して次の操作を行います [それらを管理](#manage-vcl-using-the-api).

次の例は、カスタム VCL スニペットを管理者から作成および管理する方法と、Fastly Edge モジュールおよび Edge 辞書を使用する方法を示しています。

- [CMS バックエンドへのリクエストの再ルーティング](fastly-vcl-wordpress.md)
- [参照スパムをブロック](fastly-vcl-badreferer.md)
- [リファラルスパムをブロック](fastly-vcl-badreferer.md)
- [IP 用のカスタム VCL許可リスト](fastly-vcl-allowlist.md)
- [IP 用のカスタム VCLブロックリスト](fastly-vcl-blocking.md)
- [Fastly キャッシュのバイパス](fastly-vcl-bypass-to-origin.md)

## API を使用した VCL の管理

次のウォークスルーでは、通常の VCL スニペットファイルを作成し、Fastly API を使用して Fastly サービス設定に追加する方法を示します。 からスニペットを作成および管理できます。 *ターミナル* アプリケーション。 特定の環境への SSH 接続は必要ありません。

**前提条件：**

- Fastly サービスを使用するには、クラウドインフラストラクチャ環境でAdobe Commerceを設定します。 参照： [Fastly の設定](fastly-configuration.md).

- [Fastly API 資格情報の取得](fastly-configuration.md) Fastly API へのリクエストを認証します。 正しい環境（ステージングまたは実稼動）の資格情報を取得していることを確認します。

- cURL コマンドで使用できる bash 環境変数として、Fastly サービスの資格情報を保存します。

  ```bash
  export FASTLY_SERVICE_ID=<Service-ID>
  ```

  ```bash
  export FASTLY_API_TOKEN=<API-Token>
  ```

  書き出された環境変数は、現在の bash セッションでのみ使用でき、ターミナルを閉じると失われます。 新しい値を書き出して変数を再定義できます。 Fastly に関連する、書き出された変数のリストを表示するには：

  ```bash
  export | grep FASTLY
  ```

## VCL スニペットの追加

このチュートリアルでは、Fastly API を使用してカスタムスニペットを追加する基本的な手順を説明します。

>[!NOTE]
>
>Adobe Commerce Admin からカスタム VCL スニペットを管理する方法については、を参照してください。 [Adobe Commerce Admin からの VCL の管理](#manage-custom-vcl-from-admin).


**前提条件**

{{$include /help/_includes/vcl-snippet-prerequisites.md}}

### 手順 1：アクティブな VCL バージョンを探す

Fastly API の使用 [バージョンを取得](https://docs.fastly.com/api/config#version_dfde9093f4eb0aa2497bbfd1d9415987) アクティブな VCL のバージョン番号を取得する操作：

```bash
curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/active
```

JSON 応答で、で返されたアクティブな VCL バージョン番号をメモします。 `number` キー（例：） `"number": 99`. VCL を編集用に複製する場合は、バージョン番号が必要です。

```json
{
  "testing": false,
  "locked": true,
  "number": 99,
  "active": true,
  "service_id": "872zhjyxhto5SIRb3GAE0",
  "staging": false,
  "created_at": "2019-01-29T22:38:53Z",
  "deleted_at": null,
  "comment": "Magento Module uploaded VCL",
  "updated_at": "2019-01-29T22:39:06Z",
  "deployed": false
}
```

以降の API リクエストで使用するために、アクティブなバージョン番号を bash 環境変数に保存します。

```bash
export FASTLY_VERSION_ACTIVE=<Version>
```

### 手順 2：アクティブな VCL バージョンとすべてのスニペットを複製する

カスタム VCL スニペットを追加または修正する前に、編集用にアクティブな VCL バージョンのコピーを作成する必要があります。 Fastly API の使用 [クローン](https://docs.fastly.com/api/config#version_7f4937d0663a27fbb765820d4c76c709) 操作：

```bash
curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/$FASTLY_VERSION_ACTIVE/clone -X PUT
```

JSON 応答では、バージョン番号が増分され、 *アクティブ* キー値はです `false`. 新しい非アクティブな VCL バージョンは、ローカルで修正できます。

```json
{
  "testing": false,
  "locked": false,
  "number": 100,
  "active": false,
  "service_id": "vW2bLFWhhto5SIRb3GAE0",
  "staging": false,
  "created_at": "2019-01-29T22:38:53Z",
  "deleted_at": null,
  "comment": "Magento Module uploaded VCL",
  "updated_at": "2019-01-29T22:39:06Z",
  "deployed": false
}
```

以降のコマンドで使用するために、新しいバージョン番号を bash 環境変数に保存します。

```bash
export FASTLY_EDIT_VERSION=<Version>
```

### 手順 3：カスタム VCL スニペットの作成

カスタム VCL コードを作成し、次の内容と形式で JSON ファイルに保存します。

```json
{
  "name": "<name>",
  "dynamic": "0",
  "type": "<type>",
  "priority": "100",
  "content": "<code all in one line>"
}
```

値には次が含まれます。

- `name`- VCL スニペットの名前。

- `dynamic` – これが [標準スニペット](https://docs.fastly.com/en/guides/about-vcl-snippets) または [動的スニペット](https://docs.fastly.com/guides/vcl-snippets/using-dynamic-vcl-snippets).

- `type` – 生成されたスニペットを挿入する場所を指定します。たとえば、次のような場所を指定します。 `init` （サブルーチンの上）と `recv` （サブルーチン内）。 参照： [Fastly VCL スニペットオブジェクト値](https://docs.fastly.com/api/config#snippet) これらの値について詳しくは、を参照してください。

- `priority` – 値 `1` 対象： `100` カスタム VCL スニペットコードを実行するタイミングを指定します。 値が小さいカスタム VCL スニペットが最初に実行されます。

  Fastly VCL モジュールのすべてのデフォルト VCL コードには、 `priority` 件中 `50`. アクションを最後に実行する場合、またはデフォルトの VCL コードをオーバーライドする場合は、次のように大きい数字を使用します `100`. カスタム VCL スニペットコードをすぐに実行するには、次のように、優先度を低い値に設定します `5`.

- `content` – 改行なしで 1 行で実行する VCL コードのスニペット。 参照： [カスタム VCL スニペットの例](#example-vcl-snippet-code).

### 手順 4:Fastly 設定への VCL スニペットの追加

Fastly API の使用 [スニペットを作成](https://docs.fastly.com/api/config#snippet_41e0e11c662d4d56adada215e707f30d) カスタム VCL スニペットを VCL バージョンに追加する操作。

```bash
curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/$FASTLY_EDIT_VERSION/snippet -H 'Content-Type: application/json' -X POST --data @<filename.json>
```

この `<filename.json>` は、前の手順で準備したファイルの名前です。 各 VCL スニペットに対して、このコマンドを繰り返します。

を受け取った場合 `500 Internal Server Error` fastly サービスからの応答。JSON ファイル構文を調べ、有効なファイルをアップロードしていることを確認します。

### 手順 5：カスタム VCL スニペットの検証とアクティブ化

カスタム VCL スニペットを追加すると、編集中の VCL バージョンにスニペットが挿入されます。 変更を適用するには、次の手順を実行して VCL スニペット コードを検証し、VCL バージョンをアクティブにします。

1. Fastly API の使用 [vcl バージョンの検証](https://docs.fastly.com/api/config#version_97f8cf7bfd5dc2e5ea1933d94dc5a9a6) 更新された VCL コードを検証する操作。

   ```bash
   curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/$FASTLY_EDIT_VERSION/validate
   ```

   Fastly API がエラーを返した場合は、問題を修正し、更新された VCL バージョンを再度検証します。

1. Fastly API の使用 [アクティベート](https://docs.fastly.com/api/config#version_0b79ae1ba6aee61d64cc4d43fed1e0d5) 新しい VCL バージョンをアクティブにする操作。

   ```bash
   curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/$FASTLY_EDIT_VERSION/activate -X PUT
   ```

## VCL スニペットの API クイックリファレンス

これらの API リクエストの例では、書き出された環境変数を使用して、Fastly で認証するための資格情報を提供しています。 これらのコマンドの詳細については、を参照してください [Fastly API リファレンス](https://docs.fastly.com/api/config#vcl).

>[!NOTE]
>
>次のコマンドを使用して、Fastly API を使用して追加したスニペットを管理します。 管理者からスニペットを追加した場合は、 [管理を使用して VCL スニペットを管理する](#manage-vcl-using-the-api).

- **アクティブな VCL バージョン番号を取得**

  ```bash
  curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/active
  ```

- **サービスに接続されているすべての通常の VCL スニペットを一覧表示する**

  ```bash
  curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/$FASTLY_VERSION/snippet
  ```

- **個々のスニペットの確認**

  ```bash
  curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/$FASTLY_VERSION/snippet/<snippet_name>
  ```

  この `<snippet_name>` はスニペットの名前（例：） `my_regular_snippet`.

- **スニペットの更新**

  を変更する [準備された JSON ファイル](#step-3-create-a-custom-vcl-snippet) そして、次のリクエストを送信します。

  ```bash
  curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/$FASTLY_VERSION/snippet/<snippet_name> -H 'Content-Type: application/json' -X PUT --data @<filename.json>
  ```

- **個々の VCL スニペットを削除する**

  スニペットのリストを取得して、次を使用します `curl` 削除する特定のスニペット名を指定したコマンド：

  ```bash
  curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/$FASTLY_VERSION/snippet/<snippet_name> -X DELETE
  ```

- **の値のオーバーライド [デフォルトの Fastly VCL コード](https://github.com/fastly/fastly-magento2/tree/master/etc/vcl_snippets)**

  更新された値でスニペットを作成して、の優先度を割り当てます。 `100`.
