---
title: GitLab 統合
description: クラウドインフラストラクチャプロジェクト上のAdobe Commerceを GitLab と統合する方法について説明します。
feature: Cloud, Integration
exl-id: 37fda8a0-7274-422f-9049-243f2e409f26
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '777'
ht-degree: 0%

---

# GitLab 統合

コードの変更をプッシュする際に、環境を自動的に構築してデプロイするように GitLab リポジトリを設定できます。 この統合により、GitLab リポジトリとAdobe Commerce on cloud infrastructure アカウントが同期されます。

{{private-repository}}

この統合により、次のことが可能になります。

- ブランチの作成時に環境を作成する
- プルリクエストを結合する際に環境を再デプロイする
- 分岐を削除する際に環境を削除する

処理を続行するには、GitLab トークンと Webhook を取得する必要があります。

## 前提条件

- クラウドインフラストラクチャプロジェクト上のAdobe Commerceへの管理者アクセス権
- [`magento-cloud` CLI](../dev-tools/cloud-cli-overview.md) ローカル環境のツール
- GitLab アカウント
- GitLab リポジトリへの書き込みアクセス権を持つ GitLab 個人用アクセストークン。選択したスコープは、少なくとも次の値である必要があります。 `api` および `read_repository`.

## リポジトリの準備

既存の環境からクラウドインフラストラクチャプロジェクト上のAdobe Commerceを複製し、同じブランチ名を保持したまま、プロジェクトブランチを新しい空の GitLab リポジトリに移行します。 それは **重要な** 同じ Git ツリーを保持して、Adobe Commerce on cloud infrastructure プロジェクト内の既存の環境やブランチが失われないようにします。

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
   magento-cloud project:get <project-id>
   ```

1. GitLab リポジトリをリモートリポジトリとして追加します（GitLab が SaaS バージョンで使用されていると仮定）。

   ```bash
   git remote add origin git@gitlab.com:<user-name>/<repo-name>.git
   ```

   リモート接続のデフォルト名は次のようになります。 `origin` または `magento`. 次の場合 `origin` が存在する場合は、別の名前を選択するか、既存の参照の名前を変更または削除することができます。 詳しくは、 [git-remote ドキュメント](https://git-scm.com/docs/git-remote).

1. GitLab リモートが正しく追加されたことを確認します。

   ```bash
   git remote -v
   ```

   期待される応答：

   ```terminal
   origin git@gitlab.com:<user-name>/<repo-name>.git (fetch)
   origin git@gitlab.com:<user-name>/<repo-name>.git (push)
   ```

1. プロジェクトファイルを新しい GitLab リポジトリにプッシュします。 すべてのブランチ名は同じにしてください。

   ```bash
   git push -u origin master
   ```

   新しい GitLab リポジトリを使用し始める場合は、 `-f` 」オプションを使用します。リモートリポジトリがローカルコピーと一致しないためです。

1. GitLab リポジトリに、すべてのプロジェクトファイルが含まれていることを確認します。

## GitLab 統合の有効化

以下を使用します。 `magento-cloud integration` コマンドを使用して GitLab 統合を有効にし、GitLab Webhook のペイロード URL を取得して、GitLab からクラウドインフラストラクチャプロジェクト上のAdobe Commerceに更新を送信するようにします。

```bash
magento-cloud integration:add --type=gitlab --project=<project-ID> --token=<your-GitLab-token> [--base-url=<GitLab-url> --server-project=<GitLab-project> --build-merge-requests={true|false} --merge-requests-clone-parent-data={true|false} --fetch-branches={true|false} --prune-branches={true|false}]
```

| オプション | 説明 |
| ------ | ----------- |
| `<project-ID>` | Adobe Commerce on cloud infrastructure プロジェクト ID |
| `<your-GitLab-token>` | GitLab 用に生成した個人用アクセストークン |
| `--base-url` | GitLab の URL (`https://gitlab.com/` （GitLab が SaaS バージョンで使用されている場合） |
| `--server-project` | GitLab のプロジェクト名（ベース URL の後の部分） |
| `--build-merge-requests` | An _オプション_ 結合リクエスト (`true` （デフォルト） |
| `--merge-requests-clone-parent-data` | An _オプション_ 結合リクエスト (`true` （デフォルト） |
| `--fetch-branches` | An _オプション_ クラウドインフラストラクチャ上のAdobe Commerceが（非アクティブな環境として）リモートからすべてのブランチを取得する原因となるパラメーター (`true` （デフォルト） |
| `--prune-branches` | An _オプション_ クラウドインフラストラクチャ上のAdobe Commerceに、リモート (`true` （デフォルト） |

>[!WARNING]
>
>The `magento-cloud integration` コマンドを上書き _すべて_ Adobe Commerce on cloud infrastructure プロジェクトのコードと、GitLab リポジトリのコード。 これには、 `production` 分岐。 この操作は即座に実行され、元に戻すことはできません。 ベストプラクティスとしては、GitLab 統合を追加する前に、クラウドインフラストラクチャプロジェクト上のAdobe Commerceからすべてのブランチをコピーし、GitLab リポジトリにプッシュすることが重要です。

**GitLab 統合を有効にするには、以下を実行します。**:

1. ターミナルから、GitLab 統合をクラウドインフラストラクチャプロジェクト上のAdobe Commerceに追加します。

   ```bash
   magento-cloud integration:add --type gitlab --project=3txxjf32gtryos --token=qVUfeEn4ouze7A7JH --base-url=https://gitlab.com/ --server-project=my-agency/project-name --build-merge-requests=false --merge-requests-clone-parent-data=false --fetch-branches=true --prune-branches=true
   ```

1. プロンプトが表示されたら、次のように入力します。 `y` をクリックして統合を追加します。

   ```terminal
   Warning: adding a 'gitlab' integration will automatically synchronize code from the external Git repository.
   This means it can overwrite all the code in your project.
   Are you sure you want to continue? [y/N] y
   ```

1. をコピーします。 **フック URL** 戻り出力で表示されます。

   ```terminal
   Hook URL: https://eu-3.magento.cloud/api/projects/3txxjf32gtryos/integrations/eolmpfizzg9lu/hook
   Created integration eolmpfizzg9lu (type: gitlab)
   +----------------------------------+---------------------------------------------------------------------------------------+
   | Property                         | Value                                                                                 |
   +----------------------------------+---------------------------------------------------------------------------------------+
   | id                               | <integration-id>                                                                      |
   | type                             | gitlab                                                                                |
   | token                            | ******                                                                                |
   | base_url                         | https://gitlab.com/                                                                   |
   | project                          | my-agency/project-name                                                                |
   | fetch_branches                   | true                                                                                  |
   | prune_branches                   | true                                                                                  |
   | build_merge_requests             | false                                                                                 |
   | merge_requests_clone_parent_data | false                                                                                 |
   | hook_url                         | https://eu-3.magento.cloud/api/projects/<project-id>/integrations/<integration-id>/hook |
   +----------------------------------+---------------------------------------------------------------------------------------+
   ```

### GitLab での Webhook の追加

イベント（プッシュ要求や結合要求など）を Cloud Git サーバーと通信するには、次の手順を実行する必要があります [ウェブフックの作成](https://docs.gitlab.com/ee/user/project/integrations/webhooks.html#overview) （GitLab リポジトリ用）

1. GitLab リポジトリで、 **設定** タブをクリックします。

1. 左側のナビゲーションバーで、 **ウェブフック**.

1. Adobe Analytics の _ウェブフック_ フォームで、次のフィールドを編集します。

   - **URL**: `Hook URL` GitLab 統合を有効にした際に返されます。
   - **秘密鍵トークン**：必要に応じて、検証暗号鍵を入力します。
   - **トリガー**：チェック `Merge request events` および/または `Push events` 必要に応じて。
   - **SSL 検証を有効にする**：このオプションを選択する必要があります。

1. クリック **ウェブフックを追加**.

### 統合のテスト

GitLab 統合を設定したら、 `magento-cloud` CLI:

```bash
magento-cloud integration:validate
```

または、単純な変更を GitLab リポジトリにプッシュしてテストできます。

1. テストファイルを作成します。

   ```bash
   touch test.md
   ```

1. 変更をコミットして GitLab リポジトリにプッシュします。

   ```bash
   git add . && git commit -m "Testing GitLab integration" && git push
   ```

1. にログインします。 [[!DNL Cloud Console]](../project/overview.md) コミットメッセージが表示され、プロジェクトが展開されていることを確認します。

## クラウドブランチの作成

以下を使用します。 `magento-cloud` CLI `environment:push` コマンドを使用して新しい環境を作成し、アクティブ化します。 詳しくは、 [クラウドブランチの作成](bitbucket.md#create-a-cloud-branch).

## 統合の削除

以下を使用します。 `magento-cloud` CLI `integration:delete` 」コマンドを使用して統合を削除します。 詳しくは、 [統合の削除](bitbucket.md#remove-the-integration).
