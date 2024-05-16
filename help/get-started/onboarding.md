---
title: '''[!DNL Onboarding] Commerceへ'
description: クラウドアカウントにアクセスし、クラウドインフラストラクチャプロジェクトでAdobe Commerceを設定します。
role: Admin
recommendations: noDisplay, catalog
exl-id: c6b768d7-d835-4a8d-aad9-1c0324f7570d
source-git-commit: abe9aa36b907be8bdfdf42e6f28f1e1eac68fecf
workflow-type: tm+mt
source-wordcount: '760'
ht-degree: 0%

---

# [!DNL Onboarding] Commerceへ

AdobeがクラウドインフラストラクチャサブスクリプションでCommerceをアクティブ化すると、最初のプロジェクトおよびコードへのアクセスは、ライセンスオーナー（アカウントオーナー）として指定されたユーザーのみが利用できるようになります。

ライセンスオーナーは、企業または金融組織の担当者で、クラウドインフラストラクチャアカウント上のAdobe Commerceの支払いおよびその他のビジネス関連の取引を管理します。 この人物はAdobeとの接点として機能します。 ライセンス所有者を変更する必要がある場合は、Adobeアカウントチームにお問い合わせください。

プロジェクトをすばやくオンボーディングして、ライブデプロイメント用のサイトの開発を開始できるようにするには、必要な設定を完了し、 [!DNL onboarding] 件のタスク。 通常、ライセンス所有者は管理者アクセスを保護し、セットアップ、カスタマイズ、開発作業に役立つ技術管理者ユーザーを作成することで、プロセスを開始します。

## Cloud アカウントへの新規登録

Adobe Commerce on cloud infrastructure アカウントをお持ちでない場合は、にお問い合わせください。 [売上]. 新規登録すると、Adobeでアカウントが作成され、プロジェクトインターフェイスへのアクセス方法を説明するお知らせメールが送信されます。 このメールにはリンクが含まれているので、アカウントにログインしてプロジェクトの初期設定を完了できます。

### Cloud [!DNL Onboarding] UI

のAdobe Commerce on cloud infrastructure プロジェクトページ（[!DNL Onboarding] UI）には、プロジェクトとサービスを設定し、アクセス権を決定し、開発を開始するための「はじめに」チェックリストが用意されています。 OBUI から、次の操作を実行できます。

- 技術管理者（プロジェクトとブランチを管理できるスーパーユーザー）を追加します
- へのリンクを含め、プロジェクト環境にアクセスします [!DNL Cloud Console]
- さらに詳しいテストへのリンクを含む、ユーザー受け入れテスト（UAT）のチェックリストをすばやく確認します

**プロジェクトページを開くには、次の手順に従います**:

1. にログイン [Adobe Commerce顧客アカウント](https://account.magento.com/customer/account/login).

1. 日 _マイアカウント_ ページで、 **[!UICONTROL Commerce]** タブをクリックして、アカウントのプロジェクトを表示します。

1. クリック **プロジェクト ページの表示** が含まれる [プロジェクト セクション](https://cloud.magento.com/cloud/project/).

1. プロジェクト名をクリックし、クラウドプロジェクトページ（[!DNL Onboarding] UI など）。

   ![OBUI プロジェクトページ](../assets/onboarding-ui.png)

   ポータルを参照すると、プロジェクトの計画、コードの開発、UAT とサイトの立ち上げの準備に役立つ情報やオプションが得られます。

## プロジェクトへのアクセスとユーザーの追加

ライセンス所有者は、ユーザーアカウントを追加して、コードへのアクセス、ブランチの管理、チケットの入力、環境のサポートを行うことができます。 これらのユーザーアカウントには、社内開発、コンサルタント、ソリューションスペシャリストが含まれます。

通常、ライセンス所有者が作成する必要があるユーザーはのみです。 _技術管理者_. 技術管理者には、開発者向けのユーザーアカウントの作成、環境権限の設定、すべてのブランチと環境の管理を行うための管理者アクセス権を持つユーザーアカウントが必要です。 技術管理者は、開発者、コンサルタント、 [Adobeソリューションパートナー](https://business.adobe.com/products/magento/partners.html)、または自分自身。

技術管理者は、プロジェクトポータルを通じて、次から作成できます [!DNL Cloud Console]または、を使用してコマンドラインから `magento-cloud` CLI。

### ユーザー登録

クラウドインフラストラクチャプロジェクトおよび環境では、登録済みユーザーのみをAdobe Commerceに追加できます。 新しいユーザーがいる場合は、次の操作を行います。 [アカウントの登録](https://account.magento.com/customer/account/login/) およびアカウントプロファイルに関連付けられたメールアドレスを提供します。

### 共有アカウントアクセス

ライセンス所有者は、アカウントの共有アクセスを設定できます。 共有アクセスを使用すると、信頼できる社員やサービスプロバイダーは、ヘルプセンターを使用して、クラウドインフラストラクチャプロジェクトでのAdobe Commerceに関連するサポートチケットを送信および追跡できます。 設定手順については、を参照してください [共有アクセス] ヘルプセンターの記事。

### [!DNL Cloud Console]

を使用できます [[!DNL Cloud Console]](cloud-console.md) プロジェクトを管理するには、ユーザーアカウントを追加し、ストアの開発を開始します。 ライセンス所有者、技術管理者ユーザーおよび開発者は、 [!DNL Cloud Console] すべての環境とブランチ、環境変数、環境設定およびルートを管理します。

**にアクセスするには[!DNL Cloud Console]**:

1. へのログイン [マイアカウント](https://account.magento.com/customer/account/login).

1. 日 _マイアカウント_ ページで、 **[!UICONTROL Commerce]** タブをクリックして、アカウントのプロジェクトを表示します。

1. 「」をクリックします **プロジェクト** タブをクリックして、プロジェクトを選択します。

1. クリック **インフラストラクチャアクセス**&#x200B;を選択し、 **Project Access （Web UI）**.

   ![クラウドプロジェクトポータル](../assets/obui-project-access.png)

## Adobeステータスへの新規登録

から、クラウドインフラストラクチャプラットフォーム環境および関連サービスに関するAdobe Commerceの更新を取得します [ステータスページ].

このページには、Adobe Commerceのコンポーネントとサービスのステータスが表示され、その後インシデントの報告、サービスのアップグレード、計画的な停止、予定されているメンテナンスなどに関する通知が表示されます。 プロジェクトに取り組んでいるユーザーは誰でも、Adobe Commerce ステータスサイトを購読して、メールまたはSlackでイベント通知や最新情報を受け取ることができます。 Adobeステータスのサブスクリプションをカスタマイズすると、地域およびイベント別に特定の商品をトラッキングできます。

>[!TIP]
>
> 新しいを開きます [!DNL Cloud Console] を参照し、プロジェクトと環境のアクティビティを確認します。
>
>**次の手順**: [Cl[!DNL ]Cloud Console にログインします。](cloud-console.md)

<!-- link definitions -->

[売上]: https://business.adobe.com/products/magento/get-demo.html
[共有アクセス]: https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#shared-access
[ステータスページ]: https://status.adobe.com/products/503473
