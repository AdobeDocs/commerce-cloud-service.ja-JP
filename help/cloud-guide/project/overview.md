---
title: クラウドインフラストラクチャプロジェクト
description: クラウドインフラストラクチャ上のAdobe Commerceの概要を読む [!DNL Cloud Console] アカウント設定へのアクセス方法を説明します。
last-substantial-update: 2024-02-06T00:00:00Z
exl-id: ae862898-9b4d-45ed-b370-e82cc6f99017
source-git-commit: abe9aa36b907be8bdfdf42e6f28f1e1eac68fecf
workflow-type: tm+mt
source-wordcount: '987'
ht-degree: 0%

---

# クラウドインフラストラクチャプロジェクト

Adobe Commerce on cloud infrastructure プロジェクトには、Git ブランチ、関連する環境および [!DNL Commerce] アプリケーション。 環境には、 [!DNL Commerce] アプリケーション（データベース、web サーバー、キャッシュサーバーを含む）。

Adobeが [!DNL Cloud Console] プロジェクトのあらゆる側面を完全に管理する開発者ツール アカウント所有者は、すべての環境に対するフルアクセス権を持っています。

## [!DNL Cloud Console]

The [!DNL Cloud Console] は、使いやすい形式でコマースコードを作成、管理、デプロイするためのインタラクティブな方法を提供します。 [にログインします。 [!DNL Cloud Console]](https://console.adobecommerce.com) をクリックして、プロジェクトリストを表示します。 管理者または特定の環境タイプに対してアクセスする権限を持つプロジェクトのみが表示されます。 Adobeソリューションパートナーの場合、サポート対象のクライアントに対して複数のプロジェクトが表示される場合があります。

>[!TIP]
>
>プロジェクトが表示されない場合は、 [アカウント所有者またはプロジェクト管理者](../project/user-access.md) プロジェクトに関連付けられ、アクセスをリクエストします。 初めてのユーザーの場合、 [オンボーディングトピック](../../get-started/onboarding.md#cloud-console) （内） _基本を学ぶ_ ガイド。

The _すべてのプロジェクト_ 表示には、アクセス権を持つすべてのプロジェクトが一覧表示されます。 次をクリックできます。 **[!UICONTROL Show filters]** タイプ、地域、プランでプロジェクトリストをフィルタリングします。

![プロジェクトリスト](../../assets/ui-allprojects-list.png)

### プロジェクトの概要

からプロジェクトを選択する _すべてのプロジェクト_ 「 」リストをクリックすると、プロジェクトの概要が開きます。 プロジェクトの概要には、環境セレクターと設定ボタンを含む、プロジェクトのナビゲーションバーが常に表示されます。

![プロジェクトナビゲーション](../../assets/project-nav.png)

環境が選択されていない場合、プロジェクトの概要には、プレビュー領域にプロジェクトの詳細の概要が表示されます。

- プロジェクト名
- 地域、プロジェクト ID
- 計画、割り当てられたストレージ、環境、ユーザー
- 次を含む Storefront URL **[!UICONTROL Set a custom domain]** ボタン

また、主なプロジェクトの概要では、次の操作をおこないます。

- 環境ビューには、のリストまたはツリー表示が表示されます ![活動枝](../../assets/icon-active.png){width="32"} (active) and ![inactive branch](../../assets/icon-inactive.png){width="32"} （非アクティブ）環境。
- [アクティビティストリーム](activity-stream.md) プロジェクトの実行中、保留中、最近のアクティビティを表示します。
<!-- - Apps & Services—Shows a topology of service containers -->

の場合 **スターター** プロジェクトの場合、次の場所から始まるブランチの階層があります。 `master` （実稼動）。 作成したブランチは、 `master` 分岐。 Adobeは、 `staging` ブランチを作成してから、 `integration` 開発用のブランチ。 詳しくは、 [スターターアーキテクチャ](../architecture/starter-architecture.md).

の場合 **Pro**&#x200B;の場合、次の場所から始まる分岐の階層が存在します `production` から `staging` から `integration`. The ![専用アイコン](../../assets/icon-dedicated.png){width="32"} アイコンは、ブランチが専用の環境にデプロイされることを示します。 作成したブランチは、 `integration` 分岐。 詳しくは、 [Pro アーキテクチャ](../architecture/pro-architecture.md).

![Pro 環境リスト](../../assets/pro-environments.png)

### 環境の概要

プロジェクトナビゲーションバーから環境を選択すると、概要とナビゲーションバーが変更され、選択した環境に焦点が当てられます。 ナビゲーションバーには、ブランチコントロール（ブランチ、マージ、同期）と設定ボタンが含まれます。

![環境が選択されました](../../assets/environment-selected.png)

環境の概要には、環境の詳細の概要がプレビュー領域に表示されます。

- 環境名、タイプ
- 地域、プロジェクト ID
- バックアップを含む、最後のアクティビティの日時
- HTTP アクセスと検索エンジンのステータス
- 環境に割り当てられたマシン名
- 環境ステータス（アクティブまたは非アクティブ）
- 次を含む Storefront URL **[!UICONTROL Set a custom domain]** ボタン

また、メイン環境の概要では、次の操作を実行します。

- [アクティビティストリーム](activity-stream.md) は、主な環境の概要を構成し、選択した環境の実行中、保留中、最近のアクティビティを表示します。
<!-- - Services tab shows and Apps & Services menu, including overview and configuration tabs for each service. -->
- [「バックアップ」タブ](../storage/snapshots.md#create-a-manual-backup) には、保存されたバックアップの一覧、バックアップ操作の履歴、および [ バックアップ ] ボタンが表示されます。

### ストアフロントにアクセス

アクティブな各環境にはストアフロントがあります。 上部のナビゲーションから環境を選択し、環境の概要で URL をクリックします。 また、 **[!UICONTROL URLs]** リストを追加します。

Web アクセス URL には、次の情報を含めることができます。

```terminal
https://<branch>-<unique-ID>-<project-ID>.<region>.magentosite.cloud/
```

- **一意の ID** = 7 個のランダムな英数字
- **プロジェクト ID** = 13 文字のプロジェクト ID
- **地域** = AWSまたは Azure の地域名。 [地域 IP アドレス](regional-ip-addresses.md)

Pro 実稼動環境とステージング環境には、次のリンクを使用してアクセスできる 3 つのノードが含まれています。

- ロードバランサー URL:

   - `http[s]://<your-domain>.c.<project-ID>.ent.magento.cloud`
   - `http[s]://<your-staging-domain>.c.<project-ID>.ent.magento.cloud`

- 3 台の冗長サーバの 1 つに直接アクセス：

   - `http[s]://<your-domain>.{1|2|3}.<project-ID>.ent.magento.cloud`
   - `http[s]://<your-staging-domain>.{1|2|3}.<project-ID>.ent.magento.cloud`

  実稼動 URL は、コンテンツ配信ネットワーク (CDN) で使用されます。

## 設定

を開きます。 _設定_ パネルを表示するには、 ![プロジェクトアイコンを設定](../../assets/icon-configure.png){width="36"} （設定）アイコンをクリックします。

### プロジェクト設定

**[!UICONTROL Project Settings]** プロジェクトレベルのコントロールのメニューを展開し、ユーザーや変数などを管理できます。

| オプション | 説明 |
|--------------|-------------------------------------------------------------------------------------------------------------------------------|
| 一般 | バックアップやメンテナンスのスケジュール設定に使用するタイムゾーンを管理します。 |
| アクセス | 管理 [ユーザーアクセス](user-access.md) をプロジェクトおよび環境タイプに追加します。 |
| 証明書 | プロジェクトに関連付けられている SSL 証明書の一覧を表示します。 |
| キーをデプロイ | 公開鍵をプロジェクトコードリポジトリに追加して表示します。 |
| ドメイン | プロジェクトにドメイン名を追加します。 詳しくは、 [ドメインの管理](../cdn/fastly-custom-cache-configuration.md#manage-domains). |
| 統合 | 追加と管理 [統合](../integrations/overview.md)：正常性通知や Web フックなど。 |
| 変数 | 追加 [プロジェクトレベルの変数](../environment/variable-levels.md) すべての環境でビルド時および実行時に使用できる |

{style="table-layout:auto"}

### 環境設定

クリック **[!UICONTROL Environments]** をクリックし、リストから特定の環境を選択して、サイト設定や環境変数などを管理するコントロールを表示します。

| オプション | 説明 |
| --------- | -------------------------------------------------------------------------------------------------------------------------------- |
| 一般 | 表示名、環境タイプ、親環境を設定します。<br>異なる環境設定を切り替える： |
|           | **送信メールを有効にする**：送信 [送信メール](outgoing-emails.md) SMTP プロトコルを使用する環境から。 |
|           | **検索エンジンに表示しない**：サイトからの検索エンジンのインデクサーおよびクローラーをブロックします。 |
|           | **HTTP アクセス制御**：のセキュリティ設定を有効にします [!DNL Cloud Console] ログインと IP アドレスのアクセス制御を使用する。 |
|           | ステータスは `active` または `inactive`. ほとんどの作業はアクティブな環境で行われます。 環境は、非アクティブ化または削除できます。 |
| 変数 | 表示、作成、管理 [環境レベルの変数](../environment/variable-levels.md) 実行時に使用できます。 |
| ドメイン | 次のリストを表示： [設定済みのルート](../routes/routes-yaml.md). |

{style="table-layout:auto"}

>[!WARNING]
>
>**禁止** HTTP アクセス制御メソッドを使用して、Pro ステージング環境と実稼動環境を保護します。 これは Fastly のキャッシュを壊します。 代わりに、 [ブロック](../cdn/fastly-vcl-blocking.md) Adobe Commerceの Fastly CDN で利用できる機能。

## Fastly およびNew Relicの資格情報

プロジェクトには以下が含まれます [Fastly](../cdn/fastly.md) および [New Relic](../monitor/new-relic-service.md). プロジェクトの詳細には、プロジェクト計画に関する情報と、これらの統合に関する重要なライセンスおよびトークンが表示されます。 資格情報とサービスへの初期アクセス権を持つのは、ライセンス所有者のみです。 必要に応じて、これらの資格情報を技術および開発者のリソースに指定します。

- [Fastly](https://www.fastly.com/) は、クラウドインフラストラクチャプロジェクト上のAdobe Commerceに対して、コンテンツ配信 (CDN)、画像最適化、セキュリティサービス（DDoS および WAF）を提供します。 詳しくは、 [Fastly 認証情報の取得](../cdn/fastly-configuration.md#get-fastly-credentials).

- [New Relic](../monitor/new-relic-service.md) は、ステージング環境と実稼動環境用のアプリケーション指標およびパフォーマンス情報を提供します。

以下を使用します。 [Cloud CLI](../dev-tools/cloud-cli-overview.md) 統合トークンや ID などを確認するには、以下の手順を実行します。

```bash
magento-cloud subscription:info services
```
