---
title: '[!DNL Onboarding] コマース'
description: クラウドアカウントにアクセスし、クラウドインフラストラクチャプロジェクトでAdobe Commerceを設定します。
role: Admin
recommendations: noDisplay, catalog
exl-id: c6b768d7-d835-4a8d-aad9-1c0324f7570d
source-git-commit: abe9aa36b907be8bdfdf42e6f28f1e1eac68fecf
workflow-type: tm+mt
source-wordcount: '760'
ht-degree: 0%

---

# [!DNL Onboarding] コマースへ

Adobeがクラウドインフラストラクチャ上のコマースサブスクリプションをアクティベートすると、初期プロジェクトおよびコードアクセスは、ライセンス所有者（アカウント所有者）として指定された人のみが使用できます。

ライセンス所有者は、Adobe Commerce on cloud infrastructure アカウントの支払いおよびその他のビジネス関連トランザクションを管理する、ビジネスまたは金融組織内の人物です。 この人物は、Adobeとの接触点として機能します。 アカウントでライセンス所有者を変更する必要がある場合は、Adobeアカウントチームに連絡する必要があります。

プロジェクトのオンボーディングをすばやくおこない、実稼働環境でのデプロイメント用にサイトの開発を開始するには、必要な設定を完了し、 [!DNL onboarding] タスク。 通常、ライセンス所有者は、管理者アクセスを保護し、セットアップ、カスタマイズ、開発作業に役立つ技術管理者ユーザーを作成することで、プロセスを開始します。

## クラウドアカウントに新規登録

クラウドインフラストラクチャ上のAdobe Commerceアカウントがない場合は、にお問い合わせください。 [セールス]. 新規登録すると、Adobeはアカウントを作成し、プロジェクトインターフェイスへのアクセス方法を記載したお知らせメールを送信します。 この電子メールには、アカウントにログインして初期プロジェクト設定を完了するためのリンクが含まれています。

### クラウド [!DNL Onboarding] UI

( ) のAdobe Commerce on cloud infrastructure Project ページ[!DNL Onboarding] UI) には、プロジェクトとサービスの設定、アクセス権の決定、開発の手引きのチェックリストが用意されています。 OBUI から、次のことができます。

- プロジェクトとブランチを管理できるスーパーユーザーであるテクニカル管理者を追加する
- プロジェクト環境にアクセスします ( [!DNL Cloud Console]
- ユーザー受け入れテスト (UAT) のチェックリストを完了し、さらなるテストへのリンクを追加

**プロジェクトページを開くには**:

1. にログインします。 [Adobe Commerce顧客アカウント](https://account.magento.com/customer/account/login).

1. 次の日： _マイアカウント_ ページで、 **[!UICONTROL Commerce]** タブをクリックして、アカウント内のプロジェクトを表示します。

1. クリック **プロジェクトページを表示** （内） [「プロジェクト」セクション](https://cloud.magento.com/cloud/project/).

1. プロジェクト名をクリックし、クラウドプロジェクトページを開きます ([!DNL Onboarding] UI) を参照してください。

   ![OBUI プロジェクトページ](../assets/onboarding-ui.png)

   ポータルを参照して、プロジェクトの計画を開始し、コードを開発し、UAT およびサイトの起動に備えるための役立つ情報やオプションを確認します。

## プロジェクトへのアクセスとユーザーの追加

ライセンス所有者は、コードへのアクセス、ブランチの管理、チケットの入力、およびサポート環境を提供するユーザーアカウントを追加できます。 これらのユーザーアカウントには、社内開発、コンサルタント、ソリューションスペシャリストが含まれます。

通常、ライセンス所有者が作成する必要があるユーザーは、 _テクニカル管理者_. 技術管理者は、開発者用のユーザーアカウントの作成、環境権限の設定、すべてのブランチと環境の管理をおこなうための管理者アクセス権を持つユーザーアカウントが必要です。 技術管理者には、デベロッパー、コンサルタント、 [Adobeソリューションパートナー](https://business.adobe.com/products/magento/partners.html)、または自分自身。

テクニカル管理者は、 [!DNL Cloud Console]または `magento-cloud` CLI。

### ユーザー登録

クラウドインフラストラクチャのプロジェクトと環境では、Adobe Commerceに登録ユーザーのみを追加できます。 新しいユーザーがいる場合は、 [口座に登録する](https://account.magento.com/customer/account/login/) およびを使用して、アカウントプロファイルに関連付けられた電子メールアドレスを指定します。

### 共有アカウントへのアクセス

ライセンス所有者は、アカウントの共有アクセスを設定できます。 共有アクセスを使用すると、信頼できる従業員やサービスプロバイダーがヘルプセンターを使用して、クラウドインフラストラクチャプロジェクト上のAdobe Commerceに関連するサポートチケットを送信および追跡できます。 設定手順については、 [共有アクセス] 記事を参照してください。

### [!DNL Cloud Console]

以下を使用すると、 [[!DNL Cloud Console]](cloud-console.md) プロジェクトを管理するには、ユーザーアカウントを追加し、ストアの開発を開始します。 ライセンス所有者、技術管理者ユーザー、開発者は、 [!DNL Cloud Console] すべての環境とブランチ、環境変数、環境設定、ルートを管理する。

**次の手順で[!DNL Cloud Console]**:

1. にログインします。 [マイアカウント](https://account.magento.com/customer/account/login).

1. 次の日： _マイアカウント_ ページで、 **[!UICONTROL Commerce]** タブをクリックして、アカウント内のプロジェクトを表示します。

1. 次をクリック： **プロジェクト** 「 」タブをクリックし、プロジェクトを選択します。

1. クリック **インフラストラクチャへのアクセス**&#x200B;をクリックし、 **プロジェクトアクセス (Web UI)**.

   ![クラウドプロジェクトポータル](../assets/obui-project-access.png)

## 新規登録してAdobeの状態

クラウドインフラストラクチャプラットフォーム環境および関連サービスに関するAdobe Commerceのアップデートを、 [ステータスページ].

このページには、Adobe Commerceのコンポーネントとサービスのステータスと、インシデントレポート、サービスのアップグレード、計画的な停止、定期的なメンテナンスに関する通知が表示されます。 プロジェクトで作業中の人は誰でも、Adobe Commerceステータスサイトを購読して、電子メールまたはSlackでイベントの通知や更新を受け取ることができます。 Adobeステータス配信登録をカスタマイズして、地域やイベント別に特定の製品を追跡できます。

>[!TIP]
>
> 新しい [!DNL Cloud Console] プロジェクトと環境のアクティビティを表示します。
>
>**次の手順**: [Cl[!DNL ]Cloud コンソールにログインします。](cloud-console.md)

<!-- link definitions -->

[セールス]: https://business.adobe.com/products/magento/get-demo.html
[共有アクセス]: https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#shared-access
[ステータスページ]: https://status.adobe.com/products/503473
