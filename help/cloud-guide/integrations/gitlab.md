---
title: GitLab との統合
description: クラウドインフラストラクチャプロジェクト上のAdobe Commerceを GitLab と統合する方法を説明します。
feature: Cloud, Integration
exl-id: 37fda8a0-7274-422f-9049-243f2e409f26
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '777'
ht-degree: 0%

---

# GitLab との統合

コードの変更をプッシュするときに環境を自動的にビルドおよびデプロイするように、GitLab リポジトリを設定できます。 この統合により、GitLab リポジトリがクラウドインフラストラクチャアカウント上のAdobe Commerceと同期されます。

{{private-repository}}

この統合により、次のことが可能になります。

- ブランチの作成時に環境を作成します
- プルリクエストを結合する際に環境を再デプロイ
- ブランチを削除したら、環境を削除します

プロセスを続行するには、GitLab トークンと Webhook を取得する必要があります。

## 前提条件

- Adobe Commerce on cloud infrastructure プロジェクトへの管理者アクセス
- [`magento-cloud` CLI](../dev-tools/cloud-cli-overview.md) ローカル環境のツール
- GitLab アカウント
- GitLab リポジトリへの書き込みアクセス権を持つ GitLab 個人用アクセストークン。選択した範囲は、少なくとも次の条件を満たす必要があります。 `api` および `read_repository`.

## リポジトリを準備

既存の環境からクラウドインフラストラクチャプロジェクト上にAdobe Commerceのクローンを作成し、同じブランチ名を保持したまま、プロジェクトのブランチを新しい空の GitLab リポジトリに移行します。 このプロパティは **重大** Adobe Commerce on cloud infrastructure プロジェクトで既存の環境やブランチが失われないように、同一の Git ツリーを保持する。

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
   magento-cloud project:get <project-id>
   ```

1. GitLab リポジトリをリモートとして追加します（SaaS バージョンで GitLab が使用されている場合）。

   ```bash
   git remote add origin git@gitlab.com:<user-name>/<repo-name>.git
   ```

   リモート接続のデフォルト名は以下のようになります。 `origin` または `magento`. 次の場合 `origin` 存在する、別の名前を選択できる、または既存の参照の名前を変更または削除できる。 参照： [git-remote ドキュメント](https://git-scm.com/docs/git-remote).

1. GitLab リモートが正しく追加されていることを確認します。

   ```bash
   git remote -v
   ```

   期待される応答：

   ```terminal
   origin git@gitlab.com:<user-name>/<repo-name>.git (fetch)
   origin git@gitlab.com:<user-name>/<repo-name>.git (push)
   ```

1. プロジェクトファイルを新しい GitLab リポジトリにプッシュします。 すべてのブランチ名は同じにしておくことを忘れないでください。

   ```bash
   git push -u origin master
   ```

   新しい GitLab リポジトリから始める場合は、を使用する必要がある場合があります。 `-f` リモートリポジトリがローカルコピーと一致しないためです。

1. GitLab リポジトリにすべてのプロジェクトファイルが含まれていることを確認します。

## GitLab 統合の有効化

の使用 `magento-cloud integration` コマンドを使用して GitLab 統合を有効にし、GitLab Webhook のペイロード URL を取得して、GitLab からクラウドインフラストラクチャプロジェクトのAdobe Commerceに更新を送信します。

```bash
magento-cloud integration:add --type=gitlab --project=<project-ID> --token=<your-GitLab-token> [--base-url=<GitLab-url> --server-project=<GitLab-project> --build-merge-requests={true|false} --merge-requests-clone-parent-data={true|false} --fetch-branches={true|false} --prune-branches={true|false}]
```

| オプション | 説明 |
| ------ | ----------- |
| `<project-ID>` | クラウドインフラストラクチャー上のAdobe Commerce プロジェクト ID |
| `<your-GitLab-token>` | GitLab 用に生成した個人用アクセストークン |
| `--base-url` | GitLab の URL （`https://gitlab.com/` gitlab が SaaS バージョンで使用されている場合） |
| `--server-project` | GitLab のプロジェクト名（ベース URL の後の部分） |
| `--build-merge-requests` | An _optional_ 結合リクエストごとに新しい環境を構築するようにAdobe Commerceにクラウドインフラストラクチャに指示するパラメーター（`true` デフォルト） |
| `--merge-requests-clone-parent-data` | An _optional_ 結合リクエスト用に親環境のデータを複製するようにAdobe Commerceにクラウドインフラストラクチャで指示するパラメーター（`true` デフォルト） |
| `--fetch-branches` | An _optional_ Adobe Commerce on cloud infrastructure がリモート環境（非アクティブな環境として）からすべてのブランチを取得できるようにするパラメーター（`true` デフォルト） |
| `--prune-branches` | An _optional_ リモートに存在しないブランチを削除するようにAdobe Commerceにクラウドインフラストラクチャに指示するパラメーター（`true` デフォルト） |

>[!WARNING]
>
>この `magento-cloud integration` コマンドの上書き _all_ gitlab リポジトリのコードを使用して、クラウドインフラストラクチャプロジェクト上のAdobe Commerceのコードを生成します。 これには、を含むすべてのブランチが含まれます `production` 分岐。 このアクションは即座に実行され、元に戻すことはできません。 ベストプラクティスとして、GitLab 統合を追加する前に、クラウドインフラストラクチャプロジェクト上のAdobe Commerceからすべてのブランチをクローンし、GitLab リポジトリにプッシュすることが重要です。

**GitLab 統合を有効にするには**:

1. ターミナルから、Adobe Commerce on cloud infrastructure プロジェクトに GitLab 統合を追加します。

   ```bash
   magento-cloud integration:add --type gitlab --project=3txxjf32gtryos --token=qVUfeEn4ouze7A7JH --base-url=https://gitlab.com/ --server-project=my-agency/project-name --build-merge-requests=false --merge-requests-clone-parent-data=false --fetch-branches=true --prune-branches=true
   ```

1. プロンプトが表示されたら、と入力します `y` ：統合を追加します。

   ```terminal
   Warning: adding a 'gitlab' integration will automatically synchronize code from the external Git repository.
   This means it can overwrite all the code in your project.
   Are you sure you want to continue? [y/N] y
   ```

1. をコピーします **フック URL** 戻り値の出力で表示されます。

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

イベント（プッシュリクエストや結合リクエストなど）を Cloud Git サーバーと通信するには、次の操作が必要です [webhook の作成](https://docs.gitlab.com/ee/user/project/integrations/webhooks.html#overview) gitlab リポジトリの場合

1. GitLab リポジトリで、 **設定** タブ。

1. 左側のナビゲーションバーで、 **Webhook**.

1. が含まれる _Webhook_ フォームで、次のフィールドを編集します。

   - **URL**：を入力します `Hook URL` は、GitLab 統合を有効にした場合に返されます。
   - **秘密鍵トークン**：必要に応じて、検証秘密鍵を入力します。
   - **トリガー**：チェック `Merge request events` および/または `Push events` 必要に応じて調整します。
   - **SSL 検証を有効にする**：このオプションを選択する必要があります。

1. クリック **Webhook を追加**.

### 統合のテスト

GitLab 統合を設定したら、統合が動作していることを次を使用して確認できます `magento-cloud` CLI:

```bash
magento-cloud integration:validate
```

または、GitLab リポジトリに簡単な変更をプッシュしてテストできます。

1. テストファイルを作成します。

   ```bash
   touch test.md
   ```

1. 変更をコミットして GitLab リポジトリにプッシュします。

   ```bash
   git add . && git commit -m "Testing GitLab integration" && git push
   ```

1. にログインします [[!DNL Cloud Console]](../project/overview.md) コミットメッセージが表示され、プロジェクトがデプロイされていることを確認します。

## クラウドブランチの作成

の使用 `magento-cloud` CLI `environment:push` 新しい環境を作成してアクティブにするコマンド。 参照： [クラウドブランチの作成](bitbucket.md#create-a-cloud-branch).

## 統合の削除

の使用 `magento-cloud` CLI `integration:delete` 統合を削除するコマンド。 参照： [統合の削除](bitbucket.md#remove-the-integration).
