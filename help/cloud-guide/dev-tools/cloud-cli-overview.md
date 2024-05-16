---
title: クラウド CLI
description: magento-cloud CLI の概要と、クラウドインフラストラクチャプロジェクト上のAdobe Commerceのローカル開発環境を管理する際にどう役立つかを説明します。
exl-id: 70dddd62-0269-4af4-bd2a-1a4fbf11a131
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '741'
ht-degree: 0%

---


# クラウド CLI

この `magento-cloud` CLI ツールを使用すると、開発者やシステム管理者は、クラウドのプロジェクトや環境の管理、ルーチンの実行、自動化タスクの実行が可能になります。 この `magento-cloud` CLI は、の機能を拡張します [[!DNL Cloud Console]](../../get-started/cloud-console.md). をインストールしたら、 `magento-cloud` ローカルワークステーションで CLI を使用すると、クラウドインフラストラクチャー上のAdobe Commerce Starter および Pro 統合環境を管理できます。

**をインストールするには `magento-cloud` CLI**:

1. ローカルワークステーションで、を、クラウドプロジェクトのクローンを作成するディレクトリに変更します。また、 [ファイルシステム所有者](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/prerequisites/file-system/configure-permissions.html) が _write_ アクセス。

1. のインストール `magento-cloud` CLI。

   ```bash
   curl -sS https://accounts.magento.cloud/cli/installer | php
   ```

1. 追加 `magento-cloud` bash プロファイルへの CLI。

   ```bash
   export PATH=$PATH:$HOME/.magento-cloud/bin
   ```

1. 更新した bash プロファイルを再読み込みします。

   ```bash
   . ~/.bash_profile
   ```

1. CLI を開始するには、を呼び出します。 `magento-cloud` プロンプトが表示されたら、クラウドアカウントの資格情報を入力します。

   ```bash
   magento-cloud
   ```

   ```terminal
   Welcome to Magento Cloud!
   Please log in using your Magento Cloud account.
   Your email address or username:
   ```

1. を確認 `magento-cloud` コマンドはパスにあります。 次の例は、使用可能なコマンドを示しています。

   ```bash
   magento-cloud list
   ```

## 一般的なコマンド

Adobeでは、これらのコマンドをクラウド統合環境を管理するために設計し、を実行することをお勧めします `magento-cloud` プロジェクトディレクトリから CLI を使用する。これにより、 `-p <project-ID>` パラメーター。

一般的に使用される次のリスト `magento-cloud` CLI コマンドには、必要なオプションのみが含まれます。 を使用できます `--help` オプションと任意のコマンドを使用すると、詳細を確認できます。

| コマンド | 説明 |
| ------------------------------------ | -------------------------------------------------- |
| `magento-cloud login` | プロジェクトにログインします。 |
| `magento-cloud list` | CLI ツールで使用可能なコマンドを一覧表示します。 |
| `magento-cloud environment:list` | 現在のプロジェクトの環境をリストします。 |
| `magento-cloud environment:checkout` | 既存の環境をチェックアウトします。 |
| `magento-cloud environment:merge -e` | この環境の変更をその親と結合します。 |
| `magento-cloud variables` | この環境のリスト変数。 |
| `magento-cloud ssh` | SSH を使用してリモート環境に接続します。 |
| `magento-cloud url` | ブラウザーでAdobe Commerce ストアフロントを開きます。 |
| `magento-cloud web` | を開きます [!DNL Cloud Console]. |

## 環境コマンド

環境 _名前_ は環境とは異なります _ID_ 環境名にスペースまたは大文字を使用する場合のみ。 環境 ID は、すべての小文字、数字、許可された記号で構成されます。 環境名の大文字は、ID では小文字に変換され、環境名のスペースはダッシュに変換されます。

環境名 _できません_ linux シェルまたは正規表現に予約されている文字を含めます。 禁止されている文字には中括弧（`{ }`）、括弧、アスタリスク（`*`）、山括弧（`< >`）、アンパサンド（`&`）,% （`%`）、およびその他の文字。

この `magento-cloud environment:list` コマンドは環境階層を表示しますが、 `git branch` しない。 ネストされた環境がある場合は、次を使用します。

```bash
magento-cloud environment:list
```

### 環境の再デプロイ

プッシュを使用せずに再デプロイメントをトリガーします。 再デプロイする環境を検証し、確認します。 保留状態のビルドがある場合は、再デプロイを使用しません。

```bash
magento-cloud environment:redeploy
```

応答の例：

```terminal
Are you sure you want to redeploy the environment <environment-name>? [Y/n]
```

{{redeploy-warning}}

## Git コマンド

これらのコマンドの一部は Git コマンドに似ていることに気付くかもしれません。 この `magento-cloud` コマンドは、追加機能を使用して Git ベースのクラウドプロジェクトに直接接続します。 を使用せずにブランチを作成する場合 `magento-cloud` CLI は「アクティブ化」されておらず、変更をリモート環境にプッシュしても自動的に構築されません。 この `magento-cloud` CLI コマンドにアクティブ化が含まれる。

ブランチを作成するには、 `magento-cloud` コマンドを実行すると、ブランチがアクティブになります。

```bash
magento-cloud environment:branch <new-name> <parent-branch>
```

分岐ステータスの場合：

- の使用 `magento-cloud env` 環境ブランチのリストとステータス（アクティブまたは非アクティブ）を表示するコマンド。
- の使用 `magento-cloud environment:activate` 環境ブランチをアクティブにするコマンド。

空の Git コミットをプッシュしてデプロイメントをトリガーにします。 例：

```bash
git commit --allow-empty -m "redeploy" && git push <branch-name>
```

ユーザーの追加など、一部のアクションではデプロイメントは発生しません。

### 環境ブランチの作成

次の手順は、CLI コマンドと Git コマンドを交互に使用してローカル環境を管理する方法を示しています。

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. に切り替え [ファイルシステム所有者](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/prerequisites/file-system/configure-permissions.html).

1. プロジェクトにログインします。

   ```bash
   magento-cloud login
   ```

1. プロジェクトのリストを作成します。

   ```bash
   magento-cloud project:list
   ```

1. プロジェクト内の環境をリストします。 すべての環境には、コード、データベース、環境変数、設定およびサービスを含んだアクティブな Git ブランチが含まれています。

   ```bash
   magento-cloud environment:list
   ```

   >[!NOTE]
   >
   >を使用することが重要です `magento-cloud environment:list` コマンドでは環境階層が表示されるのに対して、 `git branch` コマンドは実行しません。

1. オリジンブランチを取得して最新のコードを取得します。

   ```bash
   git fetch origin
   ```

1. 特定のブランチと環境をチェックアウトするか、切り替えます。

   ```bash
   magento-cloud environment:checkout <environment-ID>
   ```

   Git コマンドは、Git ブランチのみをチェックアウトします。 この `magento-cloud checkout` コマンドは、ブランチをチェックアウトし、アクティブな環境に切り替えます。

   >[!TIP]
   >
   >を使用して、環境ブランチを作成できます `magento-cloud environment:branch <environment-name> <parent-environment-ID>` コマンドの構文。 環境ブランチの作成とアクティブ化にはさらに時間がかかる場合があります。

1. 環境 ID を使用して、更新されたコードをローカルに取り込みます。 環境ブランチが新しい場合は、これは必要ありません。

   ```bash
   git pull origin <environment-ID>
   ```

1. （_オプション_）を作成します [スナップショット](../storage/snapshots.md) バックアップとしての環境の。

   ```bash
   magento-cloud snapshot:create -e <environment-ID>
   ```

## CLI の更新

この `magento-cloud` CLI は、ログイン時に利用可能な更新をチェックしますが、次を使用して更新をチェックできます `self:update` コマンド。 更新がある場合は、手順に従って CLI を更新します。

次の場合 `magento-cloud` CLI は最新の状態です。次の応答が表示されます。

```bash
magento-cloud update
```

```terminal
Checking for Magento Cloud CLI updates (current version: X.XX.X)
No updates found
```
