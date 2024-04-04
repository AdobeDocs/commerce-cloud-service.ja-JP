---
title: 管理変数
description: クラウドインフラストラクチャにAdobe Commerceをインストールする際に使用される環境変数の一覧を参照してください。
feature: Cloud, Configuration, Install, Roles/Permissions
role: Developer
exl-id: 2829a9dc-40bb-4665-886e-a56d98468fc1
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '421'
ht-degree: 0%

---

# 管理変数

クラウドインフラストラクチャプロジェクト上のAdobe Commerceに管理者アクセス権を持つユーザーは、次のプロジェクト環境変数を使用して、管理者ユーザーアカウントの設定を上書きし、Admin UI にアクセスできます。

## 管理者資格情報

次の表の ADMIN 変数を使用して、コマースのインストール中に管理者ユーザーの資格情報を上書きできます。

インストール後に値を変更する場合は、SSH を使用して環境に接続し、Adobe Commerce CLI を使用します [`admin:user` command](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/tutorials/admin.html) をクリックして、管理者ユーザー資格情報を作成または編集します。

| 変数 | デフォルト | 説明 |
| -------------- | --------------------------- | ----------- |
| `ADMIN_USERNAME` | ライセンス所有者のメールアドレス | 管理ユーザーを含む、他のユーザーを作成できる管理ユーザーのユーザー名。 |
| `ADMIN_EMAIL` |                             | 管理ユーザーの電子メールアドレス。 このアドレスは、パスワードリセット通知の送信に使用されます。 |
| `ADMIN_PASSWORD` |                             | 管理ユーザーのパスワード。 プロジェクトを作成すると、ランダムなパスワードが生成され、ライセンス所有者に電子メールが送信されます。 プロジェクトの作成時に、ライセンス所有者が既にパスワードを変更しているはずです。 更新したパスワードについては、ライセンス所有者にお問い合わせください。 |
| `ADMIN_LOCALE` | `en_US` | 管理者が使用するデフォルトのロケールです。 |

## 管理 URL

次の環境変数を使用して、Admin UI へのアクセスを保護します。 指定した場合、この値はインストール時にデフォルトの URL よりも優先されます。

`ADMIN_URL` — 管理 UI にアクセスするための相対 URL。 デフォルトの URL は `/admin`. セキュリティ上の理由から、Adobeでは、デフォルトを推測しにくい一意のカスタム管理 URL に変更することをお勧めします。

### 管理 URL の変更

Adobeは、インストール後に管理者 URL の環境レベル変数を変更することをお勧めします。 複製されたから分岐する前に、セキュリティ上の理由からこの設定を構成します。 `master` 環境。 から作成されたすべてのブランチ `master` ブランチは、環境レベルの変数とその値を継承します。

以下を使用します。 `magento-cloud variable:update` コマンドを使用して変数値を更新します。 ( `variable:set` コマンドは廃止され、使用できません。) 次の例では、ADMIN_URL を `newAdmin_A8v10`:

```bash
magento-cloud variable:update ADMIN_URL --value newAdmin_A8v10 -e master
```

>[!NOTE]
>
>The `ADMIN_URL` 値には、カスタム管理パスの文字（a ～ z または A ～ Z）、数字 (0 ～ 9)、アンダースコア文字 (_) を使用できます。 スペースまたは他の文字は **not** 許可済み

**を使用して URL を変更するには[!DNL Cloud Console]**:

1. にログインします。 [[!DNL Cloud Console]](https://console.adobecommerce.com).

1. 次の場所からプロジェクトを選択： _すべてのプロジェクト_ リスト。

1. プロジェクトの概要で、環境を選択し、設定アイコンをクリックします。

   ![プロジェクト設定](../../assets/icon-configure.png){width="36"}

1. を選択します。 **変数** タブをクリックします。

1. クリック **変数を作成**.

1. 次の情報を入力します。

   - **変数名** = `ADMIN_URL`
   - **値** =新しい URL。 例えば、管理 URL をに設定します。 `magento_A8v10`.

   デフォルトでは、 `Available during runtime` および `Make inheritable` が選択されている。

1. クリック **変数を作成** とが呼び出され、デプロイメントが完了するまで待ちます。 このボタンは、必須フィールドに値が含まれている場合にのみ表示されます。
