---
title: CLI でブランチを管理
description: Cloud CLI を使用して、クラウドインフラストラクチャ上のAdobe Commerceの環境ブランチを管理する方法を説明します。
role: Developer
feature: Cloud, Install
exl-id: a871c7e2-4506-4a05-8fc2-fc5ef2afe609
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '676'
ht-degree: 0%

---

# CLI でブランチを管理

をインストールするには、以下を実行します。 `magento-cloud` CLI(「 [Cloud CLI リファレンス](../dev-tools/cloud-cli-overview.md). インストール後、 `magento-cloud` クラウドインフラストラクチャへのリモートアクセス用に CLI と SSH キーを設定すると、 `magento-cloud` プロジェクトの環境を管理する CLI コマンド。 環境アーキテクチャについて詳しくは、 [スターターアーキテクチャ](../architecture/starter-architecture.md) または [Pro アーキテクチャ](../architecture/pro-architecture.md).

ブランチと環境を [!DNL Cloud Console]を参照してください。 [でブランチを管理 [!DNL Cloud Console]](../project/console-branches.md).

## CLI コマンドの使用

The `magento-cloud` CLI コマンドは Git コマンドに似ています。 これらを使用して、プロジェクトに接続し、環境を管理できます。 どのディレクトリからでもコマンドを実行できますが、プロジェクトディレクトリから実行することをお勧めします。 プロジェクトディレクトリから実行する場合は、 `-p <project-ID>` パラメーター。 詳しくは、 [Cloud CLI リファレンス](../dev-tools/cloud-cli-overview.md).

## プロジェクトを複製

以下の手順では、 `magento-cloud` プロジェクトをローカルワークステーションに複製する CLI コマンドおよび Git コマンド。 の完全なリストを表示するには `magento-cloud` CLI コマンドを使用する場合は、 `magento-cloud list` コマンドを使用します。

>[!IMPORTANT]
>
>一部の Git コマンドでは、クラウドインフラストラクチャプロジェクト上のAdobe Commerceでアクションを完了できません。 例えば、Git コマンドを使用してブランチを作成できますが、新しい環境を作成してアクティブ化することはできません。 環境は、 `magento-cloud environment:branch <branch-name>` 環境を～にするための命令 _アクティブ_. または、 [!DNL Cloud Console] アクティブな環境を作成する場合。 詳しくは、 [Cloud CLI リファレンス](../dev-tools/cloud-cli-overview.md#git-commands).

**プロジェクトを複製するには `master` 環境**:

1. を使用してローカルワークステーションにログインします。 [ファイルシステム所有者](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/prerequisites/file-system/configure-permissions.html) アカウント。

1. Web サーバーまたは仮想ホストに対する変更 _docroot_ ディレクトリ。

1. 次を使用してログイン： `magento-cloud` CLI。

   ```bash
   magento-cloud login
   ```

1. プロジェクトをリストします。

   ```bash
   magento-cloud project:list
   ```

1. プロジェクトを複製します。

   ```bash
   magento-cloud project:get <project-ID>
   ```

   プロンプトが表示されたら、ディレクトリ名を指定します。

1. 次に変更： `magento2` ディレクトリ。

1. プロジェクトで使用可能な環境のリストを表示します。

   ```bash
   magento-cloud environment:list
   ```

   >[!IMPORTANT]
   >
   >The `magento-cloud environment:list` コマンドは環境階層を表示するのに対して、 `git branch` コマンドは実行しません。

1. リモートブランチを取得します。

   ```bash
   git fetch origin
   ```

1. 更新されたコードをプルします。

   ```bash
   git pull origin <environment-ID>
   ```

>[!TIP]
>
>詳しくは、 [統合](../integrations/overview.md) Git ベースのホスティングサービスをクラウドインフラストラクチャ上のAdobe Commerceで使用する方法について説明します。

## 開発用のブランチの作成

プロジェクトのクローンを作成し、Adobe Commerce管理者アカウント設定を更新した後、開発用に分岐できます。 前述のように、 `magento-cloud environment:branch <branch-name>` コマンドまたは [!DNL Cloud Console] 環境が整う _アクティブ_.

- の場合 [スターター](../architecture/starter-develop-deploy-workflow.md#clone-and-branch)を使用する場合は、次のブランチの作成を検討してください。 `staging`を作成し、次に、 `staging` 分岐。
- の場合 [Pro](../architecture/pro-develop-deploy-workflow.md#development-workflow)、以下に基づいて開発ブランチを作成 `Integration` 分岐。

**開発ブランチを作成するには**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. プロジェクトワークフローに推奨されるブランチに基づいて環境を作成します。

   ```bash
   magento-cloud branch <new-environment-name> integration
   ```

1. 依存関係を更新します。

   ```bash
   composer --no-ansi --no-interaction install --no-progress --prefer-dist --optimize-autoloader
   ```

1. [_オプション_] の作成 [バックアップ](../storage/snapshots.md) 環境の。

### ブランチのマージ

開発が完了したら、このブランチを親に結合します。

1. コードの変更をコミットおよびプッシュする：

   ```bash
   git add -A && git commit -m "Add message here"
   ```

   ```bash
   git push origin <branch-name>
   ```

1. 親環境とのマージ：

   ```bash
   magento-cloud environment:merge <environment-ID>
   ```

### 環境の削除

環境が不要になったことが確実な場合にのみ、環境を削除します。 削除した環境は復元できません。

>[!WARNING]
>
>を削除することはできません `master` 任意のプロジェクトのブランチ。

このタスクを実行するには、プロジェクト管理者、環境管理者、またはアカウント所有者である必要があります。 詳しくは、 [クラウドプロジェクトへのユーザーアクセスを管理](../project/user-access.md).

環境を削除すると、環境はに設定されます。 _非アクティブ_. このコードは Git ブランチで引き続き使用できますが、サービスやデータベースは含まれなくなります。 環境を完全に削除するには、対応するリモート Git ブランチも削除する必要があります。

**環境を削除するには**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. リモートサーバーから更新を取得します。

   ```bash
   git fetch
   ```

1. 環境ブランチを削除します。

   ```bash
   magento-cloud environment:delete <environment-ID>
   ```

   必要に応じて、削除コマンドに複数の環境 ID を追加することで、一度に複数の環境を削除できます。

   ```bash
   magento-cloud environment:delete <environment-1-ID> <environment-2-ID>
   ```

1. プロンプトに従って、ローカル環境と対応するリモート環境を削除します。

   ```terminal
   The environment <environment-ID> is currently active: deleting it will delete all associated data.
   Are you sure you want to delete the environment <environment-ID>? [Y/n]
   ```

   環境を削除すると、 _非アクティブ_ 状態。

   ```terminal
   Delete the remote Git branch too? [Y/n]
   ```

   リモート Git ブランチを削除すると、その環境がプロジェクトから削除されます。

1. 環境が削除されるまで待ちます。

   ```terminal
   Deleting environment <environment-ID>
   Waiting for the activity...
     Deleting environment <project-id>-<environment-ID>-xxxxxx
   
     [============================]  1 min (complete)
   Activity ID succeeded
   Deleted remote Git branch <environment-ID>
   Run git fetch --prune to remove deleted branches from your local cache.
   ```

>[!TIP]
>
>非アクティブな環境をアクティブ化するには、 `magento-cloud environment:activate` コマンドを使用します。

## リモート環境とのやり取り

後で [SSH キーの設定](../development/secure-connections.md)を使用する場合、 [ローカルワークスペースからリモート環境への接続](../development/secure-connections.md#connect-to-a-remote-environment) プロジェクトサービスとやり取りし、設定を変更します。
