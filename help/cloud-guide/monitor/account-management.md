---
title: New Relic アカウント管理
description: New Relic アカウントにアクセスし、クラウドインフラストラクチャプロジェクト上のAdobe Commerceのアクセス、統合、ツール使用を管理する方法について説明します。
feature: Cloud, Observability
role: Admin
exl-id: ee639e2e-4074-4384-8f68-152bc3bac93b
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '833'
ht-degree: 0%

---

# New Relic アカウント管理

Adobeがクラウドインフラストラクチャプロジェクトをプロビジョニングすると、ライセンスオーナーは、New Relic アカウントにアクセスするための資格情報と手順が記載されたメールをNew Relicから受け取ります。 メールを受け取っていない場合は、ライセンス所有者のメールアドレスを使用して、New Relicのパスワードをリセットします。

## ユーザーアクセスの管理

New Relic アカウントには、所有者のロールに割り当てることができるユーザーは 1 人だけです。 アカウント所有者を変更する必要がある場合は、管理者の役割を現在の所有者に割り当て、所有者の役割を別のユーザーに割り当てます。 参照： [アカウント所有者の更新](https://docs.newrelic.com/docs/accounts/original-accounts-billing/original-users-roles/users-roles-original-user-model/) が含まれる _New Relic ドキュメント_ 説明を参照してください。

New Relic アクセスの管理ガイドライン：

- プロジェクト所有者と管理者ユーザーは、New Relic アカウントにユーザーを追加したり、このアカウントからユーザーを削除したりできます。
- フルアクセスは 5 つまで作成しないでください **ユーザー**.
- 完全な機能セットへのアクセスを厳密に必要とするユーザーにのみ、フルアクセス権を付与します。
- 無料の具体的なガイダンスはありません **制限付き** ユーザー。

>[!TIP]
>
>所有者の役割をユーザーに割り当てる前に、そのユーザーがクラウドインフラストラクチャー上のAdobe CommerceのNew Relic アカウントに存在することを確認します。 そのアカウントにユーザーを追加する必要があり、既存のアカウント所有者または管理者が支援できない場合は、にアクセスできるユーザー [Adobeパートナーシップ所有者アカウント](https://account.newrelic.com/accounts/1311131/users) （New Relicの場合）は、ユーザーを追加できます。

1 つ以上を追加 **Admin** すべてのアクセス、統合およびツールの使用を管理できるNew Relic アカウントにユーザーを追加します。

**New Relicで User Management にアクセスするには**:

1. にログイン [New Relic アカウント](https://login.newrelic.com/login).

1. 左下のナビゲーションからユーザー名を選択します。

1. クリック **[!UICONTROL Administration]** リストから次のいずれかの操作を行います。

   - **[!UICONTROL User management]** ユーザーを追加し、アクティブなユーザーと保留中の招待を管理します。

   - **[!UICONTROL Access management]** ユーザーグループ、役割およびアカウントを管理します。

参照： [ユーザー管理](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/user-management-ui-and-tasks/) が含まれる _New Relic_ ドキュメント。

## スターター環境用のNew Relicの設定

>[!NOTE]
>
>**Pro 環境** は、New Relic サービスを使用するように事前設定されており、有効にする手順と接続する手順をスキップできます。 New Relic APM がステージング環境と実稼動環境にインストールされていない場合や、New Relic インフラストラクチャが実稼動環境で使用できない場合は、 [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) にアクセスしてインストールを要求します。

スターター環境の場合は、 `.magento.app.yaml` を確認するためのファイル `runtime` の節にはNew Relic拡張機能が含まれています。 拡張機能が設定されていない場合は、次を追加します。

> `.magento.app.yaml`

```yaml
runtime:
    extensions:
        - newrelic
```

### ライセンスキーを適用

クラウド環境をNew Relicに接続するには、New Relic ライセンスキーを環境に追加します。

- の場合 **Pro プロジェクト**&#x200B;の場合、Adobeは、プロビジョニングプロセスの間に、ライセンスキーを実稼動環境とステージング環境に追加します。 にログインできます [New Relic アカウント](https://login.newrelic.com/login) クラウドインフラストラクチャサイトのAdobe CommerceとNew Relicの間の接続を検証します。

- の場合 **スタータープロジェクト**&#x200B;には、までサポートするNew Relic ライセンスキーがあります _三_ 環境。 キーは手動で環境設定に追加する必要があります。 スターター環境は、New Relic サービスを使用するように事前にプロビジョニングされていません。

スターター環境の場合は、環境設定にNew Relic ライセンスキーを追加して、New Relic統合を有効にします。 キーをステージング環境、実稼動環境、および任意のもう 1 つの環境に追加します。 設定に必要なのはNew Relic ライセンスキーのみです。 その他の設定オプションについて詳しくは、を参照してください。 [New Relic レポート](https://experienceleague.adobe.com/docs/commerce-admin/config/general/new-relic-reporting.html) のトピック _Adobe Commerce ユーザーガイド_.

{{redeploy-warning}}

>[!PREREQUISITES]
>
>- Adobe Commerce アカウントページまたはプロジェクトに関連付けられたNew Relic ライセンスのログイン資格情報
>- [管理レベルアクセス](../project/user-access.md) スターター環境に追加して設定
>- にアクセスするための資格情報 [Admin](https://experienceleague.adobe.com/docs/commerce-admin/systems/user-accounts/permissions.html) 環境用

**スターター環境用のNew Relicを設定するには**:

1. からNew Relic ライセンスキーを見つける [!DNL Cloud Console] または Cloud CLI です。

   **[!DNL Cloud Console]メソッド**:

   - クラウドプロジェクトを開きます [アカウントページ](https://accounts.magento.cloud/user).

   - 日 _プロジェクト_ タブで、プロジェクトを検索します。

   - クリック **詳細を表示** （プロジェクトインフラストラクチャ情報）。

   - を展開します。 **New Relic サービス** ライセンスキーを表示するには、セクションに移動します。

   - ライセンスキーをコピーします。

   **クラウド CLI メソッド**:

   ```bash
   magento-cloud subscription:info services.newrelic
   ```

1. を使用して、New Relic ライセンスキーを `magento-cloud` CLI。

   - ライセンスキーを必要とする環境に変更します。
   - 次を使用して変数値を更新します `magento-cloud` CLI コマンド：

     ```bash
     magento-cloud variable:update php:newrelic.license --value <newrelic-license-key>
     ```

   オプションで、から追加できます [Commerce管理者](https://experienceleague.adobe.com/docs/commerce-admin/start/reporting/new-relic-reporting.html#step-3%3A-configure-your-store).

1. にログイン [New Relic アカウント](https://login.newrelic.com/login) で、Adobe Commerce環境からデータを表示できることを確認します。 参照： [パフォーマンスの調査](investigate-performance.md).

### ライセンスキーを削除

New Relic ライセンスキーは、3 つのアクティブな環境でのみ使用できます。 キーが 3 つの環境で使用されている場合、別の環境に追加する前に、いずれかの環境からキーを削除する必要があります。

**環境からライセンスキーを削除するには**:

1. 環境変数のリスト。

   ```bash
   magento-cloud variable:list
   ```

   応答の例：

   ```terminal
    +----------------------+-------------+----------------------+---------+
    | Name                 | Level       | Value                | Enabled |
    +----------------------+-------------+----------------------+---------+
    | php:newrelic.license | environment | newrelic-license-key | true    |
    +----------------------+-------------+----------------------+---------+
   ```

   >[!WARNING]
   >
   >ライセンスキーをとして追加した場合 _プロジェクト_ 変数。プロジェクトレベルの変数を削除する必要があります。 プロジェクト変数は、にライセンスを追加します _毎_ 環境ブランチが作成されました。ライセンスを使用するか制限を超える可能性があります。 プロジェクト変数を一覧表示するには： `magento-cloud variable:list --level project`

1. ライセンス変数を削除します。

   ```bash
   magento-cloud variable:delete php:newrelic.license
   ```
