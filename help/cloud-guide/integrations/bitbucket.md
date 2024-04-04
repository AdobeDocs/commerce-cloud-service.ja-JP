---
title: ビットバケットの統合
description: Adobe Commerce on cloud infrastructure プロジェクトを Bitbucket と統合する方法を説明します。
feature: Cloud, Integration
exl-id: cd3cffbe-268f-429b-a2ea-0306159f4a6b
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '1020'
ht-degree: 0%

---

# ビットバケットの統合

コードの変更をプッシュする際に、環境を自動的に構築してデプロイするように Bitbucket リポジトリを設定できます。 この統合は、Bitbucket リポジトリをAdobe Commerce on クラウドインフラストラクチャアカウントと同期します。

{{private-repository}}

## 前提条件

- クラウドインフラストラクチャプロジェクト上のAdobe Commerceへの管理者アクセス権
- [`magento-cloud` CLI](../dev-tools/cloud-cli-overview.md) ローカル環境のツール
- Bitbucket アカウント
- Bitbucket リポジトリへの管理者アクセス権
- ビットバケットリポジトリの SSH アクセスキー

## リポジトリの準備

既存の環境からクラウドインフラストラクチャプロジェクト上のAdobe Commerceをクローンし、同じブランチ名を保持したまま、プロジェクトブランチを新しい空のビットバケットリポジトリに移行します。 それは **重要な** 同じ Git ツリーを保持して、Adobe Commerce on cloud infrastructure プロジェクト内の既存の環境やブランチが失われないようにします。

1. ターミナルから、クラウドインフラストラクチャプロジェクトのAdobe Commerceにログインします。

   ```bash
   magento-cloud login
   ```

1. プロジェクトを一覧表示し、プロジェクト ID をコピーします。

   ```bash
   magento-cloud project:list
   ```

1. プロジェクトをローカル環境に複製します。

   ```bash
   magento-cloud project:get <project-ID>
   ```

1. Bitbucket リポジトリをリモートとして追加します。

   ```bash
   git remote add origin git@bitbucket.org:<user-name>/<repo-name>.git
   ```

   リモート接続のデフォルト名は次のようになります。 `origin` または `magento`. 次の場合 `origin` が存在する場合は、別の名前を選択するか、既存の参照の名前を変更または削除することができます。 詳しくは、 [git-remote ドキュメント](https://git-scm.com/docs/git-remote).

1. Bitbucket リモートが正しく追加されたことを確認します。

   ```bash
   git remote -v
   ```

   期待される応答：

   ```terminal
   origin git@bitbucket.org:<user-name>/<repo-name>.git (fetch)
   origin git@bitbucket.org:<user-name>/<repo-name>.git (push)
   ```

1. プロジェクトファイルを新しい Bitbucket リポジトリにプッシュします。 すべてのブランチ名は同じにしてください。

   ```bash
   git push -u origin master
   ```

   新しい Bitbucket リポジトリを使用し始める場合は、 `-f` 」オプションを使用します。リモートリポジトリがローカルコピーと一致しないためです。

1. Bitbucket リポジトリに、すべてのプロジェクトファイルが含まれていることを確認します。

## OAuth 消費者の作成

Bitbucket の統合には、 [OAuth 消費者](https://support.atlassian.com/bitbucket-cloud/docs/use-oauth-on-bitbucket-cloud/). OAuth が必要です `key` および `secret` このコンシューマーから次の節に進みます。

**Bitbucket で OAuth コンシューマーを作成するには、以下を実行します。**:

1. にログインします。 [Bitbucket](https://id.atlassian.com/login) アカウント。

1. クリック **設定** > **アクセス管理** > **OAuth**.

1. クリック **消費者を追加** 次のように設定します。

   ![ビットバケット OAuth 消費者設定](../../assets/oauth-consumer-config.png)

   >[!WARNING]
   >
   >有効な **コールバック URL** は必須ではありませんが、統合を正常に完了するには、このフィールドに値を入力する必要があります。

1. クリック **保存**.

1. 消費者をクリック **名前** OAuth を表示するには `key` および `secret`.

1. OAuth をコピーします。 `key` および `secret` 統合を設定する場合。

## 統合の設定

1. ターミナルから、クラウドインフラストラクチャ上のAdobe Commerceプロジェクトに移動します。

1. という名前の一時ファイルを作成します。 `bitbucket.json` 次のコードを追加し、山括弧内の変数を値に置き換えます。

   ```json
   {
     "type": "bitbucket",
     "repository": "<bitbucket-user-name/bitbucket-repo-name>",
     "app_credentials": {
       "key": "<oauth-consumer-key>",
       "secret": "<oauth-consumer-secret>"
     },
     "prune_branches": true,
     "fetch_branches": true,
     "build_pull_requests": true,
     "resync_pull_requests": true
   }
   ```

   >[!TIP]
   >
   >URL ではなく、Bitbucket リポジトリの名前を使用してください。 URL を使用すると統合に失敗します。

1. を使用して、プロジェクトに統合を追加します。 `magento-cloud` CLI ツール。

   >[!WARNING]
   >
   >次のコマンドは、 _すべて_ Adobe Commerce on cloud infrastructure プロジェクトのコードと、Bitbucket リポジトリのコード。 これには、 `production` 分岐。 この操作は即座に実行され、元に戻すことはできません。 ベストプラクティスとしては、クラウドインフラストラクチャプロジェクト上のAdobe Commerceからすべてのブランチをクローンし、Bitbucket リポジトリにプッシュすることが重要です **前** bitbucket 統合の追加

   ```bash
   magento-cloud project:curl -p '<project-ID>' /integrations -i -X POST -d "$(< bitbucket.json)"
   ```

   これは、ヘッダー付きの長い HTTP 応答を返します。 統合が成功すると、ステータスコード 200 または 201 が返されます。 ステータスが 400 以上の場合は、エラーが発生したことを示します。

1. 一時的な `bitbucket.json` ファイル。

1. プロジェクトの統合を確認します。

   ```bash
   magento-cloud integrations -p <project-ID>
   ```

   ```terminal
   +----------+-----------+--------------------------------------------------------------------------------+
   | ID       | Type      | Summary                                                                        |
   +----------+-----------+--------------------------------------------------------------------------------+
   | <int-id> | bitbucket | Repository: bitbucket_Account/magento-int                                      |
   |          |           | Hook URL:                                                                      |
   |          |           | https://magento-url.cloud/api/projects/<project-id>/integrations/<int-id>/hook |
   +----------+-----------+--------------------------------------------------------------------------------+
   ```

   次の項目をメモします。 **フック URL** をクリックして、BitBucket で webhook を設定します。

### BitBucket にウェブフックを追加する

プッシュなどのイベントを Cloud Git サーバーと通信するには、BitBucket リポジトリの Webhook が必要です。 このページで詳しく説明している Bitbucket 統合の設定方法に従うと、Webhook が自動的に作成されます。 複数の統合を作成するのを避けるために、Webhook を検証することが重要です。

1. にログインします。 [Bitbucket](https://id.atlassian.com/login) アカウント。

1. クリック **リポジトリ** をクリックし、プロジェクトを選択します。

1. クリック **リポジトリ設定** > **ワークフロー** > **ウェブフック**.

1. 続行する前に、Webhook を確認します。

   フックがアクティブな場合は、残りの手順をスキップし、 [統合のテスト](#test-the-integration). フックの名前は、 **«クラウドインフラストラクチャ上のAdobe Commerce &lt;project_id>&quot;** また、次のようなフック URL 形式も設定できます。 `https://<zone>.magento.cloud/api/projects/<project_id>/integrations/<id>/hook`

1. クリック **ウェブフックを追加**.

1. Adobe Analytics の _新しいウェブフックを追加_ 次のフィールドを表示、編集します。

   - **タイトル**:Adobe Commerce統合
   - **URL**: `magento-cloud` 統合リスト
   - **トリガー**：デフォルトは基本です。 _リポジトリのプッシュ_

1. クリック **保存**.

### 統合のテスト

Bitbucket 統合を設定した後は、 `magento-cloud` CLI:

```bash
magento-cloud integration:validate
```

または、単純な変更をビットバケットリポジトリにプッシュしてテストできます。

1. テストファイルを作成します。

   ```bash
   touch test.md
   ```

1. 変更をコミットして、ビットバケットリポジトリにプッシュします。

   ```bash
   git add . && git commit -m "Testing Bitbucket integration" && git push
   ```

1. にログインします。 [[!DNL Cloud Console]](../project/overview.md) コミットメッセージが表示され、プロジェクトが展開されていることを確認します。

   ![Bitbucket 統合のテスト](../../assets/bitbucket-integration.png)

## クラウドブランチの作成

Bitbucket 統合は、クラウドインフラストラクチャプロジェクト上のAdobe Commerceの新しい環境をアクティブ化できません。 Bitbucket を使用して環境を作成する場合は、手動で環境をアクティブ化する必要があります。 この余分な手順を避けるには、 `magento-cloud` CLI ツールまたは [!DNL Cloud Console].

**ビットバケットで作成したブランチをアクティブ化するには**:

1. 以下を使用します。 `magento-cloud` ブランチをプッシュする CLI。

   ```bash
   magento-cloud environment:push from-bitbucket
   ```

   ```terminal
   Pushing from-bitbucket to the new environment from-bitbucket
   Activate from-bitbucket after pushing? [Y/n] y
   Parent environment [master]: integration
   --- (Validation and activation messages)
   ```

1. 環境がアクティブであることを確認します。

   ```bash
   magento-cloud environment:list
   ```

   ```terminal
   Your environments are:
   +---------------------+----------------+--------+
   | ID                  | Name           | Status |
   +---------------------+----------------+--------+
   | master              | Master         | Active |
   |  integration        | integration    | Active |
   |    from-bitbucket * | from-bitbucket | Active |
   +---------------------+----------------+--------+
   * - Indicates the current environment
   ```

環境を作成したら、通常の Git コマンドを使用して、対応するブランチをリモートの Bitbucket リポジトリにプッシュできます。 その後、Bitbucket のブランチを変更すると、環境が自動的に作成されてデプロイされます。

## 統合の削除

コードに影響を与えることなく、Bitbucket 統合をプロジェクトから安全に削除できます。

**Bitbucket 統合を削除するには、以下を実行します。**:

1. ターミナルから、クラウドインフラストラクチャプロジェクトのAdobe Commerceにログインします。

1. 統合の一覧を表示します。 次の手順を完了するには、Bitbucket 統合 ID が必要です。

   ```bash
   magento-cloud integration:list
   ```

1. 統合を削除します。

   ```bash
   magento-cloud integration:delete <int-ID>
   ```

また、Bitbucket の統合を削除するには、Bitbucket アカウントにログインし、アカウントに対する OAuth 付与を取り消します _設定_ ページに貼り付けます。

## Bitbucket サーバーの統合

Bitbucket サーバーの統合を使用するには、次の情報が必要です。

- [ビットバケットアクセストークン](https://confluence.atlassian.com/bitbucketserver/http-access-tokens-939515499.html) — プロジェクトを許可するトークンを生成します `read` アクセスとリポジトリ `admin` アクセス
- [ビットバケットサーバー URL](https://confluence.atlassian.com/bitbucketserver/specify-the-bitbucket-base-url-776640392.html)— Bitbucket インスタンスのベース URL を追加します。

Cloud CLI を使用して Bitbucket サーバーの統合手順を実行できますが、full コマンドは次のようになります。

```bash
magento-cloud integration:add --type=bitbucket_server --base-url=<bitbucket-url> --username=<username> --token=<bitbucket-access-token> --project=<project-ID>
```

その他の使用要件やオプションについては、ヘルプコマンドを使用します。 `magento-cloud integration:add --help`
