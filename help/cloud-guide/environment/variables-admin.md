---
title: 管理変数
description: クラウドインフラストラクチャー上にAdobe Commerceをインストールする際に使用される環境変数のリストを参照してください。
feature: Cloud, Configuration, Install, Roles/Permissions
role: Developer
exl-id: 2829a9dc-40bb-4665-886e-a56d98468fc1
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '421'
ht-degree: 0%

---

# 管理変数

クラウドインフラストラクチャプロジェクト上のAdobe Commerceに対する管理アクセス権を持つユーザーは、次のプロジェクト環境変数を使用して、管理 UI にアクセスするための管理ユーザーアカウントの設定を上書きできます。

## 管理者の資格情報

次の表の管理変数を使用して、Commerceのインストール中に管理者ユーザーの資格情報を上書きできます。

インストール後に値を変更する場合は、SSH を使用してに接続し、Adobe Commerce CLI を使用します [`admin:user` コマンド](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/tutorials/admin.html) 管理者ユーザーの資格情報を作成または編集するには、次の手順を実行します。

| 変数 | デフォルト | 説明 |
| -------------- | --------------------------- | ----------- |
| `ADMIN_USERNAME` | ライセンス所有者の E メールアドレス | 管理者ユーザーのユーザー名。管理者ユーザーを含む他のユーザーを作成できます。 |
| `ADMIN_EMAIL` |                             | 管理者ユーザーのメールアドレス。 このアドレスは、パスワードリセット通知の送信に使用されます。 |
| `ADMIN_PASSWORD` |                             | 管理者ユーザーのパスワード。 プロジェクトを作成すると、ランダムなパスワードが生成され、ライセンス所有者にメールが送信されます。 プロジェクトの作成時に、ライセンス所有者が既にパスワードを変更している必要があります。 更新されたパスワードについては、ライセンス所有者にお問い合わせください。 |
| `ADMIN_LOCALE` | `en_US` | 管理者が使用するデフォルトのロケールです。 |

## 管理者 URL

次の環境変数を使用して、管理 UI へのアクセスを保護します。 指定した場合、インストール時のデフォルト URL はこの値で上書きされます。

`ADMIN_URL` – 管理 UI にアクセスするための相対 URL。 デフォルトの URL は、です。 `/admin`. セキュリティ上の理由から、Adobeでは、デフォルトを推測しにくい一意のカスタム管理 URL に変更することをお勧めします。

### 管理者 URL の変更

Adobeでは、インストール後に管理者 URL の環境レベル変数を変更することをお勧めします。 クローンから分岐する前に、セキュリティ上の理由から、この設定を設定します `master` 環境。 から作成されたすべてのブランチ `master` ブランチは、環境レベルの変数とその値を継承します。

の使用 `magento-cloud variable:update` 変数値を更新するコマンド。 （ `variable:set` コマンドは非推奨となり、使用できなくなりました）。 次の例では、ADMIN_URL をに更新します `newAdmin_A8v10`:

```bash
magento-cloud variable:update ADMIN_URL --value newAdmin_A8v10 -e master
```

>[!NOTE]
>
>この `ADMIN_URL` 値には、カスタム管理パス用の文字（a ～ z または A ～ Z）、数字（0 ～ 9）、アンダースコア文字（_）を使用できます。 スペースまたはその他の文字は **ではない** 承諾済み。

**を使用して URL を変更するには[!DNL Cloud Console]**:

1. にログインします [[!DNL Cloud Console]](https://console.adobecommerce.com).

1. 「」からプロジェクトを選択 _すべてのプロジェクト_ リスト。

1. プロジェクトの概要で、環境を選択し、設定アイコンをクリックします。

   ![プロジェクト設定](../../assets/icon-configure.png){width="36"}

1. 「」を選択します **変数** タブ。

1. クリック **変数を作成**.

1. 以下を入力します。

   - **変数名** = `ADMIN_URL`
   - **value** =新しい URL。 例えば、管理者 URL をに設定します。 `magento_A8v10`.

   デフォルトでは `Available during runtime` および `Make inheritable` が選択されました。

1. クリック **変数を作成** デプロイメントが完了するまで待ちます。 このボタンは、必須フィールドに値が含まれている場合にのみ表示されます。
