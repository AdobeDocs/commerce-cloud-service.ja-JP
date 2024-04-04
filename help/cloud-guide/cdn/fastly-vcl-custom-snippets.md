---
title: カスタム VCL スニペットの基本を学ぶ
description: Vanish Control Language コードスニペットを使用して、Adobe Commerceの Fastly サービス設定をカスタマイズする方法を説明します。
feature: Cloud, Configuration, Services
exl-id: df0f0906-8ffa-41a1-a31c-d36deb5a6a31
source-git-commit: 89c57a486545d6165e0407f913f4fa4cf6c95abe
workflow-type: tm+mt
source-wordcount: '1947'
ht-degree: 0%

---

# カスタム VCL の概要

Fastly は、Vanish Configuration Language(VCL) のカスタマイズバージョンをサポートし、Fastly のサービス設定を要件に合わせて調整します。

カスタム VCL スニペットは、Adobe Commerceサイトにアップロードされるアクティブな VCL バージョンに追加される VCL ロジックのブロックです。 カスタム VCL スニペットによって、リクエストトラフィックに対する Fastly キャッシュサービスの応答方法が変更されます。 例えば、指定したクライアント IP アドレスからのみトラフィックをリクエストできるように、カスタム VCL スニペットを追加できます。 または、紹介スパムをAdobe Commerceサイトに送信することがわかっている Web サイトからのトラフィックをブロックするスニペットを作成します。

カスタム VCL スニペット（生成、コンパイル、およびすべての Fastly キャッシュに転送）。サーバのダウンタイムなしで読み込み、アクティブ化します。

>[!NOTE]
>
>カスタム VCL コード、エッジ辞書、ACL を Fastly モジュール設定に追加する前に、Fastly キャッシュサービスがデフォルト設定で動作することを確認します。 詳しくは、 [Fastly サービスの設定](fastly-configuration.md).

Fastly は、次の 2 種類のカスタム VCL スニペットをサポートしています。

- [通常のスニペット](https://docs.fastly.com/en/guides/about-vcl-snippets) — 特定の VCL バージョン用に、カスタムの通常の VCL スニペットがコード化されます。 通常の VCL スニペットは、Admin または Fastly API から作成、変更およびデプロイできます。

- [動的スニペット](https://docs.fastly.com/en/guides/using-dynamic-vcl-snippets)— Fastly API を使用して作成された VCL スニペットです。 サービスの Fastly VCL バージョンを更新しなくても、ダイナミックスニペットを変更およびデプロイできます。

エッジ辞書およびアクセス制御リスト (ACL) と共にカスタム VCL スニペットを使用して、カスタムコードで使用するデータを保存することをお勧めします。

- [**エッジ辞書**](https://docs.fastly.com/guides/edge-dictionaries/about-edge-dictionaries) — カスタム VCL スニペットから参照可能なディクショナリコンテナに、データをキー値ペアとして格納します。

- [**Edge ACL**](https://docs.fastly.com/guides/access-control-lists/about-acls) — ブロックまたはカスタム VCL スニペットを使用して実装されたルールのアクセス制御リストを定義するクライアント IP アドレスデータを格納します。

ディクショナリと ACL データは、ネットワーク地域全体でアクセス可能な Fastly Edge ノードにデプロイされます。 また、ステージング環境や実稼動環境に VCL コードを再デプロイする必要なく、データをネットワーク全体で動的に更新できます。

>[!NOTE]
>
>ステージング環境または実稼動環境に追加できるのは、カスタム VCL スニペットのみです。 [設定された Fastly サービス](fastly-configuration.md) その環境に対して。

## チュートリアル

このチュートリアルと例では、Edge Dictionaries と Edge ACL を使用して、Adobe Commerceの Fastly サービス設定をカスタマイズする通常のカスタム VCL スニペットの使用方法を示します。 詳しくは、以下の Fastly ドキュメントを参照してください。

- [Fastly VCL のガイド](https://docs.fastly.com/guides/vcl/guide-to-vcl)- Fastly Vanish の実装、Fastly VCL の拡張、および Vanish と VCL の詳細を学ぶためのリソースに関する情報。
- [VCL リファレンスの失敗](https://docs.fastly.com/guides/vcl/)- Fastly カスタム VCL およびカスタム VCL スニペットの開発とトラブルシューティングに関する詳細なプログラミングリファレンス。

カスタム VCL スニペットの作成と管理は、Adobe Commerce管理者から、または Fastly API を使用しておこなうことができます。

- [Adobe Commerce Admin](#manage-custom-vcl-from-admin)—VCL の変更を Fastly サービス設定に検証、アップロード、適用するプロセスを自動化できるので、Adobe Commerce管理者を使用してカスタム VCL スニペットを管理することをお勧めします。 また、Fastly サービス設定に追加されたカスタム VCL スニペットを管理者から表示および編集することもできます。

- [Fastly API](#manage-vcl-using-the-api) — 管理者にアクセスできない場合は、Fastly API を使用してカスタム VCL スニペットを管理します。 例えば、API を使用して、サイトがダウンしたときに Fastly サービス設定のトラブルシューティングをおこなったり、カスタム VCL スニペットを追加したりします。 また、一部の操作は API を使用してのみ完了できます。 例えば、API を使用して古い VCL バージョンを再アクティブ化したり、指定した VCL バージョンに含まれるすべての VCL スニペットを表示したりする必要があります。 詳しくは、 [VCL スニペットの API クイックリファレンス](#api-quick-reference-for-vcl-snippets).

### VCL スニペットコードの例

次の例は、クライアント IP アドレスでトラフィックをフィルタリングするカスタム VCL スニペット（JSON 形式）を示しています。

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
>この例では、VCL コードは JSON ペイロードとしてフォーマットされ、ファイルに保存して Fastly API リクエストで送信できます。 スニペットを API リクエストの JSON として送信する際に JSON 検証エラーを防ぐには、バックスラッシュを使用してコード内の特殊文字をエスケープします。 詳しくは、 [動的 VCL スニペットの使用](https://docs.fastly.com/vcl/vcl-snippets/) Fastly VCL のドキュメントの 管理者から VCL スニペットを送信する場合は、特殊文字をエスケープする必要はありません。

の VCL ロジック `content` フィールドは、次の操作を実行します。

- 受信 IP アドレスをチェックし、 `client.ip` リクエストごとに

- IP アドレスが *ACLNAME* エッジ ACL、を返す `403 Forbidden` エラー

次の表に、カスタム VCL スニペットの主要データの詳細を示します。 詳しくは、 [VCL スニペット](https://docs.fastly.com/api/config#api-section-snippet) Fastly ドキュメントのを参照してください。

| 値 | 説明 |
|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `API_KEY` | Fastly アカウントにアクセスするための API キー。 詳しくは、 [認証情報の取得](fastly-configuration.md). |
| `active` | スニペットまたはバージョンのアクティブステータス。 戻り値 `true` または `false`. true の場合、スニペットまたはバージョンは使用中です。 アクティブなスニペットのバージョン番号を使用して複製します。 |
| `content` | 実行する VCL コードのスニペット。 Fastly は、すべての VCL 言語機能をサポートしているわけではありません。 また、Fastly は拡張機能にカスタム機能を提供します。 サポートされる機能について詳しくは、 [Fastly VCL プログラミングリファレンス](https://docs.fastly.com/vcl/reference/). |
| `dynamic` | スニペットの動的ステータス。 戻り値 `false` 対象： [定期的なスニペット](https://docs.fastly.com/en/guides/about-vcl-snippets) Fastly サービス設定用のバージョン管理された VCL に含まれています。 戻り値 `true` の [動的スニペット](https://docs.fastly.com/vcl/vcl-snippets/using-dynamic-vcl-snippets/) 新しい VCL バージョンを必要とせずに、変更やデプロイが可能な |
| `number` | スニペットが含まれる VCL バージョン番号。 Fastly の使用方法 *編集可能なバージョン番号* を使用します。 API からカスタムスニペットを追加する場合は、API リクエストにバージョン番号を含めます。 管理者からカスタム VCL を追加した場合は、バージョンが提供されます。 |
| `priority` | 次の値を持つ数値： `1` から `100` カスタム VCL スニペットコードを実行するタイミングを指定します。 優先度の低いスニペットを最初に実行します。 指定しない場合、 `priority` 値のデフォルト値： `100`.<p>優先度の値がのカスタム VCL スニペット `5` は即座に実行されます。これは、要求ルーティング（ブロックとリダイレクト）を実装する VCL コードに最適で許可リストに加えるす。 優先度 `100` は、デフォルトの VCL スニペットコードを上書きするのに最適です。<p>すべて [デフォルトの VCL スニペット](fastly-configuration.md#upload-vcl-snippets) Fastly モジュールに含まれるMagento-Fastly モジュールは、 `priority=50`.<ul><li>高い優先度を割り当てる（例： ） `100` 他のすべての VCL 関数の後にカスタム VCL コードを実行し、デフォルトの VCL コードを上書きする場合。</li></ul> |
| `service_id` | 特定のステージング環境または実稼動環境の Fastly サービス ID。 この ID は、プロジェクトがクラウドインフラストラクチャ上のAdobe Commerceに追加される際に割り当てられます [Fastly サービスアカウント](fastly.md#fastly-service-account-and-credentials). |
| `type` | 生成されたスニペットを挿入する場所を指定します ( 例： `init` （サブルーチン以上）および `recv` （サブルーチン内）。 詳しくは、 Fastly [VCL スニペット](https://docs.fastly.com/api/config#api-section-snippet) 参照。 |

## 管理者からカスタム VCL を管理

以下が可能です。 [カスタム VCL スニペットを追加](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/CUSTOM-VCL-SNIPPETS.md) から *Fastly 設定* > *カスタム VCL スニペット* 」セクションをクリックします。

![カスタム VCL スニペットの管理](../../assets/cdn/fastly-edit-snippets.png)

The *カスタム VCL スニペット* 表示には、管理者から追加されたスニペットのみが表示されます。 Fastly API を使用してスニペットを追加する場合は、API を使用して [管理者](#manage-vcl-using-the-api).

以下の例は、管理者からカスタム VCL スニペットを作成および管理する方法、および Fastly Edge モジュールと Edge 辞書を使用する方法を示しています。

- [CMS バックエンドにリクエストを再ルーティングする](fastly-vcl-wordpress.md)
- [リファラルスパムをブロック](fastly-vcl-badreferer.md)
- [紹介スパムをブロック](fastly-vcl-badreferer.md)
- [IP のカスタム VCL 許可リストに加える](fastly-vcl-allowlist.md)
- [IP のカスタム VCL ブロックリストに加える](fastly-vcl-blocking.md)
- [Fastly キャッシュをバイパス](fastly-vcl-bypass-to-origin.md)

## API を使用して VCL を管理

以下のウォークスルーは、通常の VCL スニペットファイルを作成し、Fastly API を使用して Fastly サービス設定に追加する方法を示しています。 スニペットは、 *端末* アプリケーション。 特定の環境への SSH 接続は必要ありません。

**前提条件：**

- Fastly サービス用に、クラウドインフラストラクチャ環境上のAdobe Commerceを設定します。 詳しくは、 [Fastly の設定](fastly-configuration.md).

- [Fastly API の資格情報の取得](fastly-configuration.md) を使用して、Fastly API へのリクエストを認証します。 正しい環境（ステージングまたは実稼動）の資格情報が取得されていることを確認します。

- Fastly サービス資格情報を bash 環境変数として保存し、cURL コマンドで使用できます。

  ```bash
  export FASTLY_SERVICE_ID=<Service-ID>
  ```

  ```bash
  export FASTLY_API_TOKEN=<API-Token>
  ```

  書き出された環境変数は、現在の bash セッションでのみ使用でき、端末を閉じると失われます。 新しい値を書き出すことで、変数を再定義できます。 Fastly に関連するエクスポートされた変数のリストを表示するには：

  ```bash
  export | grep FASTLY
  ```

## VCL スニペットを追加

このチュートリアルでは、Fastly API を使用してカスタムスニペットを追加する基本的な手順を説明します。

>[!NOTE]
>
>Adobe Commerce Admin からカスタム VCL スニペットを管理する方法については、 [Adobe Commerce Admin から VCL を管理](#manage-custom-vcl-from-admin).


**前提条件**

{{$include /help/_includes/vcl-snippet-prerequisites.md}}

### 手順 1：アクティブな VCL バージョンを見つける

Fastly API の使用 [バージョンを取得](https://docs.fastly.com/api/config#version_dfde9093f4eb0aa2497bbfd1d9415987) アクティブな VCL バージョン番号を取得する操作：

```bash
curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/active
```

JSON 応答で、 `number` キー（例： ） `"number": 99`. VCL を編集用に複製する場合は、バージョン番号が必要です。

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

### 手順 2：アクティブな VCL バージョンとすべてのスニペットのクローンを作成する

カスタム VCL スニペットを追加または変更する前に、編集用にアクティブな VCL バージョンのコピーを作成する必要があります。 Fastly API の使用 [複製](https://docs.fastly.com/api/config#version_7f4937d0663a27fbb765820d4c76c709) 操作：

```bash
curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/$FASTLY_VERSION_ACTIVE/clone -X PUT
```

JSON 応答では、バージョン番号が増加し、 *アクティブ* キーの値は次のとおりです。 `false`. 非アクティブな新しい VCL バージョンをローカルで変更できます。

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

新しいバージョン番号を bash 環境変数に保存して、後続のコマンドで使用できるようにします。

```bash
export FASTLY_EDIT_VERSION=<Version>
```

### 手順 3：カスタム VCL スニペットを作成する

カスタム VCL コードを作成し、次の内容と形式を持つ JSON ファイルに保存します。

```json
{
  "name": "<name>",
  "dynamic": "0",
  "type": "<type>",
  "priority": "100",
  "content": "<code all in one line>"
}
```

次の値が含まれます。

- `name`- VCL スニペットの名前。

- `dynamic` — これが [通常のスニペット](https://docs.fastly.com/en/guides/about-vcl-snippets) または [動的スニペット](https://docs.fastly.com/guides/vcl-snippets/using-dynamic-vcl-snippets).

- `type` — 生成されたスニペットを挿入する場所を指定します。次に例を示します。 `init` （サブルーチン以上）および `recv` （サブルーチン内）。 詳しくは、 [VCL スニペットオブジェクト値](https://docs.fastly.com/api/config#snippet) を参照してください。

- `priority` — 値を `1` から `100` カスタム VCL スニペットコードを実行するタイミングを決定する 値が小さいカスタム VCL スニペットを最初に実行します。

  Fastly VCL モジュールのすべてのデフォルト VCL コードには、 `priority` / `50`. アクションを最後に実行する場合や、デフォルトの VCL コードを上書きする場合は、次のように大きい数値を使用します。 `100`. カスタム VCL スニペットコードを直ちに実行するには、優先度を次のように低い値に設定します。 `5`.

- `content`- 1 行で実行する VCL コードのスニペット（改行なし）。 詳しくは、 [カスタム VCL スニペットの例](#example-vcl-snippet-code).

### 手順 4:VCL スニペットを Fastly 設定に追加する

Fastly API の使用 [スニペットを作成](https://docs.fastly.com/api/config#snippet_41e0e11c662d4d56adada215e707f30d) 操作を使用してカスタム VCL スニペットを VCL バージョンに追加します。

```bash
curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/$FASTLY_EDIT_VERSION/snippet -H 'Content-Type: application/json' -X POST --data @<filename.json>
```

The `<filename.json>` は、前の手順で準備したファイルの名前です。 各 VCL スニペットに対してこのコマンドを繰り返します。

以下を受け取った場合、 `500 Internal Server Error` Fastly サービスからの応答で、JSON ファイルの構文を確認して、有効なファイルをアップロードしていることを確認します。

### 手順 5：カスタム VCL スニペットを検証してアクティブ化する

カスタム VCL スニペットを追加した後、Fastly は編集中の VCL バージョンにスニペットを挿入します。 変更を適用するには、次の手順を実行して VCL スニペットコードを検証し、VCL バージョンをアクティベートします。

1. Fastly API の使用 [VCL バージョンを検証](https://docs.fastly.com/api/config#version_97f8cf7bfd5dc2e5ea1933d94dc5a9a6) 操作を使用して、更新された VCL コードを検証します。

   ```bash
   curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/$FASTLY_EDIT_VERSION/validate
   ```

   Fastly API がエラーを返した場合は、問題を修正し、更新された VCL バージョンを再検証してください。

1. Fastly API の使用 [アクティブ化](https://docs.fastly.com/api/config#version_0b79ae1ba6aee61d64cc4d43fed1e0d5) 新しい VCL バージョンを有効化する操作。

   ```bash
   curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/$FASTLY_EDIT_VERSION/activate -X PUT
   ```

## VCL スニペットの API クイックリファレンス

以下の API リクエストの例では、書き出された環境変数を使用して、Fastly で認証する資格情報を提供します。 これらのコマンドの詳細については、 [Fastly API リファレンス](https://docs.fastly.com/api/config#vcl).

>[!NOTE]
>
>Fastly API を使用して追加したスニペットを管理するには、次のコマンドを使用します。 管理者からスニペットを追加した場合は、 [管理者を使用した VCL スニペットの管理](#manage-vcl-using-the-api).

- **アクティブな VCL バージョン番号を取得します**

  ```bash
  curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/active
  ```

- **サービスに接続されているすべての通常の VCL スニペットのリスト**

  ```bash
  curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/$FASTLY_VERSION/snippet
  ```

- **個々のスニペットの確認**

  ```bash
  curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/$FASTLY_VERSION/snippet/<snippet_name>
  ```

  The `<snippet_name>` はスニペットの名前です。例： `my_regular_snippet`.

- **スニペットの更新**

  を変更します。 [準備済みの JSON ファイル](#step-3-create-a-custom-vcl-snippet) 次のリクエストを送信します。

  ```bash
  curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/$FASTLY_VERSION/snippet/<snippet_name> -H 'Content-Type: application/json' -X PUT --data @<filename.json>
  ```

- **個々の VCL スニペットを削除する**

  スニペットのリストを取得し、次を使用します `curl` コマンドに、削除する特定のスニペット名を指定します。

  ```bash
  curl -H "Fastly-Key: $FASTLY_API_TOKEN" https://api.fastly.com/service/$FASTLY_SERVICE_ID/version/$FASTLY_VERSION/snippet/<snippet_name> -X DELETE
  ```

- **次の項目の値を上書き： [デフォルトの Fastly VCL コード](https://github.com/fastly/fastly-magento2/tree/master/etc/vcl_snippets)**

  更新された値を持つスニペットを作成し、優先度を `100`.
