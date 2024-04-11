---
title: ユーザーアクセスの管理
description: クラウドインフラストラクチャプロジェクトおよび環境でAdobe Commerceへのユーザーアクセスを管理する方法について説明します。
role: Admin
feature: Cloud, Roles/Permissions
last-substantial-update: 2023-06-27T00:00:00Z
topic: Security
exl-id: 3357a3ea-bf86-4a65-95d1-6b24f1152248
source-git-commit: b85a163ff62f8a63430dff7c96b5cf391cf38d79
workflow-type: tm+mt
source-wordcount: '1454'
ht-degree: 0%

---

# ユーザーアクセスの管理

クラウドインフラストラクチャ上のAdobe Commerce プロジェクトでは、役割ベースのアクセスを使用します。 プロジェクトレベルでは、次の 2 つの役割を使用できます。

- **プロジェクト管理者** – すべてのプロジェクト環境への書き込みアクセス権。ユーザーの管理、コードのプッシュ、プロジェクト設定の更新を行うことができます。
- **プロジェクトビューア** – すべてのプロジェクト環境への表示のみのアクセス。

プロジェクトビューアはどの環境でもタスクを実行できませんが、プロジェクトビューアに特定の環境タイプへの書き込みアクセス権を付与できます。

環境レベルのアクセスは、実稼働、ステージング、開発の環境タイプに基づいています。 ユーザーの許可 _ビューア_ に対する権限 _開発_ 環境とは、ユーザーが表示できることを意味します **all** プロジェクトの開発環境。 次の表に、各権限レベルに付与される機能を示します。

| 権限レベル | アクセス | SSH アクセス |
| ------------------ | ----------- | :----------: |
| **Admin** | 設定の変更、プッシュコード、タスクの実行、親環境とのマージを含むブランチ管理などの管理タスクを実行します。 | はい |
| **投稿者** | 環境のプッシュコードおよびブランチ（設定の変更やアクションの実行ができない） | はい |
| **ビューア** | 環境タイプへの表示専用アクセス | 不可 |
| **アクセスなし** | 環境タイプへのアクセス権がありません | 不可 |

{style="table-layout:auto"}

を使用して、ユーザーを追加し、役割を割り当てることができます `magento-cloud` CLI または [!DNL Cloud Console].

>[!BEGINSHADEBOX]

**前提条件：**

- Adobe IDに登録されているユーザー。 ユーザーは次の条件を満たす必要があります [Adobeアカウントの登録](https://account.adobe.com) その後 [クラウドアカウントの初期化](https://console.adobecommerce.com) クラウドプロジェクトに追加する前に、
- ユーザーがを割り当てました **Admin** の役割ではユーザーを管理できません `magento-cloud` CLI。 次の権限が付与されたユーザーのみ： **アカウント所有者** 役割はユーザーを管理できます。

>[!ENDSHADEBOX]

## CLI を使用したユーザー管理

の使用 `magento-cloud` ユーザーを管理し、自動システムと統合するための CLI:

- `magento-cloud user:add`- ユーザーをプロジェクトに追加
- `magento-cloud user:delete` – ユーザーを削除します
- `magento-cloud user:list [users]` – プロジェクトユーザーのリスト
- `magento-cloud user:role` – ユーザーロールを表示または変更します
- `magento-cloud user:update` – プロジェクトのユーザーの役割を更新

次の例では、 `magento-cloud` ユーザーの追加、役割の設定、プロジェクトの割り当ての変更、ユーザーの役割の割り当てを行うための CLI。

**ユーザーを追加して役割を割り当てるには**:

1. の使用 `magento-cloud` CLI を使用してユーザーを追加します。

   ```bash
   magento-cloud user:add
   ```

   >[!IMPORTANT]
   >
   >ユーザーにはAdobe IDが必要です。詳しくは、 [前提条件](#add-users-and-manage-access).

1. プロンプトに従って、ユーザーのメールアドレスを指定し、プロジェクトおよび環境タイプの役割を設定し、ユーザーを追加します。

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

   ユーザーを追加すると、Adobeから、クラウドインフラストラクチャプロジェクト上のAdobe Commerceにアクセスするための手順が記載されたメールが、指定されたアドレスに送信されます。

### ユーザーのプロジェクトの役割を表示する

```bash
magento-cloud user:get alice@example.com
```

>応答の例：

```terminal
Current role(s) of User (alice@example.com) on Production (project_id):
  Project role: admin
```

### 複数の環境へのユーザーの追加

ユーザーをとして追加するには `viewer` 次の日 `Production` 環境と as a `contributor` オン `Integration` 環境：

```bash
magento-cloud user:add alice@example.com -r production:v -r integration:c
```

### ユーザー環境権限の更新

ユーザー環境の権限をに更新するには： `admin` 日 `Production` 環境：

```bash
magento-cloud user:update alice@example.com -r production:a
```

## からのユーザー管理 [!DNL Cloud Console]

を使用できます [[!DNL Cloud Console]](../../get-started/cloud-console.md) 権限を追加して使用するには _編集_ 既存のユーザーの権限を変更する機能。

>[!IMPORTANT]
>
>ユーザーにはAdobe IDが必要です。詳しくは、 [前提条件](#add-users-and-manage-access).

### プロジェクトにユーザーを追加する

1. にログインします [[!DNL Cloud Console]](https://console.adobecommerce.com/).

1. 「」からプロジェクトを選択 _すべてのプロジェクト_ リスト。

1. プロジェクトダッシュボードで、右上の設定アイコンをクリックします。

1. 次の下 _プロジェクト設定_&#x200B;を選択し、 **[!UICONTROL Access]**.

1. が含まれる _アクセス_ 表示、クリック **[!UICONTROL Add]**.

1. を完了する _[!UICONTROL Add User]_フォーム：

   - ユーザーのメールアドレスを入力します。

   - **[!UICONTROL Project admin]** – すべての設定と環境タイプに管理者権限を付与します。

   - **[!UICONTROL Environment types and permissions]** – 特定の環境タイプにアクセスレベルと特定の権限レベルを付与します。 _アクセスなし_, _Admin_ （設定の変更、アクションの実行、コードの結合）、 _投稿者_ （プッシュコード）、 _ビューア_ （表示のみ）。

   >[!TIP]
   >
   >のみ **プロジェクト管理者** では、あらゆる環境のユーザーを管理できます。 次の手順でユーザーに以下へのアクセスを許可します **アクセス** タブ、別の **プロジェクト管理者** または **アカウント所有者** そのユーザーにを割り当てる必要があります。 **プロジェクト管理者** 役割。

1. クリック **[!UICONTROL Add User]**.

   >[!IMPORTANT]
   >
   >ユーザーを追加しても、デプロイメントは自動的にはトリガーされません。

1. ユーザーを追加したら、すべての環境を再デプロイして変更を適用します。 ユーザーを追加しても、デプロイメントは自動的にはトリガーされません。 再デプロイメントは、ユーザーが SSH を使用して環境にアクセスしたり、管理者タスクを実行したりできるようにするための重要な手順です。

ユーザーを追加すると、Adobeから、クラウドインフラストラクチャプロジェクト上のAdobe Commerceにアクセスするための手順が記載されたメールが、指定されたアドレスに送信されます。

## ユーザー認証の要件

セキュリティを強化するために、Adobeは、クラウドインフラストラクチャプロジェクトのソースコードおよび環境のAdobe Commerceに SSH アクセスするために、2 要素認証（TFA）を必要とする、プロジェクトレベルの多要素認証（MFA）の適用を提供します。 参照： [SSH 用 MFA の有効化](multi-factor-authentication.md).

クラウドインフラストラクチャプロジェクト上のAdobe Commerceで MFA 適用が有効になっている場合、そのプロジェクト内の環境に SSH アクセス権を持つすべてのユーザーは、クラウドインフラストラクチャアカウント上のAdobe Commerceで TFA を有効にする必要があります。 自動プロセスの場合、コマンドラインから認証するマシンユーザーと API トークンを作成できます。

クラウドプロジェクトにユーザーを追加した後、そのユーザーにアカウントセキュリティ設定を確認するように依頼し、必要に応じて次のセキュリティ設定を追加します。

- **TFA を有効にする** – 二要素認証を構成することにより、セキュリティとコンプライアンスの標準に対応します。 で設定されたプロジェクト [MFA の適用](multi-factor-authentication.md) プロジェクトへのアクセスに SSH を使用するアカウントには TFA が必要です。

- **SSH キーを有効にする**- クラウドインフラストラクチャー上のAdobe Commerceのソースコードリポジトリにアクセスする必要があるユーザーは、自分のアカウントで SSH キーを有効にする必要があります。 参照： [安全な接続](../development/secure-connections.md).

- **API トークンの作成**- ユーザーは、環境への SSH アクセスに使用される API トークンを生成する必要があります。 自動プロセスの認証ワークフローを有効にするには、トークンが必要です。

  MFA 適用が有効なプロジェクトでは、API トークンを使用して、自動アカウントからの SSH アクセスリクエストを認証する必要があります。 トークンを使用すると、TFA が必要な認証ワークフローを自動プロセスでバイパスできます。

### クラウドアカウントの TFA の有効化

クラウドインフラストラクチャー上のAdobe Commerceは、次のいずれかのアプリケーションを使用して TFA をサポートします。

- [Google認証（Android/iPhone）](https://support.google.com/accounts/answer/1066447?hl=en)
- [作成者（Android/iPhone）](https://authy.com/features/)
- [FreeOTP （Android）](https://play.google.com/store/apps/details?id=org.fedorahosted.freeotp)
- [GAuth 認証（Firefox OS、デスクトップなど）](https://github.com/gbraad-apps/gauth)

認証アプリケーションのインストールおよび TFA の有効化の手順については、を参照してください。 _アカウント設定_ 内のページ [!DNL Cloud Console].

**ユーザーアカウントで TFA を有効にするには**:

1. へのログイン [アカウント](https://console.adobecommerce.com).

1. 右上のアカウントメニューで、 **[!UICONTROL My Profile]**.

1. 日 _セキュリティ_ タブ、クリック **[!UICONTROL Set up application]**.

1. モバイルデバイスに承認済みの認証アプリケーションがない場合は、リンクされている手順に従ってインストールします。

1. クラウドインフラストラクチャアカウント上のAdobe Commerceを認証アプリケーションに追加します。

   - モバイルデバイスで、認証アプリケーションを開きます。 次に、設定コードをアプリケーションに追加します。

   - が含まれる [!UICONTROL **[!UICONTROL TFA set up - Application]**] ページで、モバイルデバイスの TFA コードを **[!UICONTROL Application verification code]** フィールド。

   - クリック **[!UICONTROL Verify and save]**.

     コードが有効な場合、Adobeはアカウントのメールアドレスに、アカウントに TFA が設定されたことを確認する通知を送信します。

1. オプション。 Enable （有効） _信頼できるブラウザー_ 認証コードをブラウザーに 30 日間キャッシュする設定。

   この設定により、プロジェクトへのログイン時に発生する認証の問題を軽減できます。

1. クリック **保存** または **スキップ**.

1. リカバリ・コードを保存します。

   - 日 _TFA の設定 – リカバリ_ コードページでリカバリコードをコピーして保存し、モバイルデバイスや認証アプリケーションにアクセスできない場合に cloud infrastructure プロジェクトのAdobe Commerceにログインできるようにします。

   - デバイスまたは認証アプリケーションへのアクセスが失われた場合に備えて、回復コードを別の場所にコピーするか、書き留めます。

   - クリック **保存** アカウントのセキュリティ設定からコードを表示および管理できるように、コードをアカウントに保存します。

     >[!WARNING]
     >
     >TFA のアカウントへのアクセス権を失い、回復コード リストを持っていない場合は、プロジェクト管理者に連絡する必要があります。 [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) TFA アプリケーションをリセットします。

1. TFA 設定が完了したら、 **保存** をクリックしてアカウントを更新します。

1. 現在のセッションを TFA で認証します。

   - アカウントからログアウトします。
   - ユーザー名とパスワードを使用してログインします。
   - プロンプトが表示されたら、の TFA コードを入力 `accounts.magento.cloud` モバイルデバイスの authenticator アプリケーションからエントリします。

### TFA 構成および回復コードの管理

Adobe Commerce on クラウドインフラストラクチャアカウントの TFA 設定は、 _セキュリティ_ に関する節 _マイプロファイル_ ページ。

1. へのログイン [アカウント](https://console.adobecommerce.com).

1. 右上のアカウントメニューで、 **[!UICONTROL My Profile]**.

1. 日 _マイプロファイル_ ページで、 **[!UICONTROL Security]** タブ。

1. 使用可能なリンクを使用して、クラウドインフラストラクチャアカウントのAdobe Commerceの TFA 設定を更新します。

   - TFA を無効にする
   - 認証アプリケーションのリセット
   - 信頼済みブラウザーの追加または削除
   - アカウントの TFA 回復コードを表示または更新します

### API トークンの作成

API トークンを OAuth 2 アクセストークンと交換し、リクエストの認証に使用できます。

MFA 適用が有効になっているプロジェクトでは、マシンユーザーと自動プロセス用の SSH アクセスを有効にするには、API トークンが必要です。

>[!IMPORTANT]
>
>アカウントのProtect API トークン値。 コードサンプル、画面のキャプチャ、安全でないクライアントサーバー通信で値を公開しないでください。 また、公開リポジトリに格納されているソースコードで値を公開しないでください。

**API トークンを作成するには**:

1. へのログイン [アカウント](https://console.adobecommerce.com).

1. 右上のアカウントメニューで、 **[!UICONTROL My Profile]**.

1. 日 _マイプロファイル_ ページで、 **[!UICONTROL API tokens]** タブ。

1. クリック **[!UICONTROL Create API token]** そして、名前を入力します。例えば、マシンユーザーに一致する名前または API トークンを使用する自動プロセスを指定します。

   ![API トークン](../../assets/api-token-name.png)

1. クリック **[!UICONTROL Create API token]**.
