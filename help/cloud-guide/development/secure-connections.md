---
title: 接続の保護
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

# リモート環境への接続を保護

Secure Shell(SSH) は、リモートサーバーやシステムに安全にログインするために使用される一般的なプロトコルです。 SSH を使用してリモート環境にアクセスし、Adobe Commerceアプリケーションの管理とリモート環境ログへのアクセスを行うことができます。 Adobeは、SSH 公開鍵を使用したセキュア FTP(sFTP) 接続のみをサポートします。 FTP 接続はサポートされていません。

## SSH キーペアの生成

プロジェクトのソースコードと環境へのアクセスを必要とするすべてのマシンとワークスペースで SSH キーのペアを作成します。 SSH キーを使用すると、GitHub に接続してソースコードを管理し、クラウドサーバーに接続する際に、ユーザー名とパスワードを常に入力する必要がなくなります。 詳しくは、 [SSH での GitHub への接続](https://docs.github.com/en/authentication/connecting-to-github-with-ssh) を参照してください。

- The _公開鍵_ は、サイト、SSH、sFTP へのアクセスに安全に提供します。
- The _秘密鍵_ は、ワークステーション上で非公開のままになります。

>[!CAUTION]
>
>**秘密鍵は共有しないでください。** チケットに追加したり、チャットにコピーしたり、メールに添付したりしないでください。

## アカウントへの SSH 公開鍵の追加

クラウドインフラストラクチャ上のAdobe Commerceアカウントに SSH 公開鍵を追加したら、アカウントにすべてのアクティブな環境を再デプロイして、その鍵をインストールします。

SSH キーは、 Cloud CLI または [!DNL Cloud Console].

>[!BEGINTABS]

>[!TAB CLI]

### Cloud CLI を使用した SSH キーの追加

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. プロジェクトにログインします。

   ```bash
   magento-cloud login
   ```

1. 公開鍵を追加します。

   ```bash
   magento-cloud ssh-key:add ~/.ssh/id_rsa.pub
   ```

>[!TIP]
>
>Cloud CLI コマンドを使用して、SSH キーをリストおよび削除できます `ssh-key:list` および `ssh-key:delete`.

>[!TAB コンソール]

### を使用して SSH キーを追加する [!DNL Cloud Console]

**新しいプロジェクトに SSH キーを追加するには**:

1. にログインします。 [[!DNL Cloud Console]](https://console.adobecommerce.com).

1. クリック **[!UICONTROL No SSH key]**. このアイコンは、コマンドフィールドの右側にあり、プロジェクトに SSH キーが含まれていない場合に表示されます。

1. SSH 公開鍵の内容を、 **公開鍵** フィールドに入力します。

1. 残りのプロンプトに従います。

**SSH キーをクラウドプロファイルに追加するには**:

1. にログインします。 [[!DNL Cloud Console]](https://console.adobecommerce.com).

1. 右上のアカウントメニューで、 **マイプロファイル**.

1. Adobe Analytics の _SSH キー_ 表示、クリック **公開鍵を追加**.

1. Adobe Analytics の _SSH キーの追加_ フォーム、キーを入力 **タイトル**&#x200B;をクリックし、 **キー** フィールドに入力します。

1. クリック **保存**.

>[!ENDTABS]

## リモート環境に接続

リモート環境に接続するには、 `magento-cloud` CLI または SSH コマンド。 The `magento-cloud` CLI コマンドは、Starter と Pro の統合環境でのみ使用できます。

### Cloud CLI を使用する

**リモート統合環境にログインするには**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. そのプロジェクト内の環境のリストを表示します。

   ```bash
   magento-cloud environment:list -p <project-ID>
   ```

1. SSH を使用してリモート環境にログインします。

   ```bash
   magento-cloud ssh -p <project-ID> -e <environment-ID>
   ```

### SSH コマンドを使用する

The [!DNL Cloud Console] には、各環境の Web および SSH アクセスコマンドのリストが含まれています。

**SSH コマンドをコピーするには**:

1. にログインします。 [[!DNL Cloud Console]](https://console.adobecommerce.com).

1. 次の場所からプロジェクトを選択： _すべてのプロジェクト_ リスト。

1. 環境を選択します。

1. クリック **[!UICONTROL SSH]**.

1. Adobe Analytics の _SSH_ 「 」タブの「コピー」ボタンをクリックして、完全な SSH コマンドをクリップボードにコピーします。

1. ターミナルを開き、SSH コマンドを貼り付けて接続を作成します。

   ```bash
   ssh abcdefg123abc-branch-a12b34c--mymagento@ssh.us-2.magento.cloud
   ```

>[!TIP]
>
>Pro のステージング環境および実稼動環境では、SSH コマンドは次のようになります。
>
>```bash
>ssh <node>.ent-<project-ID>-<environment>-<user-ID>@ssh.<region>.magento.com
>```

## sFTP

クラウドインフラストラクチャ上のAdobe Commerceは、SSH 認証を使用した SFTP（セキュア FTP）による環境へのアクセスをサポートします。 SFTP で SSH キー認証をサポートするクライアントを使用し、SSH 公開鍵を使用します。 ターゲット環境に SSH 公開鍵を追加する必要があります。 スターター環境と Pro 統合環境では、次の操作を実行できます。 [を通じて追加する [!DNL Cloud Console]](#add-your-ssh-key-using-the-project-web-interface).

読み取り専用の sFTP 接続は、 _not_ サポート。sFTP アクセスは、 _write_ デフォルトでは、権限です。

SFTP を設定する場合は、SSH アクセス環境コマンドの情報を使用します。 `<project-id>-<environment-id>--<app-name>@ssh<cloud-host>`

- **ユーザー名**: `@` 」をクリックします。
- **パスワード**:SFTP のパスワードは必要ありません。 sFTP アクセスでは、SSH キー認証を使用します。
- **ホスト**: `@` （SSH アクセス）を使用します。
- **ポート**:22（デフォルトの SSH ポート）。
- **SSH** 秘密鍵：必要に応じて、秘密鍵の場所を sFTP クライアントに指定します。 デフォルトでは、秘密鍵は `~/.ssh` ディレクトリ。

クライアントによっては、SFTP の SSH 認証を完了するために追加のオプションが必要になる場合があります。 選択したクライアントのドキュメントを確認します。

の場合 **スターター環境と Pro 統合環境**&#x200B;を使用する場合、 [追加， `mount`](../application/properties.md#mounts) 特定のディレクトリにアクセスする場合。 マウントを `.magento.app.yaml` ファイル。 書き込み可能なディレクトリのリストについては、 [プロジェクト構造](../project/file-structure.md). このマウントポイントは、これらの環境でのみ機能します。

の場合 **プロステージング環境と実稼動環境**( 環境への SSH アクセス権がない場合、 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) ：特定のフォルダーへの sFTP アクセスおよびマウントポイントをリクエストする場合（例： ） `pub/media`.

>[!NOTE]
>Pro のステージング環境および実稼動環境の場合、sFTP 接続が _汎用_ 次を行うユーザー **not** ～する必要がある [クラウドプロジェクトに追加されました](../project/user-access.md)を選択し、 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) と一緒に **公開** キーが添付されています。 **SSH 秘密鍵は指定しないでください。**

## SSH トンネリング

SSH トンネリングを使用すると、ローカル開発環境からサービスに接続する際に、サービスがローカルの場合と同じように設定できます。 トンネリングをおこなう前に、 [SSH](#add-an-ssh-public-key-to-your-account).

ターミナルアプリケーションを使用してログインし、コマンドを発行します。

```bash
magento-cloud login
```

を使用してトンネルが開いているかどうかを確認します。

```bash
magento-cloud tunnel:list
```

トンネルを構築するには、 [アプリケーション名](../application/properties.md#name). CLI を使用してアプリケーション名を確認できます。

```bash
magento-cloud apps
```

### SSH トンネルの設定

```bash
magento-cloud tunnel:open -e <environment-ID> --app <app-name>
```

例えば、次の場所へのトンネルを開くには、 `sprint5` という名前のアプリを使用してプロジェクト内でブランチ `mymagento`，と入力します。

```bash
magento-cloud tunnel:open -e sprint5 --app mymagento
```

レスポンスのサンプル：

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

### サービスに接続

SSH トンネルを確立した後は、ローカルで実行しているかのようにサービスに接続できます。 たとえば、データベースに接続するには、次のコマンドを使用します。

```bash
mysql --host=127.0.0.1 --user='<database-username>' --pass='<user-password>' --database='<name>' --port='<port>'
```
