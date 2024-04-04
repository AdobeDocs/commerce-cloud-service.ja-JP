---
title: New Relic Account Management
description: New Relicアカウントにアクセスし、Adobe Commerce on cloud infrastructure プロジェクトのアクセス、統合、ツール使用を管理する方法について説明します。
feature: Cloud, Observability
role: Admin
exl-id: ee639e2e-4074-4384-8f68-152bc3bac93b
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '833'
ht-degree: 0%

---

# New Relicアカウント管理

Adobeがクラウドインフラストラクチャプロジェクトをプロビジョニングすると、ライセンス所有者は、New Relicアカウントへのアクセスに必要な資格情報と手順が記載された電子メールをNew Relicから受け取ります。 この電子メールが届かない場合は、ライセンス所有者の電子メールアドレスを使用して、New Relicのパスワードをリセットします。

## ユーザーアクセスを管理

1 つのNew Relicアカウントに割り当てることができるのは、1 人のユーザーのみです。 アカウント所有者を変更する必要がある場合は、管理者の役割を現在の所有者に割り当て、その所有者の役割を別のユーザーに割り当てます。 詳しくは、 [アカウント所有者の更新](https://docs.newrelic.com/docs/accounts/original-accounts-billing/original-users-roles/users-roles-original-user-model/) （内） _New Relicドキュメント_ 」を参照してください。

New Relicアクセス管理のガイドライン：

- プロジェクト所有者と管理者ユーザーは、New Relicアカウントのユーザーの追加と削除ができます。
- フルアクセスを 5 つ以上作成しないでください **ユーザー**.
- 完全な機能セットへのアクセスを厳密に必要とするユーザーに対してのみ、フルアクセス権を付与します。
- 無料での具体的なガイダンスはありません **制限** ユーザー。

>[!TIP]
>
>ユーザーに所有者の役割を割り当てる前に、そのユーザーがクラウドインフラストラクチャ上のAdobe CommerceのNew Relicアカウントに存在することを確認します。 ユーザーをそのアカウントに追加する必要があり、既存のアカウントの所有者または管理者が支援できない場合、 [Adobeパートナーシップ所有者アカウント](https://account.newrelic.com/accounts/1311131/users) のNew Relicは、顧客の代わりにユーザーを追加できます。

少なくとも 1 つ追加してください **管理者** すべてのアクセス、統合およびツール使用を管理できるNew Relicアカウントへのユーザー。

**New Relicのユーザー管理にアクセスするには**:

1. にログインします。 [New Relicアカウント](https://login.newrelic.com/login).

1. 左下のナビゲーションからユーザー名を選択します。

1. クリック **[!UICONTROL Administration]** をクリックし、リストから次のいずれかを選択します。

   - **[!UICONTROL User management]** ：ユーザーを追加し、アクティブなユーザーと保留中の招待を管理します。

   - **[!UICONTROL Access management]** ：ユーザーグループ、役割、アカウントを管理します。

詳しくは、 [ユーザー管理](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/user-management-ui-and-tasks/) （内） _New Relic_ ドキュメント。

## スターター環境用にNew Relicを設定する

>[!NOTE]
>
>**Pro 環境** は、New Relicサービスを使用するように事前に設定されており、有効化および接続の手順をスキップできます。 New Relic APM がステージング環境と実稼動環境にインストールされていない場合、またはNew Relicインフラストラクチャが実稼動環境で使用できない場合は、 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) をクリックして、インストールをリクエストします。

スターター環境の場合は、 `.magento.app.yaml` ファイルを開いて、 `runtime` の節には、 New Relic拡張機能が含まれています。 拡張機能が設定されていない場合は、以下を追加します。

> `.magento.app.yaml`

```yaml
runtime:
    extensions:
        - newrelic
```

### ライセンスキーを適用

クラウド環境をNew Relicに接続するには、New Relicライセンスキーを環境に追加します。

- の場合 **プロプロジェクト**&#x200B;を使用すると、Adobeは、プロビジョニングプロセス中にライセンスキーを実稼動環境とステージング環境に追加します。 にログインできます。 [New Relicアカウント](https://login.newrelic.com/login) をクリックして、Adobe Commerce on cloud infrastructure サイトとNew Relicの間の接続を検証します。

- の場合 **スタータープロジェクト**&#x200B;に等しい場合、最大でをサポートするNew Relicライセンスキーがあります。 _3_ 環境。 手動で環境設定にキーを追加する必要があります。 スターター環境は、New Relicサービスを使用するように事前にプロビジョニングされていません。

スターター環境の場合、New Relicライセンスキーを環境設定に追加して、New Relic統合を有効にします。 キーをステージング環境と実稼動環境に追加し、別の環境を任意に追加します。 設定に必要なのは、New Relicライセンスキーのみです。 その他の設定オプションについて詳しくは、 [New Relic Reporting](https://experienceleague.adobe.com/docs/commerce-admin/config/general/new-relic-reporting.html) トピック _Adobe Commerce User Guide_.

{{redeploy-warning}}

>[!PREREQUISITES]
>
>- Adobe Commerceアカウントページ、またはプロジェクトに関連付けられているNew Relicライセンスのログイン資格情報
>- [管理者レベルのアクセス](../project/user-access.md) を設定するスターター環境に追加します。
>- にアクセスするための資格情報 [管理者](https://experienceleague.adobe.com/docs/commerce-admin/systems/user-accounts/permissions.html) （環境の）

**スターター環境用にNew Relicを設定するには**:

1. New Relicのライセンスキーは、 [!DNL Cloud Console] または Cloud CLI。

   **[!DNL Cloud Console]メソッド**:

   - クラウドプロジェクトを開く [アカウントページ](https://accounts.magento.cloud/user).

   - 次の日： _プロジェクト_ 「 」タブで、プロジェクトを検索します。

   - クリック **詳細を表示** プロジェクトインフラストラクチャに関する情報。

   - を展開します。 **New Relic Service** セクションに、ライセンスキーを表示します。

   - ライセンスキーをコピーします。

   **Cloud CLI のメソッド**:

   ```bash
   magento-cloud subscription:info services.newrelic
   ```

1. を使用して、New Relicライセンスキーを環境に追加する `magento-cloud` CLI。

   - ライセンスキーが必要な環境に変更します。
   - 次を使用して変数値を更新します。 `magento-cloud` CLI コマンド：

     ```bash
     magento-cloud variable:update php:newrelic.license --value <newrelic-license-key>
     ```

   オプションで、 [コマース管理者](https://experienceleague.adobe.com/docs/commerce-admin/start/reporting/new-relic-reporting.html#step-3%3A-configure-your-store).

1. にログインします。 [New Relicアカウント](https://login.newrelic.com/login) をクリックして、Adobe Commerce環境からデータが表示されることを確認します。 詳しくは、 [パフォーマンスの調査](investigate-performance.md).

### ライセンスキーを削除

3 つのアクティブな環境でのみNew Relicライセンスキーを使用できます。 キーが 3 つの環境で使用されている場合、キーを別の環境に追加する前に、いずれかの環境からキーを削除する必要があります。

**環境からライセンスキーを削除するには**:

1. 環境変数をリストします。

   ```bash
   magento-cloud variable:list
   ```

   レスポンスのサンプル：

   ```terminal
    +----------------------+-------------+----------------------+---------+
    | Name                 | Level       | Value                | Enabled |
    +----------------------+-------------+----------------------+---------+
    | php:newrelic.license | environment | newrelic-license-key | true    |
    +----------------------+-------------+----------------------+---------+
   ```

   >[!WARNING]
   >
   >ライセンスキーを _プロジェクト_ 変数を使用する場合は、そのプロジェクトレベルの変数を削除する必要があります。 プロジェクト変数がライセンスをに追加します。 _毎_ 環境ブランチが作成されました。ライセンス制限を超えたり、超過したりすることができます。 プロジェクト変数を一覧表示するには： `magento-cloud variable:list --level project`

1. ライセンス変数を削除します。

   ```bash
   magento-cloud variable:delete php:newrelic.license
   ```
