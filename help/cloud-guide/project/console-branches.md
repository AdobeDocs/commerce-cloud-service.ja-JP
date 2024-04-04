---
title: 「 [!DNL Cloud Console]"
description: を使用して、クラウドインフラストラクチャ上のAdobe Commerceの環境ブランチを管理する方法を説明します。 [!DNL Cloud Console].
role: Developer
feature: Cloud, Install
exl-id: 2c4ef149-fdb9-473f-91fd-5e6421ac5a43
source-git-commit: a5af3cc6e174a731a8f2ff198acd794384ef4585
workflow-type: tm+mt
source-wordcount: '1589'
ht-degree: 0%

---

# でブランチを管理 [!DNL Cloud Console]

環境の管理には、 [!DNL Cloud Console] または `magento-cloud` CLI。 プロジェクトファイルは Git リポジトリに保存されます。 Git コマンドを使用してコードを管理できますが、 `magento-cloud` CLI は、Git コマンドでは操作できないのに対して、プラットフォームの機能を操作するように設計されています。 詳しくは、 [Git コマンド](../dev-tools/cloud-cli-overview.md#git-commands) （cloud CLI のトピック）を参照してください。

このトピックでは、 [!DNL Cloud Console] 移動先：

- 環境の追加または削除
- 同期 (`git pull`) を親環境から
- 結合 (`git push`) を親環境に追加します。

>[!TIP]
>
>プロステージング環境と実稼動環境からブランチを作成することはできません。 次の場所から `master` 分岐。

## 環境の作成

分岐戦略は、コードを開発し、開発ブランチに拡張機能を追加する、一般的な Git ワークフローを使用します。 詳しくは、 [スターター](../architecture/starter-architecture.md) および [Pro](../architecture/starter-develop-deploy-workflow.md) アーキテクチャの概要。

- スターターの場合は、 `staging` ～から分岐する `master` 分岐してから分岐 `staging` 開発用。
- Pro の場合、 `Integration` 環境。

お使いのアカウントでサポートされる数は限られています ![活動枝](../../assets/icon-active.png){width="32"} (active) and an unlimited number of ![inactive branch](../../assets/icon-inactive.png){width="32"} （非アクティブ）開発ブランチ。 アクティブな分岐と非アクティブな分岐を管理するには、 [!DNL Cloud Console] または Cloud CLI。 ブランチを削除する前に、そのブランチを非アクティブ化します。このブランチは、 _環境_ リスト： _非アクティブ_. 後でブランチを再アクティブ化することもできます。 [ブランチを削除](../dev-tools/cloud-cli-overview.md#) （環境設定内、または Cloud CLI を使用）。

開発用に追加のアクティブな環境が必要な場合は、 [サポートチケット](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket).

**ブランチを追加するには**:

1. にログインします。 [[!DNL Cloud Console]](https://console.adobecommerce.com).

1. 次の場所からプロジェクトを選択： _すべてのプロジェクト_ リスト。

1. 環境を選択します。

   >[!TIP]
   >
   >新しいブランチはこの環境から複製されます。 作成しようとしている環境に類似した親環境を選択します。

1. クリック **[!UICONTROL Branch]**.

   ![ブランチの作成](../../assets/button-branch.png){width="150"}

1. Adobe Analytics の _分岐元…_ フォームで、ブランチ名を入力します。

   環境 _名前_ 環境とは異なる _ID_ 環境名にスペース文字または大文字を使用する場合にのみ有効です。 環境 ID は、すべて小文字、数字、使用可能な記号で構成されます。 環境名の大文字は ID では小文字に変換され、環境名のスペースはダッシュに変換されます。

   環境名 **できません** Linux シェル用または正規表現用に予約された文字を含めます。 使用不可の文字には中括弧 (`{ }`)，括弧，アスタリスク (`*`)，山括弧 (`>`), ampersand (`&`), % (<code>%</code>)、およびその他の文字。

1. を選択します。 **[!UICONTROL Environment type]**.

1. クリック **[!UICONTROL Create Branch]**.

1. 環境がデプロイされるまでお待ちください。

   デプロイ時の環境のステータスは次のとおりです。  **処理中**. デプロイメントが正常に完了すると、ステータスが **成功**.

## 非アクティブな分岐を作成

非アクティブなブランチは、Adobe Commerce Cloudコンソールまたは CLI から作成できません。 非アクティブなブランチを作成する場合は、Git リポジトリで作成し、 `environment.Parent` オプションを使用して、

```bash
git push -o "environment.Parent=<parent branch>" <origin> <branch>
```

## 環境の削除

環境を削除する前に、非アクティブ化する必要があります。 環境が非アクティブになったら、その環境を削除できます。

**環境を非アクティブ化するには**:

1. にログインします。 [[!DNL Cloud Console]](https://console.adobecommerce.com).

1. 次の場所からプロジェクトを選択： _すべてのプロジェクト_ リスト。

1. ナビゲーションバーから環境を選択します。 _環境_ リスト。

1. 上部のナビゲーションバーの右側にある設定アイコンをクリックすると、環境設定が開きます。

1. 次の日： _[!UICONTROL General]_タブで、下にスクロールして_[!UICONTROL Deactivate environment]_ 「 」セクションで、「 」をクリックします。 **[!UICONTROL Deactivate environment and delete data]** そして指示に従う

## 環境の同期

環境（またはブランチ）の同期は、 `git pull origin <parent>`. 更新したコードは親環境から同期できます。 この機能は、 [!DNL Cloud Console] Starter および Pro のすべての環境に対応しています。

Pro プランの場合は、ステージングと実稼動からにを同期できます `master` 分岐。 この同期は、データではなく、コードの取り込みとプッシュのみをおこないます。 データを同期するには、データベースのデータをダンプし、別の環境のデータベースにプッシュします。 詳しくは、 [静的ファイルとデータの移行とデプロイ](/help/cloud-guide/deploy/staging-production.md#migrate-static-files).

**環境を同期するには**:

1. にログインします。 [[!DNL Cloud Console]](https://console.adobecommerce.com).

1. 次の場所からプロジェクトを選択： _すべてのプロジェクト_ リスト。

1. 環境リストで、同期するブランチの名前をクリックします。

1. （同期）をクリックします。

   ![環境の同期](../../assets/button-sync.png){width="150"}

1. 同期する項目を選択します。

   - データ（データとファイル）の置き換えは、データベース内の変更と親ブランチのコンテンツファイルを同期します。
   - 「結合」(Merge) — （コード）更新されたコードを親ブランチから同期します。

   これにより、コピーして使用する CLI コマンドも作成されます。

1. クリック **同期**.

## 親環境と結合

環境（またはブランチ）のマージは、 `git push origin`. 結合して、更新されたコードを環境から親環境にプッシュします。 このコードを `master`. を使用して、ステージングおよび実稼動にデプロイできます。 `merge` コマンドを使用します。

**親環境とマージするには**:

1. にログインします。 [[!DNL Cloud Console]](https://console.adobecommerce.com).

1. 次の場所からプロジェクトを選択： _すべてのプロジェクト_ リスト。

1. 環境リストで、マージするブランチの名前をクリックします。

1. （結合）をクリックします。

   ![環境の結合](../../assets/button-merge.png){width="150"}

1. クリック **結合** をクリックして、アクションを確定します。

## ログを表示

を通じて [!DNL Cloud Console]を使用すると、ビルド、デプロイ、デプロイ履歴など、環境に関する様々なログを確認できます。

の場合 **スターター**&#x200B;を使用すると、ビルドとデプロイのログとデプロイの履歴を確認できます。 これらの環境には、 `master` （実稼動）ブランチと、そのブランチから作成されたすべてのブランチ。

の場合 **Pro**&#x200B;を参照する場合、各環境で次のログを確認できます。

- 統合：ビルドとデプロイおよびデプロイの履歴
- ステージング — ログとデプロイメント履歴を作成します。 SSH を使用してサーバーにログインし、デプロイログを表示します。
- 「実稼働」(Production) — ログとデプロイメント履歴を作成します。 SSH を使用してサーバーにログインし、デプロイログを表示します。

**ログを[!DNL Cloud Console]**:

1. にログインします。 [[!DNL Cloud Console]](https://console.adobecommerce.com).

1. 次の場所からプロジェクトを選択： _すべてのプロジェクト_ リスト。

1. 環境を選択します。

   環境ビューには、 [アクティビティリスト](activity-stream.md) 示している _最近_ 同期、結合、ブランチ、バックアップなどを含む、アクションごとに 1 つのエントリが試行されました。 クリック **すべて** を参照してください。

1. ビルドログを表示するには、アカウント上のデプロイメントレコードごとに「成功」リンクまたは「失敗」リンクを選択します。

>[!TIP]
>
>次をクリック： **フィルター条件** アイコンをクリックして、表示するメッセージのタイプを選択します。

## プライベート Git リポジトリからコードを取り込む

クラウドインフラストラクチャ上のAdobe Commerceプロジェクトに、プライベート Git リポジトリのコードを含めることができます。 例えば、プライベートリポジトリ内にカスタムモジュールやテーマのコードがあるとします。 これをおこなうには、プロジェクトの公開 SSH 鍵をプライベート Git リポジトリに追加し、プロジェクトを更新する必要があります `composer.json` ファイル。

プライベート GitHub リポジトリにデプロイメントキーを追加するには、そのリポジトリの管理者である必要があります。 GitHub では、デプロイキーを 1 つのリポジトリに対してのみ使用できます。

プロジェクトが複数のリポジトリにアクセスする場合は、自動化されたユーザーアカウントに SSH キーを添付できます。 このアカウントは、人間では使用されないので、 [マシンユーザー](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/managing-deploy-keys). マシンアカウントを共同作業者として追加するか、リポジトリへのアクセス権を持つチームにマシンユーザーを追加します。

>[!INFO]
>
>Adobeでは、このコードを追加して、プロジェクト Git リポジトリーにマージすることをお勧めします。 接続を設定しない場合、ビルドに関する問題が発生する可能性があります。

**SSH 公開鍵を検索するには**:

1. にログインします。 [[!DNL Cloud Console]](https://console.adobecommerce.com).

1. 次の場所からプロジェクトを選択： _すべてのプロジェクト_ リスト。

1. 上部のナビゲーションバーの右側にある設定アイコンをクリックします。

1. In _プロジェクト設定_&#x200B;をクリックし、 **[!UICONTROL Deploy Key]**.

1. 次の Git ベースの方法の 1 つで使用するために、デプロイキーをクリップボードにコピーします。

>[!BEGINTABS]

>[!TAB GitHub]

### GitHub デプロイキーを入力します

GitHub では、デプロイキーはデフォルトで読み取り専用です。

**プロジェクトの公開鍵を GitHub デプロイキーとして入力するには**:

1. GitHub リポジトリに管理者としてログインします。
1. リポジトリをクリックします。 **[!UICONTROL Settings]** タブをクリックします。

   >[!NOTE]
   >
   >このオプションが表示されない場合、リポジトリ管理者としてログインしておらず、このタスクを完了できません。 GitHub リポジトリの管理者に問い合わせて、この操作をおこなってください。

1. 次の日： _設定_ タブをクリックし、 **[!UICONTROL Deploy Keys]**.
1. クリック **[!UICONTROL Add deploy key]**.
1. 画面の指示に従います。

In `composer.json`を使用する場合は、 `<user>@<host>:<.git</code>` 形式、または `ssh://<user>@<host>:<port>/<path>.git` 非標準ポートを使用する場合。

>[!TAB Bitbucket]

### Bitbucket デプロイキーを入力

**プロジェクトの公開鍵を Bitbucket デプロイキーとして入力するには**:

1. 管理者として Bitbucket リポジトリにログインします。
1. 左側のナビゲーションで、 **[!UICONTROL Settings]**.
1. 一般/ **[!UICONTROL Deployment Keys]**.
1. クリック **[!UICONTROL Add Key]**.
1. 画面の指示に従います。

>[!TAB GitLab]

### GitLab デプロイキーを入力

**プロジェクトの SSH 公開鍵を GitLab デプロイキーとして追加するには**:

1. GitLab リポジトリに所有者としてログインします。
1. 次を確認します。 _パイプライン_ オプションは、プロジェクトで有効になっています。

   1. プロジェクト設定で、 **[!UICONTROL Visibility, project, features, permissions]** 」セクションに入力します。
   1. 必要に応じて、 **[!UICONTROL Pipelines]** をクリックして、オプションを有効にします。

1. CI/CD 設定に SSH 公開鍵を追加します。

   1. 左側のナビゲーションで、設定/ **[!UICONTROL CI / CD]**.
   1. 「キーをデプロイ」をクリックします。 **展開** をクリックして、キーを設定します。
   1. Adobe Analytics の _キーをデプロイ_ フォームで、デプロイキー名を **[!UICONTROL Title]** 」フィールドに SSH 公開鍵を入力し、 **[!UICONTROL Key]** フィールドに入力します。
   1. クリック **[!UICONTROL Add Key]** をクリックして設定を保存します。

>[!ENDTABS]

## セキュアな環境とブランチ

Web ブラウザーを使用して、任意の場所からプロジェクトおよび環境にアクセスできます。 [!DNL Cloud Console]. 実稼動環境、ストア、サイトのセキュリティが設定されている場合があります。 この節では、開発者や DBA などに厳密に対応するために、統合環境とステージング環境を保護する方法について説明します。

>[!WARNING]
>
>**禁止** Pro ステージング環境と実稼動環境を保護するには、次の方法を使用します。 これは Fastly のキャッシュを壊します。 以下を使用します。 [ブロック](../cdn/fastly-vcl-blocking.md) Adobe Commerceの Fastly CDN で利用できる機能。

**環境を保護するには**:

1. にログインします。 [[!DNL Cloud Console]](https://console.adobecommerce.com).

1. 次の場所からプロジェクトを選択： _すべてのプロジェクト_ リスト。

1. 環境を選択し、ナビゲーションバーの設定アイコンをクリックします。

1. 環境設定で _一般_ タブ、クリック **オン** 対象： **[!UICONTROL HTTP access control enabled]** ：セキュアなアクセスを有効にします。 アクセスをフィルタリングするために、資格情報か IP アドレスのどちらかを選択できます。

1. 資格情報でフィルターするには、「 **[!UICONTROL Add Login]**、ユーザー名とパスワードを入力し、「 **[!UICONTROL Add Login]** を追加します。

1. IP アドレスでフィルタリングするには、IP アドレスを `deny` または `allow`. 例：

   ```text
   123.456.789.111/29 allow
   123.456.789.112/29 allow
   234.123.567.111/29 allow
   0.0.0.0/0 deny
   ```

1. クリック **[!UICONTROL Save]**. これにより、環境が再デプロイされ、セキュリティと設定が更新されます。 Adobeでは、セキュリティ設定を完了した後に環境をテストすることをお勧めします。
