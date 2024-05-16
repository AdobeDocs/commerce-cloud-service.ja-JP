---
title: CLI によるブランチの管理
description: Cloud CLI を使用して、クラウドインフラストラクチャ上のAdobe Commerceの環境ブランチを管理する方法について説明します。
role: Developer
feature: Cloud, Install
exl-id: a871c7e2-4506-4a05-8fc2-fc5ef2afe609
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '676'
ht-degree: 0%

---

# CLI によるブランチの管理

をインストールするには `magento-cloud` CLI、を参照 [Cloud CLI リファレンス](../dev-tools/cloud-cli-overview.md). をインストールしたら、 `magento-cloud` CLI と、クラウドインフラストラクチャへのリモートアクセス用の SSH キーを設定すると、を使用できます。 `magento-cloud` プロジェクトの環境を管理する CLI コマンド。 環境のアーキテクチャについては、を参照してください。 [スターターアーキテクチャ](../architecture/starter-architecture.md) または [Pro アーキテクチャ](../architecture/pro-architecture.md).

を使用してブランチと環境を管理する [!DNL Cloud Console]を参照してください [を使用して分岐を管理する [!DNL Cloud Console]](../project/console-branches.md).

## CLI コマンドの使用

この `magento-cloud` CLI コマンドは、Git コマンドに似ています。 これらを使用して、プロジェクトに接続し、環境を管理できます。 コマンドは任意のディレクトリから実行できますが、プロジェクトディレクトリから実行することをお勧めします。 プロジェクトディレクトリから実行する場合、を省略できます `-p <project-ID>` パラメーター。 を参照してください。 [Cloud CLI リファレンス](../dev-tools/cloud-cli-overview.md).

## プロジェクトのクローン

以下の手順では、以下を組み合わせて使用します。 `magento-cloud` プロジェクトをローカルワークステーションに複製するための CLI コマンドと Git コマンド。 の完全なリストを表示するには `magento-cloud` CLI コマンド、を使用 `magento-cloud list` コマンド。

>[!IMPORTANT]
>
>一部の Git コマンドでは、クラウドインフラストラクチャプロジェクト上のAdobe Commerceでアクションを完了できません。 例えば、Git コマンドを使用してブランチを作成できますが、新しい環境を作成してアクティブ化することはできません。 を使用して環境を作成する必要があります `magento-cloud environment:branch <branch-name>` 環境がになるコマンド _アクティブ_. または、を使用することもできます。 [!DNL Cloud Console] アクティブな環境を作成する 参照： [Cloud CLI リファレンス](../dev-tools/cloud-cli-overview.md#git-commands).

**プロジェクトを複製するには `master` 0.9511122**:

1. を使用して、ローカルワークステーションにログインします。 [ファイルシステム所有者](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/prerequisites/file-system/configure-permissions.html) アカウント。

1. Web サーバーまたは仮想ホストへの変更 _docroot_ ディレクトリ。

1. を使用してログイン `magento-cloud` CLI。

   ```bash
   magento-cloud login
   ```

1. プロジェクトのリストを作成します。

   ```bash
   magento-cloud project:list
   ```

1. プロジェクトのクローン。

   ```bash
   magento-cloud project:get <project-ID>
   ```

   プロンプトが表示されたら、ディレクトリ名を指定します。

1. をに変更します。 `magento2` ディレクトリ。

1. プロジェクトで使用可能な環境のリスト。

   ```bash
   magento-cloud environment:list
   ```

   >[!IMPORTANT]
   >
   >この `magento-cloud environment:list` コマンドは環境階層を表示するのに対して、 `git branch` コマンドは実行しません。

1. リモートブランチを取得します。

   ```bash
   git fetch origin
   ```

1. 更新されたコードを取り込みます。

   ```bash
   git pull origin <environment-ID>
   ```

>[!TIP]
>
>参照： [統合](../integrations/overview.md) cloud infrastructure 上のAdobe Commerceで Git ベースのホスティングサービスを使用する方法について詳しくは、

## 開発用のブランチの作成

プロジェクトを複製し、Adobe Commerce管理者アカウントの設定を更新したら、開発用に分岐できます。 前述のように、を使用して環境を作成する必要があります `magento-cloud environment:branch <branch-name>` command または [!DNL Cloud Console] 環境が～になる _アクティブ_.

- の場合 [スターター](../architecture/starter-develop-deploy-workflow.md#clone-and-branch)を作成する場合は、に分岐を作成することを検討します `staging`に基づいて開発ブランチを作成します。 `staging` 分岐。
- の場合 [プロ](../architecture/pro-develop-deploy-workflow.md#development-workflow)に基づいて、開発ブランチを作成します。 `Integration` 分岐。

**開発ブランチを作成するには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. プロジェクトワークフローに推奨されるブランチに基づいて環境を作成します。

   ```bash
   magento-cloud branch <new-environment-name> integration
   ```

1. 依存関係を更新します。

   ```bash
   composer --no-ansi --no-interaction install --no-progress --prefer-dist --optimize-autoloader
   ```

1. [_optional_] を作成 [バックアップ](../storage/snapshots.md) 環境の。

### ブランチの結合

開発が完了したら、このブランチを親に結合します。

1. コードの変更をコミットしてプッシュします。

   ```bash
   git add -A && git commit -m "Add message here"
   ```

   ```bash
   git push origin <branch-name>
   ```

1. 親環境と結合します。

   ```bash
   magento-cloud environment:merge <environment-ID>
   ```

### 環境の削除

環境が不要になったことが確実な場合にのみ、環境を削除します。 環境を削除した後は、その環境を復元できません。

>[!WARNING]
>
>を削除することはできません `master` 任意のプロジェクトのブランチ。

このタスクを実行するには、プロジェクト管理者、環境管理者、またはアカウント所有者である必要があります。 参照： [クラウドプロジェクトへのユーザーアクセスの管理](../project/user-access.md).

環境を削除すると、その環境はに設定されます _inactive_. コードは引き続き Git ブランチで使用できますが、サービスやデータベースは含まれなくなりました。 環境を完全に削除するには、対応するリモート Git ブランチも削除する必要があります。

**環境を削除するには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. リモートサーバーから更新を取得します。

   ```bash
   git fetch
   ```

1. 環境ブランチを削除します。

   ```bash
   magento-cloud environment:delete <environment-ID>
   ```

   オプションとして、delete コマンドに複数の環境 ID を追加することで、一度に複数の環境を削除できます。

   ```bash
   magento-cloud environment:delete <environment-1-ID> <environment-2-ID>
   ```

1. プロンプトに応答して、ローカル環境と対応するリモート環境を削除します。

   ```terminal
   The environment <environment-ID> is currently active: deleting it will delete all associated data.
   Are you sure you want to delete the environment <environment-ID>? [Y/n]
   ```

   環境を削除すると、に配置されます。 _inactive_ 都道府県。

   ```terminal
   Delete the remote Git branch too? [Y/n]
   ```

   リモート Git ブランチを削除すると、プロジェクトから環境が削除されます。

1. 環境が削除されるのを待ちます。

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
>非アクティブな環境をアクティブ化するには、 `magento-cloud environment:activate` コマンド。

## リモート環境とのインタラクション

お先に [ssh キーの設定](../development/secure-connections.md)の場合は、次のことができます [ローカルワークスペースからリモート環境への接続](../development/secure-connections.md#connect-to-a-remote-environment) を使用して、プロジェクトサービスとやり取りし、設定を変更できます。
