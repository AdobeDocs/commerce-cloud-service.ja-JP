---
title: SSH アクセス用の多要素認証の有効化
description: クラウドインフラストラクチャ環境でのAdobe Commerceへの SSH アクセスに関する認証要件を管理する方法について説明します。
feature: Cloud, Security
topic: Security
exl-id: 754b2c22-f197-49be-a699-fb3bedf053fc
source-git-commit: ec1e59c3aafae6452ad1590fdb9de37c68b94ed9
workflow-type: tm+mt
source-wordcount: '1051'
ht-degree: 0%

---

# SSH アクセス用の多要素認証の有効化

セキュリティを強化するため、Adobe Commerce on cloud infrastructure は、Cloud 環境への SSH アクセスに関する認証要件を管理するために、多要素認証 (MFA) の実施を提供します。

プロジェクトで MFA が有効になっている場合、SSH アクセスを使用するすべてのユーザーアカウントで環境にアクセスするには、2 要素認証 (TFA) コードか API トークンと SSH 証明書のどちらかが必要です。

>[!NOTE]
>
>MFA は、Cloud プロジェクトではデフォルトで有効になっていません。 クラウドインフラストラクチャ上のAdobe Commerceプロジェクトのアカウント所有者 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) をクリックして有効にします。 MFA が有効になっている場合、プロジェクト環境への SSH アクセスには、すべてのユーザーがクラウドインフラストラクチャ上のAdobe Commerceアカウントで 2 要素認証 (TFA) を有効にする必要があります。

## SSH アクセス用の証明書

MFA を使用すると、ユーザーは Auth Cloud Certifier API で生成された短時間のみ有効な SSH 証明書と OAUTH アクセストークンをAdobeで交換できます。 ユーザーが管理者または貢献者の役割、有効な SSH キー、有効な TFA コードまたは API トークンを持っている場合、クラウドインフラストラクチャ上のAdobe Commerceは、これらの資格情報を使用して一時的な SSH 証明書を生成します。 証明書の有効期限は 1 時間に設定されますが、現在のセッション中に自動的に更新されます。

MFA を使用してプロジェクトにログインした後、ユーザーは `magento-cloud` SSH 証明書を生成する CLI:

```bash
magento-cloud ssh-cert:load
```

The `ssh-cert:load` コマンドは、SSH 証明書を生成し、ローカルユーザーの SSH エージェントにインストールします。

### ログイン時に証明書を自動生成する

に対する認証時に SSH 証明書が自動的に生成されるように、ローカル環境を設定できます。 `magento-cloud` CLI。

**SSH 証明書の自動生成を `magento-cloud` CLI 設定**:

1. ローカルワークステーションで、という名前のファイルを作成します。 `config.yaml` （内） `.magento-cloud` フォルダが存在しない場合は、ホームディレクトリ内のフォルダ。

   ```bash
   touch ~/.magento-cloud/config.yaml
   ```

1. 次の設定を `config.yaml` ファイル。

   ```yaml
   api:
      auto_load_ssh_cert: true
   ```

1. 以下を使用します。 `magento-cloud` 再度認証する CLI:

   >ログアウト：

   ```bash
   magento-cloud logout
   ```

   >ログイン：

   ```bash
   magento-cloud login
   ```

   >応答に従います。

   ```terminal
   Please open the following URL in a browser and log in:
   http://127.0.0.1:5000
   
   Help:
     Leave this command running during login.
     If you need to quit, use Ctrl+C.
   
     To log in using an API token, run: magento-cloud auth:api-token-login
   
   Login information received. Verifying...
   You are logged in.
   
   Generating SSH certificate...
   A new SSH certificate has been generated.
   It will be automatically refreshed when necessary.
   The certificate is included in your SSH configuration: /Users/<user-name>/.ssh/config
   ```

## TFA での SSH を使用した環境への接続

プロジェクトで MFA が有効になっている場合、SSH を使用してリモート環境に接続する前に、アカウントで TFA を有効にしておく必要があります。 詳しくは、 [TFA を有効にする](user-access.md#enable-tfa-for-cloud-accounts).

>[!BEGINSHADEBOX]

**前提条件：**

MFA の施行で有効になったプロジェクトの場合、SSH アクセスには次の権限とアカウント設定が必要です。

- [環境への管理者または寄稿者のアクセス](user-access.md)
- [アカウントに設定された SSH アクセスキー](../development/secure-connections.md#add-an-ssh-public-key-to-your-account)
- [アカウントで TFA が有効になっています](user-access.md#enable-tfa-for-cloud-accounts)

>[!ENDSHADEBOX]

**SSH を使用して TFA ユーザーアカウント資格情報で接続するには**:

1. にログインします。 [お使いのアカウント](https://console.adobecommerce.com).

1. ローカルのワークステーションで、 `magento-cloud` SSH 証明書を生成する CLI。

   ```bash
   magento-cloud ssh-cert:load
   ```

   > レスポンスのサンプル：

   ```terminal
   Generating SSH certificate...
     Expires at: 2020-07-13T15:28:13-04:00
     Multi-factor authentication: verified
     Mode: interactive
   The certificate will be automatically refreshed when necessary.
   Checking SSH configuration file: /Users/<user-name>/.ssh/config
   Do you want to update the file automatically? [Y/n] Y
   Configuration file updated successfully: /Users/<user-name>/.ssh/config
   ```

1. SSH を使用してリモート環境に接続します。

   ```bash
   ssh abcdef7uyxabce-master-7rqtwti--mymagento@ssh.us-5.magento.cloud
   ```

   ```terminal
    __  __                   _          ___ _             _
   |  \/  |__ _ __ _ ___ _ _| |_ ___   / __| |___ _  _ __| |
   | |\/| / _` / _` / -_) ' \  _/ _ \ | (__| / _ \ || / _` |
   |_|  |_\__,_\__, \___|_||_\__\___/  \___|_\___/\_,_\__,_|
               |___/
   
    Welcome to Magento Cloud.
   
    This is environment master-7rqtwti
    of project abcdef7uyxabce.
   
   web@mymagento.0:~$
   ```

## TFA での SSH を使用したソースコードの管理

クラウドインフラストラクチャプロジェクト上のAdobe Commerceのソースコードを管理する場合は、SSH を使用して、プロジェクトの Git リポジトリに対する認証をおこないます。 プロジェクトで MFA の強制が有効になっている場合、Git リポジトリを使用してコマンドライン操作を実行する前に、SSH 証明書を生成する必要があります。

**SSH を使用して TFA ユーザーアカウント資格情報で接続するには**:

1. にログインします。 [お使いのアカウント](https://console.adobecommerce.com) TFA を使用して認証をおこないます。

   >[!NOTE]
   >
   >アカウントで TFA を有効にしていない場合は、有効にする必要があります。 詳しくは、 [クラウドアカウントでの TFA の有効化](user-access.md#enable-tfa-for-cloud-accounts).

1. ローカルのワークステーションで、 `magento-cloud` SSH 証明書を生成する CLI。

   ```bash
   magento-cloud ssh-cert:load
   ```

   > レスポンスのサンプル：

   ```terminal
   Generating SSH certificate...
     Expires at: 2020-07-13T15:28:13-04:00
     Multi-factor authentication: verified
     Mode: interactive
   The certificate will be automatically refreshed when necessary.
   Checking SSH configuration file: /Users/<user-name>/.ssh/config
   Do you want to update the file automatically? [Y/n] Y
   Configuration file updated successfully: /Users/<user-name>/.ssh/config
   ```

1. プロジェクト環境用に Git リポジトリを複製します。

   ```bash
   git clone --branch integration abcdef7uyxabce@git.us-3.magento.cloud:abcdef7uyxabce.git myproject
   ```

   > レスポンスのサンプル：

   ```terminal
   Cloning into 'myproject'...
   Connection to git.us-3.magento.cloud port 22 [tcp/ssh] succeeded!
   remote: counting objects: 22, done.
   Receiving objects: 100% (22/22), 82.42 KiB | 16.48 MiB/s, done.
   ```

## API トークンを使用した SSH 環境への接続

プロジェクトで MFA が有効になっている場合、クラウド環境への SSH アクセスを必要とする自動プロセスには API トークンが必要です。 プロジェクトの管理者または寄稿者アクセス権を持つ、クラウドインフラストラクチャ上のAdobe Commerceアカウントからトークンを生成できます。

API トークンを使用した認証では、SSH 証明書の生成が必要です。 自動化プロセスは、SSH 証明書の生成も自動化する必要があります。

>[!BEGINSHADEBOX]

**前提条件：**

- [クラウドインフラストラクチャ環境上のAdobe Commerceへの管理者または寄稿者のアクセス権](user-access.md)
- [有効な API トークンがアカウントで使用可能です](user-access.md#create-an-api-token)

>[!ENDSHADEBOX]

**API トークンの資格情報で SSH を使用して接続するには**:

1. API キー認証を使用してクラウドプロジェクトにログインします。

   ```bash
   magento-cloud auth:api-token
   ```

1. プロンプトで、有効な API トークンの値を入力します。

   ```terminal
   Please enter an API token:
   >
   
   The API token is valid.
   You are logged in.
   ```

### 例：自動 SSH スクリプト

API トークンを保存する方法は 2 つあります。

>[!NOTE]
>
>API トークンが保存されている場合、 `magento-cloud` CLI は自動的に認証を行い、 `magento-cloud login` コマンドを使用します。

**オプション 1**:API トークンを保存するための環境変数を作成します。

bash_profile にトークンを書き込む

```bash
echo "export MAGENTO_CLOUD_CLI_TOKEN=<your api token>" >> ~/.bash_profile
```

**オプション 2**：にトークンを追加します。 `config.yaml` ファイル

1. ローカルワークステーションで、という名前のファイルを作成します。 `config.yaml` （内） `.magento-cloud` フォルダが存在しない場合は、ホームディレクトリ内のフォルダ。

   ```bash
   touch ~/.magento-cloud/config.yaml
   ```

1. 次の設定を `config.yaml` ファイル。

   ```yaml
   api:
      token: <your api token>
   ```

>bash スクリプトのサンプル

```shell
#!/bin/bash
magento-cloud ssh-cert:load
ssh abcdef7uyxabce-master-7rqtabc--mymagento@ssh.us-3.magento.cloud "tail -n 10 ~/var/log/cloud.log"
```

## トラブルシューティング

次の情報を使用して、次のような認証エラーによる SSH 接続要求の失敗を解決します。 `access requires MFA` または `permission denied`.

### 要求に有効な証明書が指定されていません

要求で有効な証明書が提供されない場合は、次のようなメッセージが表示されます。

```terminal
to Hello user-test (UUID: abaacca12-5cd1-4b123-9096-411add578998), you successfully
authenticated, but could not connect to service abcdef7uyxabce-master-7rqtabc--mymagento@ssh.us-3.magento.cloud:>
(reason: access requires MFA)
```

接続の問題を解決するには、次のトラブルシューティング手順を試してください。

- アカウント TFA 設定の確認
- 再認証し、証明書を再読み込みします

**TFA 設定と認証を検証するには、以下を実行します。**:

1. にログインします。 [お使いのアカウント](https://console.adobecommerce.com).

1. 右上のアカウントメニューで、 **[!UICONTROL My Profile]**.

1. 次の日： _マイプロファイル_ ページで、 **[!UICONTROL Security]** タブをクリックします。

   TFA が有効な場合、「セキュリティ」セクションに TFA 設定を管理するオプションが表示されます。

1. TFA が設定されていない場合は、 **[!UICONTROL Set up application]** 指示に従って有効にします。 詳しくは、 [TFA を有効にする](user-access.md#enable-tfa-for-cloud-accounts).

1. TFA が設定されている場合は、もう一度認証をお試しください。

**SSH 証明書を認証して再読み込みするには**:

1. 以下を使用します。 `magento-cloud` 再度認証する CLI:

   ```bash
   magento-cloud logout
   ```

   ```bash
   magento-cloud login
   ```

1. SSH 証明書を再読み込みします。

   ```bash
   magento-cloud ssh-cert:load
   ```

### 権限が拒否されました

SSH キーが見つからないか無効な場合、SSH 接続リクエストは `Permission denied (publickey)` エラー。

```terminal
Hello user-test (UUID: abaacca12-5cd1-4b123-9096-411add578998), you successfully authenticated, but could not connect to service oh2wi6klp5ytk-mc-35985-integration-nnulm4a--mymagento (reason: service doesn't exist or you do not have access to it)
oh2wi6klp5ytk-mc-35985-integration-nnulm4a--mymagento@ssh.eu-3.magento.cloud: Permission denied (publickey).
```

問題を修正するには、現在のセッションに SSH キーを追加するか、SSH 設定ファイルを更新して SSH キーを自動的に読み込みます。 詳しくは、 [SSH 公開鍵の追加](../development/secure-connections.md#add-an-ssh-public-key-to-your-account).

### MFA のないプロジェクトにアクセスできません

多要素認証 (MFA) が有効なプロジェクトに対して認証をおこなうと、MFA を必要としない他のプロジェクトに接続する際に、次のエラーが表示される場合があります。

```bash
ssh abcdef7uyxabce-master-7rqtabc--mymagento@ssh.us-3.magento.cloud
```

レスポンスのサンプル：

```terminal
abcdef7uyxabce-master-7rqtabc--mymagento@ssh.us-3.magento.cloud: Permission denied (publickey).
```

SSH 証明書の生成時に、 `magento-cloud` CLI により、ローカル環境に SSH キーが追加されます。 ローカルの SSH 設定にプロジェクトアクセス用の SSH キーが含まれていない場合は、そのキーがデフォルトで使用されます。

**ローカル設定に SSH キーを追加するには**:

1. を作成します。 `config` ファイルが存在しない場合はファイル。

   ```bash
   touch ~/.ssh/config
   ```

1. を追加します。 `IdentityFile` 設定。

   ```yaml
   Host *
     IdentityFile ~/.ssh/id_rsa
   ```

>[!NOTE]
>
>複数の SSH キーを追加することで、複数の SSH キーを指定できます `IdentityFile` 設定へのエントリ。
