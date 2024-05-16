---
title: 安全な接続
description: クラウドインフラストラクチャプロジェクト上のAdobe Commerceに SSH キーを適用し、リモート環境にログインする方法を説明します。
role: Developer
feature: Cloud, Security
topic: Security
exl-id: b5780e8e-e3da-4b10-8ca3-2778085acd4a
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '976'
ht-degree: 0%

---

# リモート環境への安全な接続

Secure Shell （SSH）は、リモートサーバーおよびシステムに安全にログインするために使用される共通プロトコルです。 SSH を使用してリモート環境にアクセスし、Adobe Commerce アプリケーションを管理してリモート環境ログにアクセスできます。 Adobeでは、SSH 公開鍵を使用したセキュア FTP （sFTP）接続のみをサポートしています。 FTP 接続はサポートされていません。

## SSH キーペアの生成

プロジェクトのソースコードと環境にアクセスする必要があるすべてのマシンとワークスペースに、SSH キーペアを作成します。 SSH キーを使用すると、GitHub に接続してソースコードを管理したり、クラウドサーバーに接続したりできます。ユーザー名やパスワードを常に指定する必要はありません。 参照： [SSH を使用した GitHub への接続](https://docs.github.com/en/authentication/connecting-to-github-with-ssh) ssh キーペアの作成に関する詳細な手順。

- この _公開鍵_ は、サイト、SSH、sFTP にアクセスする場合に提供しても安全です。
- この _秘密鍵_ ワークステーションのプライベート状態を維持します。

>[!CAUTION]
>
>**秘密鍵は決して共有しないでください。** チケットに追加したり、チャットにコピーしたり、メールに添付したりしないでください。

## SSH 公開鍵をアカウントに追加

クラウドインフラストラクチャアカウント上のAdobe Commerceに SSH 公開鍵を追加した後、アカウント上のすべてのアクティブな環境を再デプロイして鍵をインストールします。

次のいずれかの方法を使用して、アカウントに SSH キーを追加できます。Cloud CLI または [!DNL Cloud Console].

>[!BEGINTABS]

>[!TAB CLI]

### Cloud CLI を使用して SSH キーを追加する

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. プロジェクトへのログイン

   ```bash
   magento-cloud login
   ```

1. 公開鍵を追加します。

   ```bash
   magento-cloud ssh-key:add ~/.ssh/id_rsa.pub
   ```

>[!TIP]
>
>Cloud CLI コマンドを使用して、SSH キーの一覧表示と削除を行うことができます `ssh-key:list` および `ssh-key:delete`.

>[!TAB コンソール]

### を使用して SSH キーを追加する [!DNL Cloud Console]

**新しいプロジェクトに SSH キーを追加するには**:

1. にログインします [[!DNL Cloud Console]](https://console.adobecommerce.com).

1. クリック **[!UICONTROL No SSH key]**. このアイコンはコマンドフィールドの右側にあり、プロジェクトに SSH キーが含まれていない場合に表示されます。

1. に SSH 公開鍵の内容をコピーして貼り付けます。 **公開鍵** フィールド。

1. 残りのプロンプトに従います。

**クラウドプロファイルに SSH キーを追加するには**:

1. にログインします [[!DNL Cloud Console]](https://console.adobecommerce.com).

1. 右上のアカウントメニューで、 **マイプロファイル**.

1. が含まれる _SSH キー_ 表示、クリック **公開鍵を追加**.

1. が含まれる _SSH キーの追加_ フォーム、キーを **タイトル**&#x200B;に、SSH 公開鍵を貼り付けます。 **キー** フィールド。

1. クリック **保存**.

>[!ENDTABS]

## リモート環境への接続

リモート環境に接続するには、 `magento-cloud` CLI または SSH コマンド。 この `magento-cloud` CLI コマンドは、Starter および Pro 統合環境でのみ使用できます。

### Cloud CLI の使用

**リモート統合環境にログインするには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. そのプロジェクト内の環境をリストします。

   ```bash
   magento-cloud environment:list -p <project-ID>
   ```

1. SSH を使用してリモート環境にログインします。

   ```bash
   magento-cloud ssh -p <project-ID> -e <environment-ID>
   ```

### SSH コマンドの使用

この [!DNL Cloud Console] 各環境の Web および SSH アクセス・コマンドのリストが含まれています。

**SSH コマンドをコピーするには、次の手順に従います**:

1. にログインします [[!DNL Cloud Console]](https://console.adobecommerce.com).

1. 「」からプロジェクトを選択 _すべてのプロジェクト_ リスト。

1. 環境を選択します。

1. クリック **[!UICONTROL SSH]**.

1. が含まれる _SSH_ タブをクリックし、「コピー」ボタンをクリックして、完全な SSH コマンドをクリップボードにコピーします。

1. ターミナルを開き、SSH コマンドを貼り付けて接続を作成します。

   ```bash
   ssh abcdefg123abc-branch-a12b34c--mymagento@ssh.us-2.magento.cloud
   ```

>[!TIP]
>
>ステージング環境および実稼動環境では、SSH コマンドは次のようになります。
>
>```bash
>ssh <node>.ent-<project-ID>-<environment>-<user-ID>@ssh.<region>.magento.com
>```

## sFTP

クラウドインフラストラクチャー上のAdobe Commerceでは、SSH 認証を使用した sFTP （セキュア FTP）を使用した環境へのアクセスをサポートしています。 sFTP 用の SSH キー認証をサポートするクライアントを使用し、SSH 公開キーを使用します。 SSH 公開鍵をターゲット環境に追加する必要があります。 スターター環境と Pro 統合環境の場合は、次のことができます [を使用して追加 [!DNL Cloud Console]](#add-your-ssh-key-using-the-project-web-interface).

読み取り専用の sFTP 接続 _ではない_ サポート。sFTP アクセスはで提供 _write_ 権限のデフォルト。

sFTP の設定時には、SSH アクセス環境コマンドからの情報を使用します。 `<project-id>-<environment-id>--<app-name>@ssh<cloud-host>`

- **ユーザー名**：次より前のすべてのコンテンツ `@` SSH アクセス先で行います。
- **パスワード**:sFTP 用のパスワードは必要ありません。 sFTP アクセスでは、SSH キー認証を使用します。
- **ホスト**：次の期間より後のすべてのコンテンツ `@` SSH アクセス権で。
- **ポート**: 22 （デフォルトの SSH ポート）。
- **SSH** 秘密鍵：必要に応じて、秘密鍵の場所を sFTP クライアントに指定します。 デフォルトでは、秘密鍵はに保存されます `~/.ssh` ディレクトリ。

クライアントによっては、sFTP の SSH 認証を完了するために、追加のオプションが必要になる場合があります。 選択したクライアントのドキュメントを確認します。

の場合 **スターター環境と Pro 統合環境**&#x200B;の場合も、次の点を考慮してください。 [の追加 `mount`](../application/properties.md#mounts) 特定のディレクトリにアクセスする場合。 マウントを `.magento.app.yaml` ファイル。 書き込み可能なディレクトリのリストについては、を参照してください [プロジェクト構造](../project/file-structure.md). このマウントポイントは、これらの環境でのみ機能します。

の場合 **ステージング環境と実稼動環境**&#x200B;環境に対する SSH アクセス権がない場合は、次の操作を行う必要があります [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) 特定のフォルダーにアクセスするために、sFTP アクセスおよびマウントポイントをリクエストします（例：）。 `pub/media`.

>[!NOTE]
>Pro ステージング環境および実稼動環境では、sFTP 接続が _汎用_ 実行するユーザー **ではない** ～である必要がある [がクラウドプロジェクトに追加されました](../project/user-access.md)は、次の操作が必要です [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) と共に **パブリック** キーが添付されました。 **SSH 秘密鍵を決して入力しないでください。**

## SSH トンネリング

SSH トンネリングを使用すると、ローカル開発環境からサービスに対して、サービスがローカルであるかのように接続できます。 トンネリングを行う前に、 [SSH](#add-an-ssh-public-key-to-your-account).

ターミナルアプリケーションを使用してログインし、コマンドを発行します。

```bash
magento-cloud login
```

を使用してトンネルが開いているかどうかを確認します。

```bash
magento-cloud tunnel:list
```

トンネルを構築するには、 [アプリケーション名](../application/properties.md#name). アプリケーション名は、CLI を使用して確認できます。

```bash
magento-cloud apps
```

### SSH トンネルのセットアップ

```bash
magento-cloud tunnel:open -e <environment-ID> --app <app-name>
```

たとえば、へのトンネルを開くには `sprint5` という名前のアプリを使用したプロジェクトのブランチ `mymagento`、と入力します

```bash
magento-cloud tunnel:open -e sprint5 --app mymagento
```

応答の例：

```terminal
SSH tunnel opened on port 30004 to relationship: redis
SSH tunnel opened on port 30005 to relationship: database
Logs are written to: /home/magento_user/.magento/tunnels.log

List tunnels with: magento-cloud tunnels
View tunnel details with: magento-cloud tunnel:info
Close tunnels with: magento-cloud tunnel:close
```

**トンネルに関する情報を表示するには**:

```bash
magento-cloud tunnel:info -e <environment-ID>
```

### サービスへの接続

SSH トンネルを確立すると、ローカルで実行されているかのようにサービスに接続できます。 例えば、データベースに接続するには、次のコマンドを使用します。

```bash
mysql --host=127.0.0.1 --user='<database-username>' --pass='<user-password>' --database='<name>' --port='<port>'
```
