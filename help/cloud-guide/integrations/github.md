---
title: GitHub 統合
description: クラウドインフラストラクチャプロジェクト上のAdobe Commerceを GitHub と統合する方法を説明します。
feature: Cloud, Integration
last-substantial-update: 2023-05-25T00:00:00Z
exl-id: 5305452f-4c8d-438c-ac78-e2e1ec2f8cd9
source-git-commit: 4d32fc596064f01eaefe3ee509a655837fa846de
workflow-type: tm+mt
source-wordcount: '924'
ht-degree: 0%

---

# GitHub 統合

GitHub 統合を使用すると、クラウドインフラストラクチャ環境でAdobe Commerceを GitHub リポジトリから直接管理できます。 この統合では、既に GitHub に存在するコンテンツを管理し、クラウドインフラストラクチャコードリポジトリ上のAdobe Commerceと同期します。 基本的に、コードリポジトリは GitHub リポジトリのミラーになります。

{{private-repository}}

この統合により、次のことが可能になります。

- ブランチの作成時に環境を作成します
- プルリクエストを結合する際に環境を再デプロイ
- ブランチを削除したら、環境を削除します

プロセスを続行するには、GitHub トークンと Webhook を取得する必要があります。

## 前提条件

- Adobe Commerce on cloud infrastructure プロジェクトへの管理者アクセス
- GitHub リポジトリ
- GitHub 個人用アクセストークン

## GitHub トークンの生成

GitHub 開発者設定で、従来の個人用アクセストークンを作成します。 GitHub リポジトリへの書き込みアクセス権を持つグループのメンバーであることが必要です。これにより、 _プッシュ_ をリポジトリに送信します。 トークンを作成する際には、次の範囲を含めます。

- `admin:repo_hook`— Web フックを作成します。
- `repo` – リポジトリと統合する
- `read:org` – 組織リポジトリと統合します。

参照： [GitHub：作成](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token).

## リポジトリを準備

既存の環境からクラウドインフラストラクチャー上のAdobe Commerce プロジェクトのクローンを作成し、同じブランチ名を保持したまま、プロジェクトのブランチを新しい空の GitHub リポジトリに移行します。 このプロパティは **重大** Adobe Commerce on cloud infrastructure プロジェクトで既存の環境やブランチが失われないように、同一の Git ツリーを保持する。

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

1. GitHub リポジトリをリモートとして追加します。

   ```bash
   git remote add origin git@github.com:<user-name>/<repo-name>.git
   ```

   リモート接続のデフォルト名は以下のようになります。 `origin` または `magento`. 次の場合 `origin` 存在する、別の名前を選択できる、または既存の参照の名前を変更または削除できる。 参照： [git-remote ドキュメント](https://git-scm.com/docs/git-remote).

1. GitHub リモートが正しく追加されていることを確認します。

   ```bash
   git remote -v
   ```

   期待される応答：

   ```terminal
   origin git@github.com:<user-name>/<repo-name>.git (fetch)
   origin git@github.com:<user-name>/<repo-name>.git (push)
   ```

1. プロジェクトファイルを新しい GitHub リポジトリにプッシュします。 すべてのブランチ名は同じにしておくことを忘れないでください。

   ```bash
   git push -u origin master
   ```

   新しい GitHub リポジトリから始める場合は、を使用する必要がある場合があります。 `-f` リモートリポジトリがローカルコピーと一致しないためです。

1. GitHub リポジトリにすべてのプロジェクトファイルが含まれていることを確認します。

## GitHub 統合の有効化

開始する前に、プロジェクトコードと環境を GitHub リポジトリに配置する必要があります。 統合を有効にすると、GitHub リポジトリがコードソースになります。 コードの変更を元の値にプッシュした場合 `magento` リポジトリに対するコードの変更を GitHub リポジトリにプッシュすると、統合によって上書きされます。

以下は GitHub 統合を有効にし、Webhook の作成時に使用するペイロード URL を指定します。

>[!WARNING]
>
>次のコマンドは上書きします _all_ クラウドインフラストラクチャプロジェクト上のAdobe Commerceのコード。GitHub リポジトリのコードが含まれます。このリポジトリには、以下を含むすべてのブランチが含まれます。 `production` 分岐。 このアクションは即座に実行され、元に戻すことはできません。 ベストプラクティスとして、Adobe Commerce on cloud infrastructure プロジェクトからすべてのブランチをクローンし、GitHub リポジトリにプッシュすることが重要です **次の前** github 統合を追加します。

次のコマンドを使用して、CLI プロンプトをステップ実行できます。 `magento-cloud integration:add` または、次のオプションを使用して統合コマンドを作成できます。

| オプション | 必須？ | 説明 |
| ----------------------- | --------- | --------------------------------- |
| `--base-url` | はい | サーバーインストールのベース URL。次のようになります `https://github.com/` またはカスタム。 リポジトリが公開 Github でホストされている場合は、このオプションを省略します。 |
| `--token` | はい | GitHub 用に生成した個人用アクセストークン |
| `--repository` | はい | リポジトリ名： `owner-or-organisation/repository` |
| `--build-pull-requests` | オプション | は、プルリクエストを結合した後にデプロイするように、クラウドインフラストラクチャ上のAdobe Commerceに指示します（`true` デフォルト） |
| `--fetch-branches` | オプション | クラウドインフラストラクチャ上のAdobe Commerceで分岐を追跡し、分岐を更新した後にデプロイします（`true` デフォルト） |
| `--prune-branches` | オプション | リモートに存在しないブランチを削除します（`true` デフォルト） |

その他にも様々なオプションがあり、ヘルプ オプションを使用して確認できます。

```bash
magento-cloud integration:add --help
```

**GitHub 統合を有効にするには**:

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

1. をコピーします **ペイロード URL** 戻り値の出力で表示されます。

   ```terminal
   Created integration <integration-ID> (type: github)
   Repository: myUserName/myrepo
   Build PRs: yes
   Fetch branches: yes
   Payload URL: https://us.magento.cloud/api/projects/<project-id>/integrations/wO8a0eoamxwcg/hook
   ```

## GitHub での Webhook の追加

プッシュなどのイベントをクラウド Git サーバーと通信するには、GitHub リポジトリの Webhook を作成する必要があります。

1. GitHub リポジトリで、 **設定** タブ。

1. 左側のナビゲーションバーで、 **Webhook**.

1. が含まれる _Webhook_ ウィンドウで、をクリック **Webhook を追加**.

1. が含まれる _Webhook/Add Webhook_ フォームで、次のフィールドを編集します。

   - **ペイロード URL**:GitHub 統合を有効にしたときに返される URL を入力します。
   - **コンテンツタイプ**：を選択 **application/json** リストから。
   - **秘密鍵**：検証シークレットを入力します。
   - **この Webhook をトリガーにするイベント**：を選択 **すべて送ってください**.
   - 「」を選択します **アクティブ** チェックボックス。

1. クリック **Webhook を追加**.

## 統合のテスト

GitHub 統合を設定したら、統合が動作していることを次を使用して確認できます `magento-cloud` CLI:

```bash
magento-cloud integration:validate
```

または、GitHub リポジトリに簡単な変更をプッシュしてテストできます。

1. テストファイルを作成します。

   ```bash
   touch test.md
   ```

1. 変更をコミットして GitHub リポジトリにプッシュします。

   ```bash
   git add . && git commit -m "Testing GitHub integration" && git push
   ```

1. にログインします [[!DNL Cloud Console]](../project/overview.md) コミットメッセージが表示され、プロジェクトがデプロイされていることを確認します。

## 統合の削除

コードに影響を与えることなく、プロジェクトから GitHub 統合を安全に削除できます。

**GitHub 統合を削除するには**:

1. ターミナルから、クラウドインフラストラクチャプロジェクトのAdobe Commerceにログインします。

1. 統合のリストを作成します。 次の手順を完了するには、GitHub 統合 ID が必要です。

   ```bash
   magento-cloud integration:list
   ```

1. 統合を削除します。

   ```bash
   magento-cloud integration:delete <int-ID>
   ```

また、GitHub アカウントにログインし、 _Webhook_ リポジトリーのタブ _設定_.
