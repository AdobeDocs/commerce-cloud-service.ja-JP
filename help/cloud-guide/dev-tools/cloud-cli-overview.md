---
title: Cloud CLI
description: Magento-cloud CLI と、Adobe Commerce on クラウドインフラストラクチャプロジェクトのローカル開発環境を管理する方法について説明します。
exl-id: 70dddd62-0269-4af4-bd2a-1a4fbf11a131
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '741'
ht-degree: 0%

---


# Cloud CLI

The `magento-cloud` CLI ツールを使用すると、開発者とシステム管理者は、クラウドのプロジェクトと環境の管理、ルーチンの実行、自動化タスクの実行を行うことができます。 The `magento-cloud` CLI は、 [[!DNL Cloud Console]](../../get-started/cloud-console.md). インストール後、 `magento-cloud` ローカルワークステーションの CLI を使用して、クラウドインフラストラクチャの Starter および Pro 統合環境上でAdobe Commerceを管理できます。

**をインストールするには、以下を実行します。 `magento-cloud` CLI**:

1. ローカルワークステーションで、クラウドプロジェクトを複製するディレクトリと、 [ファイルシステム所有者](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/prerequisites/file-system/configure-permissions.html) 次に該当 _write_ にアクセスします。

1. をインストールします。 `magento-cloud` CLI。

   ```bash
   curl -sS https://accounts.magento.cloud/cli/installer | php
   ```

1. 追加 `magento-cloud` bash プロファイルへの CLI。

   ```bash
   export PATH=$PATH:$HOME/.magento-cloud/bin
   ```

1. 更新された bash プロファイルをリロードします。

   ```bash
   . ~/.bash_profile
   ```

1. CLI を起動するには、を呼び出します。 `magento-cloud` をクリックし、指示に従ってクラウドアカウントの資格情報を入力します。

   ```bash
   magento-cloud
   ```

   ```terminal
   Welcome to Magento Cloud!
   Please log in using your Magento Cloud account.
   Your email address or username:
   ```

1. を確認します。 `magento-cloud` コマンドがパスに含まれています。 次の例では、使用可能なコマンドを示します。

   ```bash
   magento-cloud list
   ```

## 共通コマンド

Adobeは、クラウド統合環境を管理するためにこれらのコマンドを設計し、 `magento-cloud` プロジェクトディレクトリから CLI を削除して、 `-p <project-ID>` パラメーター。

次に、一般的に使用されるのリストを示します `magento-cloud` CLI コマンドには、必要なオプションのみが含まれます。 以下を使用すると、 `--help` オプションを使用して、詳細を確認できます。

| コマンド | 説明 |
| ------------------------------------ | -------------------------------------------------- |
| `magento-cloud login` | プロジェクトにログインします。 |
| `magento-cloud list` | CLI ツールで使用可能なコマンドの一覧を示します。 |
| `magento-cloud environment:list` | 現在のプロジェクトの環境の一覧を表示します。 |
| `magento-cloud environment:checkout` | 既存の環境をチェックアウトします。 |
| `magento-cloud environment:merge -e` | この環境の変更を親とマージします。 |
| `magento-cloud variables` | この環境の変数をリストします。 |
| `magento-cloud ssh` | SSH を使用してリモート環境に接続します。 |
| `magento-cloud url` | ブラウザーでAdobe Commerceストアフロントを開きます。 |
| `magento-cloud web` | を開きます。 [!DNL Cloud Console]. |

## 環境コマンド

環境 _名前_ 環境とは異なる _ID_ 環境名にスペース文字または大文字を使用する場合にのみ有効です。 環境 ID は、すべて小文字、数字、使用可能な記号で構成されます。 環境名の大文字は ID では小文字に変換され、環境名のスペースはダッシュに変換されます。

環境名 _できません_ Linux シェル用または正規表現用に予約された文字を含めます。 使用不可の文字には中括弧 (`{ }`)，括弧，アスタリスク (`*`)，山括弧 (`< >`), ampersand (`&`), % (`%`)、およびその他の文字。

The `magento-cloud environment:list` コマンドは環境階層を表示しますが、 `git branch` は実行しません。 ネストされた環境がある場合は、次を使用します。

```bash
magento-cloud environment:list
```

### 環境の再デプロイ

プッシュを使用せずに再デプロイメントをトリガー。 再デプロイする環境を確認し、確認します。 保留中の状態のビルドがある場合は、再デプロイを使用しないでください。

```bash
magento-cloud environment:redeploy
```

レスポンスのサンプル：

```terminal
Are you sure you want to redeploy the environment <environment-name>? [Y/n]
```

{{redeploy-warning}}

## Git コマンド

これらのコマンドの一部は Git コマンドに似ています。 The `magento-cloud` コマンドは、追加の機能を使用して、Git ベースの Cloud プロジェクトに直接接続します。 ブランチを作成する場合、 `magento-cloud` CLI は「アクティブ化」されず、リモート環境に変更をプッシュしても自動的にはビルドされません。 The `magento-cloud` CLI コマンドは、アクティベーションを含みます。

ブランチを作成するには、 `magento-cloud` コマンドを使用して、ブランチをアクティブにします。

```bash
magento-cloud environment:branch <new-name> <parent-branch>
```

ブランチのステータスの場合：

- 以下を使用します。 `magento-cloud env` コマンドを使用して、環境ブランチとそのステータス（アクティブまたは非アクティブ）のリストを表示します。
- 以下を使用します。 `magento-cloud environment:activate` コマンドを使用して、環境ブランチをアクティブにします。

空の Git コミットをプッシュして、デプロイメントをトリガーにします。 例：

```bash
git commit --allow-empty -m "redeploy" && git push <branch-name>
```

ユーザーの追加など、一部のアクションはデプロイメントには影響しません。

### 環境ブランチの作成

以下の手順は、CLI コマンドと Git コマンドを同じ意味で使用してローカル環境を管理する方法を示しています。

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. 次に切り替え： [ファイルシステム所有者](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/prerequisites/file-system/configure-permissions.html).

1. プロジェクトにログインします。

   ```bash
   magento-cloud login
   ```

1. プロジェクトをリストします。

   ```bash
   magento-cloud project:list
   ```

1. プロジェクト内の環境のリストを表示します。 すべての環境には、コード、データベース、環境変数、設定およびサービスを含むアクティブな Git ブランチが含まれています。

   ```bash
   magento-cloud environment:list
   ```

   >[!NOTE]
   >
   >これは、 `magento-cloud environment:list` コマンドを使用するのは、環境階層を表示するのに対して、 `git branch` コマンドは実行しません。

1. 最新のコードを取得するには、接触チャネルブランチを取得します。

   ```bash
   git fetch origin
   ```

1. 特定のブランチと環境をチェックアウトまたは切り替えます。

   ```bash
   magento-cloud environment:checkout <environment-ID>
   ```

   Git コマンドは、Git ブランチのみをチェックアウトします。 The `magento-cloud checkout` コマンドはブランチをチェックアウトし、アクティブな環境に切り替えます。

   >[!TIP]
   >
   >環境ブランチは、 `magento-cloud environment:branch <environment-name> <parent-environment-ID>` コマンド構文を使用します。 環境ブランチの作成とアクティブ化には、さらに時間がかかる場合があります。

1. 環境 ID を使用して、更新されたコードをローカルに取り込みます。 環境ブランチが新規の場合は、この操作は必要ありません。

   ```bash
   git pull origin <environment-ID>
   ```

1. (_オプション_) を作成します。 [スナップショット](../storage/snapshots.md) バックアップとしての環境の

   ```bash
   magento-cloud snapshot:create -e <environment-ID>
   ```

## CLI を更新する

The `magento-cloud` CLI はログイン時に利用可能な更新をチェックしますが、 `self:update` コマンドを使用します。 利用可能な更新がある場合は、指示に従って CLI を更新します。

次の場合、 `magento-cloud` CLI は最新の状態です。次の応答が表示されます。

```bash
magento-cloud update
```

```terminal
Checking for Magento Cloud CLI updates (current version: X.XX.X)
No updates found
```
