---
title: GitHub との統合
description: クラウドインフラストラクチャプロジェクト上のAdobe Commerceを GitHub と統合する方法について説明します。
feature: Cloud, Integration
last-substantial-update: 2023-05-25T00:00:00Z
exl-id: 5305452f-4c8d-438c-ac78-e2e1ec2f8cd9
source-git-commit: 4d32fc596064f01eaefe3ee509a655837fa846de
workflow-type: tm+mt
source-wordcount: '924'
ht-degree: 0%

---

# GitHub との統合

GitHub 統合を使用すると、クラウドインフラストラクチャ環境上のAdobe Commerceを GitHub リポジトリから直接管理できます。 統合では、既に GitHub にあるコンテンツを管理し、クラウドインフラストラクチャコードリポジトリのAdobe Commerceと同期します。 基本的に、コードリポジトリは GitHub リポジトリのミラーになります。

{{private-repository}}

この統合により、次のことが可能になります。

- ブランチの作成時に環境を作成する
- プルリクエストを結合する際に環境を再デプロイする
- 分岐を削除する際に環境を削除する

処理を続行するには、GitHub トークンと Webhook を入手する必要があります。

## 前提条件

- クラウドインフラストラクチャプロジェクト上のAdobe Commerceへの管理者アクセス権
- GitHub リポジトリ
- GitHub 個人用アクセストークン

## GitHub トークンの生成

GitHub 開発者設定で、従来の個人用アクセストークンを作成します。 GitHub リポジトリへの書き込みアクセス権を持つグループのメンバーであることが、次の操作を実行できるようにする必要があります。 _プッシュ_ をリポジトリに追加します。 トークンを作成する際に、次のスコープを含めます。

- `admin:repo_hook` — ウェブフックを作成する
- `repo` — リポジトリと統合します。
- `read:org` — 組織のリポジトリと統合します。

詳しくは、 [GitHub：作成](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token).

## リポジトリの準備

既存の環境からクラウドインフラストラクチャプロジェクト上のAdobe Commerceを複製し、同じブランチ名を保持したまま、プロジェクトブランチを新しい空の GitHub リポジトリに移行します。 それは **重要な** 同じ Git ツリーを保持して、Adobe Commerce on cloud infrastructure プロジェクト内の既存の環境やブランチが失われないようにします。

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

1. GitHub リポジトリをリモートリポジトリとして追加します。

   ```bash
   git remote add origin git@github.com:<user-name>/<repo-name>.git
   ```

   リモート接続のデフォルト名は次のようになります。 `origin` または `magento`. 次の場合 `origin` が存在する場合は、別の名前を選択するか、既存の参照の名前を変更または削除することができます。 詳しくは、 [git-remote ドキュメント](https://git-scm.com/docs/git-remote).

1. GitHub リモートが正しく追加されたことを確認します。

   ```bash
   git remote -v
   ```

   期待される応答：

   ```terminal
   origin git@github.com:<user-name>/<repo-name>.git (fetch)
   origin git@github.com:<user-name>/<repo-name>.git (push)
   ```

1. プロジェクトファイルを新しい GitHub リポジトリにプッシュします。 すべてのブランチ名は同じにしてください。

   ```bash
   git push -u origin master
   ```

   新しい GitHub リポジトリを使用し始める場合は、 `-f` 」オプションを使用します。リモートリポジトリがローカルコピーと一致しないためです。

1. GitHub リポジトリに、すべてのプロジェクトファイルが含まれていることを確認します。

## GitHub 統合の有効化

開始する前に、プロジェクトのコードと環境を GitHub リポジトリに配置する必要があります。 統合を有効にすると、GitHub リポジトリがコードソースになります。 コードの変更を元の `magento` リポジトリの場合、コードの変更を GitHub リポジトリにプッシュすると、統合によって上書きされます。

次の例では、GitHub 統合が有効になっており、Webhook の作成時に使用するペイロード URL を提供しています。

>[!WARNING]
>
>次のコマンドは、 _すべて_ Adobe Commerce on cloud infrastructure プロジェクト内のコード（GitHub リポジトリ内のコード）。この中には、 `production` 分岐。 この操作は即座に実行され、元に戻すことはできません。 ベストプラクティスとしては、クラウドインフラストラクチャプロジェクト上のAdobe Commerceからすべてのブランチをコピーし、それらを GitHub リポジトリにプッシュすることが重要です **前** GitHub 統合を追加する方法について説明します。

CLI プロンプトを順を追って表示するには、 `magento-cloud integration:add` または、次のオプションを使用して統合コマンドを作成できます。

| オプション | 必須？ | 説明 |
| ----------------------- | --------- | --------------------------------- |
| `--base-url` | はい | サーバーインストールのベース URL( `https://github.com/` またはカスタム。 リポジトリーがパブリック GitHub でホストされている場合は、このオプションを省略します。 |
| `--token` | はい | GitHub 用に生成した個人用アクセストークン |
| `--repository` | はい | リポジトリ名： `owner-or-organisation/repository` |
| `--build-pull-requests` | オプション | プルリクエスト (`true` （デフォルト） |
| `--fetch-branches` | オプション | ブランチ (`true` （デフォルト） |
| `--prune-branches` | オプション | リモートに存在しないブランチを削除する (`true` （デフォルト） |

他にも多くのオプションがあり、ヘルプオプションを使用して表示できます。

```bash
magento-cloud integration:add --help
```

**GitHub 統合を有効にするには、以下を実行します。**:

1. 統合を有効にします。

   ```bash
   magento-cloud integration:add --type=github --project=<project-ID> --token=<your-GitHub-token> {--repository=USER/REPOSITORY | --repository=ORGANIZATION/REPOSITORY} [--build-pull-requests={true|false} --fetch-branches={true|false}
   ```

   **例 1**：個人用のプライベートリポジトリに対して GitHub 統合を有効にします。

   ```bash
   magento-cloud integration:add --type=github --project=ov58dlacU2e --base-url=https://github.com --token=<token> --repository=myUserName/myrepo
   ```

   **例 2**：組織リポジトリの GitHub 統合を有効にします。

   ```bash
   magento-cloud integration:add --type=github --project=ov58dlacU2e --base-url=https://github.com --token=<token> --repository=Magento/teamrepo
   ```

1. プロンプトが表示されたら、必要な情報を入力します。

1. をコピーします。 **ペイロード URL** 戻り出力で表示されます。

   ```terminal
   Created integration <integration-ID> (type: github)
   Repository: myUserName/myrepo
   Build PRs: yes
   Fetch branches: yes
   Payload URL: https://us.magento.cloud/api/projects/<project-id>/integrations/wO8a0eoamxwcg/hook
   ```

## GitHub に Webhook を追加する

イベント（プッシュなど）を Cloud Git サーバーと通信するには、GitHub リポジトリの Webhook を作成する必要があります。

1. GitHub リポジトリで、 **設定** タブをクリックします。

1. 左側のナビゲーションバーで、 **ウェブフック**.

1. Adobe Analytics の _ウェブフック_ ウィンドウ枠で、 **ウェブフックを追加**.

1. Adobe Analytics の _ウェブフック/ウェブフックを追加_ フォームで、次のフィールドを編集します。

   - **ペイロード URL**:GitHub 統合を有効にした際に返される URL を入力します。
   - **コンテンツタイプ**：を選択します。 **application/json** を選択します。
   - **秘密鍵**：検証暗号鍵を入力します。
   - **このウェブフックに対してトリガーを設定するイベントを選択してください。**：を選択します。 **すべて送信**.
   - を選択します。 **アクティブ** チェックボックス。

1. クリック **ウェブフックを追加**.

## 統合のテスト

GitHub 統合を設定したら、 `magento-cloud` CLI:

```bash
magento-cloud integration:validate
```

または、簡単な変更を GitHub リポジトリにプッシュしてテストすることもできます。

1. テストファイルを作成します。

   ```bash
   touch test.md
   ```

1. 変更をコミットして GitHub リポジトリにプッシュします。

   ```bash
   git add . && git commit -m "Testing GitHub integration" && git push
   ```

1. にログインします。 [[!DNL Cloud Console]](../project/overview.md) コミットメッセージが表示され、プロジェクトが展開されていることを確認します。

## 統合の削除

コードに影響を与えることなく、GitHub 統合をプロジェクトから安全に削除できます。

**GitHub 統合を削除するには、以下を実行します。**:

1. ターミナルから、クラウドインフラストラクチャプロジェクトのAdobe Commerceにログインします。

1. 統合の一覧を表示します。 次の手順を完了するには、GitHub 統合 ID が必要です。

   ```bash
   magento-cloud integration:list
   ```

1. 統合を削除します。

   ```bash
   magento-cloud integration:delete <int-ID>
   ```

また、GitHub アカウントにログインして、 _ウェブフック_ リポジトリの「 」タブ _設定_.
