---
title: Bitbucket の統合
description: クラウドインフラストラクチャプロジェクト上のAdobe Commerceを Bitbucket と統合する方法を説明します。
feature: Cloud, Integration
exl-id: cd3cffbe-268f-429b-a2ea-0306159f4a6b
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '1020'
ht-degree: 0%

---

# Bitbucket の統合

コードの変更をプッシュする際に、環境を自動的にビルドおよびデプロイするように、Bitbucket リポジトリーを設定できます。 この統合により、Bitbucket リポジトリがクラウドインフラストラクチャアカウント上のAdobe Commerceと同期されます。

{{private-repository}}

## 前提条件

- Adobe Commerce on cloud infrastructure プロジェクトへの管理者アクセス
- [`magento-cloud` CLI](../dev-tools/cloud-cli-overview.md) ローカル環境のツール
- Bitbucket アカウント
- Bitbucket リポジトリへの管理者アクセス
- Bitbucket リポジトリの SSH アクセスキー

## リポジトリを準備

既存の環境からクラウドインフラストラクチャプロジェクト上にAdobe Commerceのクローンを作成し、同じブランチ名を保持したまま、プロジェクトのブランチを新しい空の Bitbucket リポジトリに移行します。 このプロパティは **重大** Adobe Commerce on cloud infrastructure プロジェクトで既存の環境やブランチが失われないように、同一の Git ツリーを保持する。

1. ターミナルから、クラウドインフラストラクチャプロジェクトのAdobe Commerceにログインします。

   ```bash
   magento-cloud login
   ```

1. プロジェクトをリストして、プロジェクト ID をコピーします。

   ```bash
   magento-cloud project:list
   ```

1. プロジェクトのクローンをローカル環境に作成します。

   ```bash
   magento-cloud project:get <project-ID>
   ```

1. Bitbucket リポジトリをリモートとして追加します。

   ```bash
   git remote add origin git@bitbucket.org:<user-name>/<repo-name>.git
   ```

   リモート接続のデフォルト名は以下のようになります。 `origin` または `magento`. 次の場合 `origin` 存在する、別の名前を選択できる、または既存の参照の名前を変更または削除できる。 参照： [git-remote ドキュメント](https://git-scm.com/docs/git-remote).

1. Bitbucket リモートを正しく追加したことを確認します。

   ```bash
   git remote -v
   ```

   期待される応答：

   ```terminal
   origin git@bitbucket.org:<user-name>/<repo-name>.git (fetch)
   origin git@bitbucket.org:<user-name>/<repo-name>.git (push)
   ```

1. プロジェクトファイルを新しい Bitbucket リポジトリにプッシュします。 すべてのブランチ名は同じにしておくことを忘れないでください。

   ```bash
   git push -u origin master
   ```

   新しい Bitbucket リポジトリで始める場合は、を使用する必要があります。 `-f` リモートリポジトリがローカルコピーと一致しないためです。

1. Bitbucket リポジトリに、すべてのプロジェクトファイルが含まれていることを確認します。

## OAuth コンシューマーの作成

Bitbucket の統合には、が必要です [OAuth コンシューマー](https://support.atlassian.com/bitbucket-cloud/docs/use-oauth-on-bitbucket-cloud/). Oauth が必要です `key` および `secret` このコンシューマーから次の節を完了します。

**Bitbucket で OAuth コンシューマーを作成するには：**:

1. にログイン [Bitbucket](https://id.atlassian.com/login) アカウント。

1. クリック **設定** > **アクセス管理** > **OAuth**.

1. クリック **消費者を追加** そして、次のように設定します。

   ![Bitbucket OAuth コンシューマー設定](../../assets/oauth-consumer-config.png)

   >[!WARNING]
   >
   >有効な **コールバック URL** 必須ではありませんが、統合を正常に完了するには、このフィールドに値を入力する必要があります。

1. クリック **保存**.

1. 消費者をクリックします **名前** OAuth を表示するには `key` および `secret`.

1. OAuth をコピー `key` および `secret` ：統合の設定

## 統合の設定

1. ターミナルから、クラウドインフラストラクチャプロジェクトのAdobe Commerceに移動します。

1. という一時ファイルを作成します。 `bitbucket.json` 次のコードを追加し、山括弧で囲まれた変数を、必要な値に置き換えます。

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
   >URL ではなく、Bitbucket リポジトリの名前を使用してください。 URL を使用すると、統合が失敗します。

1. を使用して、統合をプロジェクトに追加する `magento-cloud` CLI ツール。

   >[!WARNING]
   >
   >次のコマンドは上書きします _all_ Adobe Commerce on cloud infrastructure プロジェクトのコードを、Bitbucket リポジトリーのコードと共に作成します。 これには、を含むすべてのブランチが含まれます `production` 分岐。 このアクションは即座に実行され、元に戻すことはできません。 ベストプラクティスとして、クラウドインフラストラクチャプロジェクト上のAdobe Commerceからすべてのブランチをクローンし、Bitbucket リポジトリにプッシュすることが重要です **次の前** bitbucket 統合の追加

   ```bash
   magento-cloud project:curl -p '<project-ID>' /integrations -i -X POST -d "$(< bitbucket.json)"
   ```

   これは、ヘッダー付きの長い HTTP 応答を返します。 統合が成功すると、200 または 201 ステータスコードが返されます。 ステータスが 400 以上の場合は、エラーが発生したことを示します。

1. 一時を削除 `bitbucket.json` ファイル。

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

   をメモします **フック URL** bitbucket で Webhook を設定するには、次の手順に従います。

### BitBucket への Webhook の追加

プッシュなどのイベントを Cloud Git サーバーと通信するには、BitBucket リポジトリに Webhook が必要です。 このページで詳しく説明している Bitbucket 統合の設定方法に従うと、自動的に Webhook が作成されます。 複数の統合を作成しないようにするために、Webhook を検証することが重要です。

1. にログイン [Bitbucket](https://id.atlassian.com/login) アカウント。

1. クリック **リポジトリ** プロジェクトを選択します。

1. クリック **リポジトリ設定** > **ワークフロー** > **Webhook**.

1. 続行する前に Webhook を確認します。

   フックがアクティブな場合は、残りの手順をスキップし、 [統合のテスト](#test-the-integration). フックの名前はのようになります。 **クラウドインフラストラクチャー上のAdobe Commerce &lt;project_id>“** および次のようなフック URL 形式： `https://<zone>.magento.cloud/api/projects/<project_id>/integrations/<id>/hook`

1. クリック **Webhook を追加**.

1. が含まれる _新しい Webhook を追加_ 表示して、次のフィールドを編集します。

   - **タイトル**:Adobe Commerceの統合
   - **URL**：からフック URL を使用します `magento-cloud` 統合リスト
   - **トリガー**：デフォルトは基本です _リポジトリのプッシュ_

1. クリック **保存**.

### 統合のテスト

Bitbucket 統合を設定したら、統合が次を使用して動作していることを確認できます。 `magento-cloud` CLI:

```bash
magento-cloud integration:validate
```

または、単純な変更を Bitbucket リポジトリにプッシュしてテストすることもできます。

1. テストファイルを作成します。

   ```bash
   touch test.md
   ```

1. 変更をコミットし、Bitbucket リポジトリにプッシュします。

   ```bash
   git add . && git commit -m "Testing Bitbucket integration" && git push
   ```

1. にログインします [[!DNL Cloud Console]](../project/overview.md) コミットメッセージが表示され、プロジェクトがデプロイされていることを確認します。

   ![Bitbucket 統合のテスト](../../assets/bitbucket-integration.png)

## クラウドブランチの作成

Bitbucket 統合では、クラウドインフラストラクチャプロジェクト上のAdobe Commerceで新しい環境をアクティブ化できません。 Bitbucket を使用して環境を作成する場合は、手動で環境をアクティブ化する必要があります。 このような追加の手順を回避するには、を使用して環境を作成することがベストプラクティスです。 `magento-cloud` CLI ツールまたは [!DNL Cloud Console].

**Bitbucket で作成されたブランチをアクティブにするには**:

1. の使用 `magento-cloud` CLI を使用して分岐をプッシュします。

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

環境を作成したら、通常の Git コマンドを使用して、対応するブランチをリモート Bitbucket リポジトリにプッシュできます。 その後、Bitbucket のブランチに変更を加えると、環境が自動的にビルドおよびデプロイされます。

## 統合の削除

コードに影響を与えることなく、プロジェクトから Bitbucket 統合を安全に削除できます。

**Bitbucket 統合を削除するには**:

1. ターミナルから、クラウドインフラストラクチャプロジェクトのAdobe Commerceにログインします。

1. 統合のリストを作成します。 次の手順を完了するには、Bitbucket 統合 ID が必要です。

   ```bash
   magento-cloud integration:list
   ```

1. 統合を削除します。

   ```bash
   magento-cloud integration:delete <int-ID>
   ```

また、Bitbucket アカウントにログインし、アカウントの OAuth 付与を取り消すことで、Bitbucket の統合を削除できます _設定_ ページ。

## Bitbucket サーバーの統合

Bitbucket サーバー統合を使用するには、以下が必要です。

- [ビットバケットアクセストークン](https://confluence.atlassian.com/bitbucketserver/http-access-tokens-939515499.html)- プロジェクトを許可するトークンを生成します `read` アクセスとリポジトリ `admin` アクセス
- [Bitbucket サーバー URL](https://confluence.atlassian.com/bitbucketserver/specify-the-bitbucket-base-url-776640392.html)— Bitbucket インスタンスのベース URL を追加します。

Cloud CLI を使用して Bitbucket サーバーの統合手順を実行することもできますが、完全なコマンドは次のようになります。

```bash
magento-cloud integration:add --type=bitbucket_server --base-url=<bitbucket-url> --username=<username> --token=<bitbucket-access-token> --project=<project-ID>
```

その他の使用要件やオプションについては、help コマンドを使用してください。 `magento-cloud integration:add --help`
