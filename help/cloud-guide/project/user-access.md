---
title: ユーザーアクセスを管理
description: クラウドインフラストラクチャのプロジェクトと環境でAdobe Commerceへのユーザーアクセスを管理する方法について説明します。
role: Admin
feature: Cloud, Roles/Permissions
last-substantial-update: 2023-06-27T00:00:00Z
topic: Security
exl-id: 3357a3ea-bf86-4a65-95d1-6b24f1152248
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '1441'
ht-degree: 0%

---

# ユーザーアクセスを管理

クラウドインフラストラクチャのAdobe Commerceプロジェクトでは、役割ベースのアクセスを使用します。 プロジェクトレベルでは、次の 2 つの役割を使用できます。

- **プロジェクト管理者** — すべてのプロジェクト環境への書き込みアクセス権と、ユーザーの管理、コードのプッシュ、およびプロジェクト設定の更新をおこなうことができます。
- **プロジェクトビューア** — すべてのプロジェクト環境への表示専用アクセス権。

プロジェクトビューアは、どの環境でもタスクを実行できません。ただし、プロジェクトビューアに特定の環境タイプに対する書き込みアクセス権を付与することはできます。

環境レベルのアクセスは、環境のタイプ（実稼動、ステージング、開発）に基づいています。 ユーザーの許可 _閲覧者_ ～に対する許可 _開発_ 環境とは、ユーザーが表示できることを意味します。 **すべて** プロジェクトの開発環境。 次の表に、各権限レベルに付与される機能を示します。

| 権限レベル | アクセス | SSH アクセス |
| ------------------ | ----------- | :----------: |
| **管理者** | 親環境とのマージを含む、設定の変更、プッシュコードの変更、タスクの実行、ブランチ管理などの管理者タスクを実行します。 | はい |
| **寄稿者** | コードをプッシュして環境を分岐します。設定を変更したりアクションを実行したりすることはできません | はい |
| **閲覧者** | 環境タイプへの表示のみアクセス | いいえ |
| **アクセスなし** | 環境タイプへのアクセス権がありません | いいえ |

{style="table-layout:auto"}

を使用して、ユーザーを追加したり、役割を割り当てたりできます。 `magento-cloud` CLI または [!DNL Cloud Console].

>[!BEGINSHADEBOX]

**前提条件：**

- Adobe IDの登録ユーザー。 ユーザーが [Adobe口座に登録する](https://account.adobe.com) その後 [クラウドアカウントを初期化する](https://console.adobecommerce.com) をクリックして、Cloud プロジェクトに追加できるようにします。
- ユーザーが **管理者** ロールは、 `magento-cloud` CLI。 次の権限を付与されたユーザーのみ： **アカウント所有者** の役割はユーザーを管理できます。

>[!ENDSHADEBOX]

## CLI を使用してユーザーを管理する

以下を使用します。 `magento-cloud` ユーザーを管理し、自動システムと統合する CLI:

- `magento-cloud user:add` — プロジェクトにユーザーを追加
- `magento-cloud user:delete` — ユーザーの削除
- `magento-cloud user:list [users]`プロジェクトユーザーのリスト
- `magento-cloud user:role` — ユーザーの役割を表示または変更します
- `magento-cloud user:update` — プロジェクトのユーザーの役割を更新

次の例では、 `magento-cloud` ユーザーの追加、役割の設定、プロジェクト割り当ての変更、ユーザーの役割の割り当てをおこなう CLI。

**ユーザーを追加し、役割を割り当てるには**:

1. 以下を使用します。 `magento-cloud` ユーザーを追加する CLI。

   ```bash
   magento-cloud user:add
   ```

   >[!IMPORTANT]
   >
   >ユーザーがAdobe IDを持っている。 [前提条件](#add-users-and-manage-access).

1. プロンプトに従って、ユーザーの電子メールアドレスを指定し、プロジェクトと環境タイプの役割を設定し、ユーザーを追加します。

   > サンプルプロンプト

   ```terminal
   Enter the user's email address: alice@example.com
   
   Email address: alice@example.com
   
   The user's project role can be admin (a) or viewer (v).
   
   Project role (default: viewer) [a/v]: viewer
   
   The user's environment type role(s) can be admin (a), viewer (v), contributor (c) or none (n).
   
   Role on type development (default: none) [a/v/c/n]: none
   Role on type production (default: none) [a/v/c/n]: admin
   Role on type staging (default: none) [a/v/c/n]: admin
   
   Adding the user alice@example.com to (project_id):
   Project role: viewer
     Role on type production: admin
     Role on type staging: admin
   
   Are you sure you want to add this user? [Y/n] y
   Adding the user to the project
   ```

   ユーザーを追加すると、Adobeは、クラウドインフラストラクチャプロジェクト上のAdobe Commerceにアクセスするための手順と共に、指定したアドレスに E メールを送信します。

### ユーザーのプロジェクトの役割の表示

```bash
magento-cloud user:get alice@example.com
```

>レスポンスのサンプル：

```terminal
Current role(s) of User (alice@example.com) on Production (project_id):
  Project role: admin
```

### 複数の環境へのユーザーの追加

ユーザーを `viewer` の `Production` 環境、およびとして `contributor` の `Integration` 環境：

```bash
magento-cloud user:add alice@example.com -r production:v -r integration:c
```

### ユーザー環境の権限を更新

ユーザー環境の権限をに更新するには： `admin` の `Production` 環境：

```bash
magento-cloud user:update alice@example.com -r production:a
```

## ユーザーを [!DNL Cloud Console]

以下を使用すると、 [[!DNL Cloud Console]](../../get-started/cloud-console.md) 権限を追加して _編集_ 既存のユーザーの権限を変更する機能です。

>[!IMPORTANT]
>
>ユーザーがAdobe IDを持っている。 [前提条件](#add-users-and-manage-access).

### プロジェクトにユーザーを追加する

1. にログインします。 [[!DNL Cloud Console]](https://console.adobecommerce.com/).

1. 次の場所からプロジェクトを選択： _すべてのプロジェクト_ リスト。

1. 「プロジェクト」ダッシュボードで、右上の設定アイコンをクリックします。

1. の下 _プロジェクト設定_&#x200B;をクリックし、 **[!UICONTROL Access]**.

1. Adobe Analytics の _アクセス_ 表示、クリック **[!UICONTROL Add]**.

1. 次を完了： _[!UICONTROL Add User]_フォーム：

   - ユーザーの電子メールアドレスを入力します。

   - **[!UICONTROL Project admin]** — すべての設定と環境タイプに管理者権限を付与します。

   - **[!UICONTROL Environment types and permissions]** — 特定の環境タイプに対して、アクセスレベルと特定の権限レベルを付与します。 _アクセスなし_, _管理者_ （設定の変更、アクションの実行、コードの結合）、 _寄稿者_ （プッシュコード）または _閲覧者_ （表示のみ）。

   >[!TIP]
   >
   >a のみ **プロジェクト管理者** は、どの環境でもユーザーを管理できます。 ユーザーに **アクセス** タブ、別の **プロジェクト管理者** または **アカウント所有者** は、そのユーザーを割り当てる必要があります **プロジェクト管理者** 役割。

1. クリック **[!UICONTROL Add User]**.

1. ユーザーを追加した後、すべての環境を再デプロイして、変更を適用します。 ユーザーを追加しても、デプロイメントのトリガーは自動的には行われません。 再デプロイは、ユーザーが SSH を使用して環境に確実にアクセスできるようにするための重要な手順です。

ユーザーを追加すると、Adobeは、クラウドインフラストラクチャプロジェクト上のAdobe Commerceにアクセスするための手順と共に、指定したアドレスに E メールを送信します。

## ユーザー認証の要件

セキュリティの強化のため、Adobeは、クラウドインフラストラクチャのプロジェクトのソースコードおよび環境で、Adobe Commerceに SSH でアクセスするために 2 要素認証 (TFA) を必要とするプロジェクトレベルの MFA(Multi-Factor Authentication) の適用を提供します。 詳しくは、 [SSH 用 MFA の有効化](multi-factor-authentication.md).

MFA の実施がクラウドインフラストラクチャプロジェクト上のAdobe Commerceで有効になっている場合、そのプロジェクト内の環境に SSH でアクセスするすべてのユーザーは、クラウドインフラストラクチャ上のAdobe Commerceのアカウントで TFA を有効にする必要があります。 自動化されたプロセスの場合は、コマンドラインから認証するマシンユーザーと API トークンを作成できます。

クラウドプロジェクトにユーザーを追加した後、ユーザーに自分のアカウントのセキュリティ設定を確認し、必要に応じて次のセキュリティ設定を追加するように依頼します。

- **TFA を有効にする**- 2 要素認証を構成することで、セキュリティとコンプライアンスの標準に準拠します。 で構成されたプロジェクト [MFA の適用](multi-factor-authentication.md) プロジェクトにアクセスするために SSH を使用するアカウントでは、TFA が必要です。

- **SSH キーを有効にする** — クラウドインフラストラクチャのソースコードリポジトリー上のAdobe Commerceへのアクセスを必要とするユーザーは、自分のアカウントで SSH キーを有効にする必要があります。 詳しくは、 [接続の保護](../development/secure-connections.md).

- **API トークンの作成** — ユーザーは、環境への SSH アクセスに使用する API トークンを生成する必要があります。 自動化されたプロセスで認証ワークフローを有効にするには、トークンが必要です。

  MFA 強制が有効なプロジェクトでは、API トークンを使用して、自動アカウントから SSH アクセスリクエストを認証する必要があります。 トークンを使用すると、自動化されたプロセスが、TFA を必要とする認証ワークフローをバイパスできます。

### クラウドアカウントに対する TFA の有効化

クラウドインフラストラクチャ上のAdobe Commerceは、次のアプリケーションのいずれかを使用して TFA をサポートします。

- [Google Authenticator (Android/iPhone)](https://support.google.com/accounts/answer/1066447?hl=en)
- [認証者 (Android/iPhone)](https://authy.com/features/)
- [FreeOTP (Android)](https://play.google.com/store/apps/details?id=org.fedorahosted.freeotp)
- [GAuth Authenticator（Firefox OS、デスクトップなど）](https://github.com/gbraad-apps/gauth)

オーセンティケーターアプリケーションをインストールし、TFA を有効にする手順については、 _アカウント設定_ ページの [!DNL Cloud Console].

**ユーザーアカウントで TFA を有効にするには、以下を実行します。**:

1. にログインします。 [お使いのアカウント](https://console.adobecommerce.com).

1. 右上のアカウントメニューで、 **[!UICONTROL My Profile]**.

1. 次の日： _セキュリティ_ タブ、クリック **[!UICONTROL Set up application]**.

1. モバイルデバイスに承認済みの認証アプリケーションがない場合は、リンクされた手順に従って、認証アプリケーションをインストールしてください。

1. Adobe Commerce on クラウドインフラストラクチャアカウントをオーセンティケーターアプリケーションに追加します。

   - モバイルデバイスで、認証アプリケーションを開きます。 次に、設定コードをアプリケーションに追加します。

   - Adobe Analytics の [!UICONTROL **[!UICONTROL TFA set up - Application]**] ページで、モバイルデバイスの TFA コードを **[!UICONTROL Application verification code]** フィールドに入力します。

   - クリック **[!UICONTROL Verify and save]**.

     コードが有効な場合、Adobeはアカウントの電子メールアドレスに通知を送信し、アカウントが TFA を持つようになったことを確認します。

1. オプション。 有効にする _信頼できるブラウザー_ 認証コードをブラウザーに 30 日間キャッシュする設定です。

   この設定により、プロジェクトのログイン中の認証に関する課題の数が減ります。

1. クリック **保存** または **スキップ**.

1. 回復コードを保存します。

   - 次の日： _TFA 設定 — 回復_ モバイルデバイスや認証アプリケーションにアクセスできない場合にAdobe Commerce on cloud infrastructure プロジェクトにログインできるように、回復コードページをコピーして保存します。

   - デバイスや認証アプリケーションへのアクセスが失われた場合は、リカバリコードを別の場所にコピーするか、書き留めます。

   - クリック **保存** アカウントにコードを保存して、アカウントのセキュリティ設定から表示および管理できるようにします。

     >[!WARNING]
     >
     >TFA のアカウントへのアクセス権を失い、回復コードリストがない場合は、プロジェクト管理者に問い合わせる必要があります。 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) をクリックして、TFA アプリケーションをリセットします。

1. TFA 設定が完了したら、「 **保存** をクリックして、アカウントを更新します。

1. TFA で現在のセッションを認証します。

   - アカウントからログアウトします。
   - ユーザー名とパスワードを使用してログインします。
   - プロンプトが表示されたら、 `accounts.magento.cloud` モバイルデバイス上のオーセンティケーターアプリケーションからのエントリ。

### TFA 設定および回復コードの管理

クラウドインフラストラクチャ上のAdobe Commerceアカウントの TFA 設定は、 _セキュリティ_ のセクション _マイプロファイル_ ページに貼り付けます。

1. にログインします。 [お使いのアカウント](https://console.adobecommerce.com).

1. 右上のアカウントメニューで、 **[!UICONTROL My Profile]**.

1. 次の日： _マイプロファイル_ ページで、 **[!UICONTROL Security]** タブをクリックします。

1. 次のリンクを使用して、クラウドインフラストラクチャ上のAdobe Commerceの TFA 設定を更新します。

   - TFA を無効にする
   - 認証アプリケーションをリセット
   - 信頼できるブラウザーを追加または削除する
   - アカウントで TFA 回復コードを表示または更新します

### API トークンの作成

API トークンを OAuth 2 アクセストークンと交換できます。このトークンは、リクエストの認証に使用できます。

MFA 強制が有効になっているプロジェクトでは、マシンユーザーと自動プロセスの SSH アクセスを有効にするための API トークンが必要です。

>[!IMPORTANT]
>
>Protect API トークンの値。 コードサンプル、画面キャプチャ、または安全でないクライアントサーバー間通信でこの値を公開しないでください。 また、公開リポジトリに保存されているソースコードの値を公開しないでください。

**API トークンを作成するには、以下を実行します。**:

1. にログインします。 [お使いのアカウント](https://console.adobecommerce.com).

1. 右上のアカウントメニューで、 **[!UICONTROL My Profile]**.

1. 次の日： _マイプロファイル_ ページで、 **[!UICONTROL API tokens]** タブをクリックします。

1. クリック **[!UICONTROL Create API token]** API トークンを使用するマシンユーザーまたは自動プロセスに一致する名前を指定し、例えば、名前を入力します。

   ![API トークン](../../assets/api-token-name.png)

1. クリック **[!UICONTROL Create API token]**.
