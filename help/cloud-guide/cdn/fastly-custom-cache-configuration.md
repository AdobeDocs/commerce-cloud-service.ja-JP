---
title: キャッシュ設定のカスタマイズ
description: Fastly サービスの設定が完了した後に、キャッシュ設定を確認し、カスタマイズする方法を説明します。
feature: Cloud, Configuration, Iaas, Cache
exl-id: f1fc85d4-7867-4bb5-9f11-bc8d2d80383b
source-git-commit: 8a0523f1714b6ea41887e99b5c31294cf5e5255e
workflow-type: tm+mt
source-wordcount: '1808'
ht-degree: 0%

---

# キャッシュ設定のカスタマイズ

ステージング環境と実稼動環境で Fastly サービスを設定してテストした後、キャッシュ設定を確認し、カスタマイズします。 例えば、設定を更新して、TLS による HTTP 要求の Fastly へのリダイレクトを強制したり、パージ設定を更新したり、開発時にサイトのパスワード保護に必要な基本認証を有効にしたりできます。

以下の節では、一部のキャッシュ設定の概要と手順を説明します。 使用可能な設定オプションに関する追加情報を見つけるには、 [Magento2 の Fastly CDN Module](https://github.com/fastly/fastly-magento2/tree/master/Documentation) ドキュメント。

## TLS を強制

Fastly が _TLS を強制_ 暗号化されていないリクエスト (HTTP) を Fastly にリダイレクトするためのオプションです。 ステージング環境または実稼動環境に [有効な SSL/TLS 証明書](fastly-configuration.md#provision-ssltls-certificates)の場合は、ストアの Fastly 設定を更新して、「 TLS を強制」オプションを有効にできます。 Fastly を見る [TLS を強制ガイド](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/FORCE-TLS.md) （内） _Magento2 の Fastly CDN Module_ ドキュメント。

>[!NOTE]
>
>「 TLS を強制」オプションの有効化は、クラウドインフラストラクチャストア上のAdobe Commerceに対して推奨されるベストプラクティスです。

## Extend Fastly タイムアウト

Fastly サービス設定では、管理者への HTTPS リクエストのデフォルトのタイムアウト時間として 180 秒を指定します。 タイムアウト時間を超えるリクエスト処理は、503 エラーを返します。 その結果、長い処理が必要なリクエストに応じて、または一括操作を実行しようとすると、503 エラーが発生する場合があります。

3 分以上かかるバルクアクションを完了するには、 _管理パスのタイムアウト_ 値：503 エラーを防ぎます。

>[!NOTE]
>
>Fastly UI で Admin 以外のの Fastly タイムアウトパラメーターを拡張するには、 [長期間のジョブのタイムアウトの増加](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/Edge-Modules/EDGE-MODULE-INCREASE-TIMEOUTS-LONG-JOBS.md).

**管理者の Fastly タイムアウトを拡張するには**:

{{admin-login-step}}

1. クリック **ストア** /設定/ **設定** > **詳細** > **システム** を展開します。 **フルページキャッシュ**.

1. Adobe Analytics の _Fastly 設定_ セクション、展開 **詳細設定**.

1. を設定します。 **管理パスのタイムアウト** の値（秒）。 この値は、10 分（600 秒）以下にする必要があります。

1. クリック **設定を保存** をクリックします。

1. ページがリロードされたら、「 」を選択します。 **VCL を Fastly にアップロード** （内） _Fastly 設定_ 」セクションに入力します。

VCL ファイルを生成するための管理者パスを `app/etc/env.php` 設定ファイル。

## パージオプションの設定

Fastly では、製品カテゴリ、製品アセット、コンテンツをパージするオプションなど、Magentoキャッシュ管理ページに複数のタイプのパージオプションが用意されています。 有効にすると、Fastly はイベントを監視して、これらのキャッシュを自動的にパージします。 パージオプションを無効にした場合、キャッシュ管理ページで更新を終了した後に、手動で Fastly キャッシュをパージできます。

パージオプションは次のとおりです。

- **カテゴリをパージ** — 単一の製品を追加および更新する際に、製品カテゴリのコンテンツ（製品コンテンツではなく）をパージします。 この機能を無効のままにして、製品と製品カテゴリをパージする製品のパージを有効にすることができます。
- **製品をパージ** — 製品に対する 1 回の変更を保存する際に、すべての製品および製品カテゴリのコンテンツをパージします。 製品のパージを有効にすると、価格の変更、製品オプションの追加、製品在庫の在庫切れ時に、顧客に即座に更新を提供するのに役立ちます。
- **CMS ページをパージ**- Adobe Commerce CMS でページを更新および追加する際に、ページコンテンツをパージします。 例えば、利用条件や返品ポリシーを更新する際に削除したい場合があります。 これらの変更をほとんどおこなわない場合、自動パージを無効にできます。
- **ソフトパージ** — 変更されたコンテンツを古いタイミングに従って古く、削除するように設定します。 古いタイミングに加えて、お客様は古いコンテンツを提供し、Fastly はバックグラウンドでコンテンツを更新します。

![パージオプションの設定](../../assets/cdn/fastly-purge-options.png)

**ファストリパージオプションを設定するには**:

1. Adobe Analytics の _Fastly 設定_ セクション、展開 **詳細設定** をクリックして、パージオプションを表示します。

1. パージオプションごとに、 **はい** 自動パージを有効にする **いいえ** 自動パージを無効にする。

   パージオプションを無効にした場合、そのカテゴリのキャッシュを _キャッシュ管理_ ページに貼り付けます。

1. クリック **設定を保存** をクリックします。

1. ページがリロードされたら、「 」を選択します。 **VCL を Fastly にアップロード** （内） _Fastly 設定_ 」セクションに入力します。

詳しくは、 [Fastly 設定オプション](https://github.com/fastly/fastly-magento2/blob/21b61c8189971275589219d418332798efc7db41/Documentation/CONFIGURATION.md#further-configuration-options).

## GeoIP の処理を設定

Fastly モジュールには、訪問者を自動的にリダイレクトしたり、取得した国コードと一致する店舗のリストを提供したりするための GeoIP 処理が含まれています。 既に GeoIP 処理用の拡張機能を使用している場合は、Fastly オプションを使用して機能を検証する必要が生じる場合があります。

**GeoIp 処理を設定するには**:

{{admin-login-step}}

1. クリック **ストア** /設定/ **設定** > **詳細** > **システム** を展開します。 **フルページキャッシュ**.

1. Adobe Analytics の _Fastly 設定_ セクション、展開 **詳細設定**.

1. 下にスクロールして、「 」を選択します。 **はい** から **GeoIP を有効にする**. 追加の設定オプションが表示されます。

1. GeoIP アクションの場合は、で訪問者が自動的にリダイレクトされるかどうかを選択します。 **リダイレクト** または、選択するストアのリストが提供されます。 **ダイアログ**.

1. の場合 **国マッピング**&#x200B;を選択します。 **追加** を使用して、リストから特定のAdobe Commerceストアにマッピングする 2 文字の国コードを入力します。

   ![GeoIP 国マップを追加](/help/assets/cdn/fastly-geo-code.png)

1. クリック **設定を保存** をクリックします。

1. ページの再読み込み後、「 」を選択します。 **VCL を Fastly にアップロード** （内） _Fastly 設定_ 」セクションに入力します。

>[!NOTE]
>
>現在のAdobe Commerce Fastly GeoIP モジュールの実装では、複数の Web サイト間でのリダイレクトをサポートしていません。

Fastly はまた、一連の [位置情報関連の VCL 機能](https://developer.fastly.com/reference/vcl/variables/geolocation/) カスタマイズされた位置情報のコーディング。

## Fastly Edge モジュールの有効化

Fastly Edge Modules は、テンプレートを介して UI コンポーネントと関連する VCL コードを定義できる柔軟なフレームワークです。 これらのモジュールを使用すると、カスタム VCL スニペットを使用する代わりに、ユーザーインターフェイスを通じて Fastly のサービス設定を簡単にカスタマイズおよび拡張できます。

エッジモジュールを使用すると、CORS ヘッダーや Cloud Sitemap の書き換えなどの特定の機能を有効にしたり、Adobe Commerceストアと他の CMS やバックエンドとの統合を設定したりできます。

Edge Modules メニューにアクセスして使用可能なモジュールを表示、設定、管理するには、 _Fastly Edge モジュールの有効化_ オプション。 詳しくは、 [Fastly エッジモジュール](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/Edge-Modules/EDGE-MODULES.md) （ Fastly CDN モジュールのドキュメント）を参照してください。

## バックエンドとオリジンシールドを設定する

バックエンド設定では、接触チャネルのシールドとタイムアウトを使用して、Fastly のパフォーマンスを微調整できます。 A _バックエンド_ は、Origin Shield と、キャッシュされたコンテンツを確認および提供するためのタイムアウト設定が設定された特定の場所（IP またはドメイン）です。

_原点遮蔽_ ストアに対するすべてのリクエストを特定の Point of Presence(POP) にルーティングします。 要求を受け取ると、POP はキャッシュされたコンテンツを確認して提供します。 キャッシュされない場合は、Shield POP に続き、コンテンツをキャッシュする Origin サーバーに続きます。 シールドは、接触チャネルへのトラフィックを直接削減します。

デフォルトの Fastly VCL コードは、クラウドインフラストラクチャサイト上のAdobe Commerceの接触チャネルシールドとタイムアウトのデフォルト値を指定します。 場合によっては、デフォルト値の変更が必要になることがあります。 例えば、最初のバイトまでの時間 (TTFB) エラーが発生した場合、 _最初のバイトタイムアウト_ の値です。

>[!NOTE]
>
>サイトで、 [Wordpress](fastly-vcl-wordpress.md)を使用する場合は、Fastly サービス設定をカスタマイズして、バックエンドを追加し、Adobe Commerceストアから Wordpress へのリダイレクトを管理します。 詳しくは、 [ファストリーエッジモジュール — その他の CMS/バックエンド統合](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/Edge-Modules/EDGE-MODULE-OTHER-CMS-INTEGRATION.md) Fastly モジュールのドキュメントを参照してください。

**バックエンドの設定を確認するには**:

{{admin-login-step}}

1. クリック **ストア** /設定/ **設定** > **詳細** > **システム** を展開します。 **フルページキャッシュ**.

1. を展開します。 **Fastly 設定** 」セクションに入力します。

1. 展開 **バックエンドの設定** をクリックし、デフォルトのバックエンドをチェックするギアを選択します。 モーダルが開き、現在の設定と変更するオプションが表示されます。

   ![バックエンドの変更](../../assets/cdn/fastly-backend.png)

1. を選択します。 **盾** 場所（またはデータセンター）。

   プロジェクトのデフォルトの Fastly 設定により、Cloud Service 地域に最も近い場所が設定されます。 変更する必要がある場合は、既定の場所に近い場所を選択します。

1. シールドへの接続のタイムアウト値（マイクロ秒）、バイト間の時間、最初のバイトの時間を変更します。 デフォルトのタイムアウト設定はそのままにすることをお勧めします。

1. オプションで、選択して **編集または保存後にバックエンドとシールドを有効にする**.

1. クリック **アップロード** 変更を保存し、Fastly サーバーにアップロードします。

1. 管理者で、 **設定を保存**.

詳しくは、 [バックエンド設定ガイド](https://github.com/fastly/fastly-magento2/blob/21b61c8189971275589219d418332798efc7db41/Documentation/Guides/BACKEND-SETTINGS.md) Fastly モジュールのドキュメントを参照してください。

## 基本認証

基本認証は、ユーザー名とパスワードを使用して、サイト上のすべてのページとアセットを保護する機能です。 水 **推奨しない** 実稼動環境での基本認証のアクティブ化 開発プロセス中にサイトを保護するために、ステージングで設定できます。 詳しくは、 [基本認証ガイド](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/BASIC-AUTH.md) （ Fastly CDN モジュールのドキュメント）を参照してください。

ユーザーアクセスを追加し、ステージングで基本認証を有効にした場合でも、追加の資格情報を必要とせずに管理者にアクセスできます。

## カスタム VCL スニペットの作成

Fastly は、Fastly サービス設定をカスタマイズするために、Vanish Configuration Language(VCL) のカスタマイズバージョンをサポートしています。 例えば、エッジおよびアクセス制御リスト (ACL) 辞書を持つ VCL コードブロックを使用して、特定のユーザーまたは IP アドレスに対するアクセスを許可、ブロック、またはリダイレクトできます。

カスタム VCL スニペット、エッジ辞書、ACL の作成手順については、 [カスタム Fastly VCL スニペット](fastly-vcl-custom-snippets.md).

>[!NOTE]
>
>カスタム VCL コード、エッジ辞書、ACL を Fastly モジュール設定に追加する前に、Fastly キャッシュサービスがデフォルト設定で動作することを確認します。 詳しくは、 [Fastly の設定](fastly-configuration.md).

## ドメインの管理

Starter と Pro の両方のプロジェクトで、 [!UICONTROL Domains] オプションを使用して、ストアの Fastly ドメイン設定を追加および管理できます。

- スタータープロジェクトの場合は、 [!UICONTROL Domains] 」タブをクリックします。 [!DNL Cloud Console] をクリックして、プロジェクト URL を追加します。

- Pro プロジェクトの場合は、 [Adobe Commerceサポートチケット](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) をクリックして、クラウドプロジェクト設定にドメインを追加します。 また、サポートチームは、Adobe Commerce Fastly アカウント設定を更新してドメインを追加します。

**管理者から Fastly ドメイン設定を管理するには**:

{{admin-login-step}}

1. 選択 **ストア** /設定/ **設定** > **詳細** > **システム** を展開します。 **フルページキャッシュ**.

1. 管理者で _Fastly 設定_ セクション、選択 **ドメイン**.

1. クリック **ドメインの管理** をクリックして、ドメインページを開きます。

1. クラウド環境のストアの最上位ドメイン名とサブドメイン名を追加します。

   指定できるドメインは、クラウドインフラストラクチャ設定に既に追加されているドメインのみです。

   ![スターター用の Fastly ドメイン設定を追加](../../assets/cdn/fastly-starter-activate-domain.png)

1. クリック **有効化** をクリックして、Fastly ドメイン設定を更新します。

>[!NOTE]
>
>同じドメインが別の Fastly アカウントで設定されている場合は、Adobe Commerceにドメインを追加する前に、Adobe Commerceサポートチケットを送信してドメインのデリゲーションをリクエストする必要があります。 詳しくは、 [複数の Fastly アカウントと割り当てられたドメイン](fastly.md#multiple-fastly-accounts-and-assigned-domains).

## メンテナンスモードを有効にする

以下を使用します。 _メンテナンスモード_ オプションを使用して、指定した IP アドレスからサイトへの管理者アクセスを許可し、その他すべてのリクエストのエラーページを返すことができます。

**管理アクセスでメンテナンスモードを有効にするには**:

1. を開きます。 _Fastly 設定_ 」セクションをクリックします。

1. Adobe Analytics の _Edge ACL_ セクション、 `maint_allow` 管理 IP アドレスを持つアクセス制御リスト (ACL)。メンテナンスモードの間にストアにアクセスできます。

   ![IP メンテナンスモードの更新許可リスト](../../assets/cdn/fastly-maint-allowlist.png)

1. Adobe Analytics の _メンテナンスモード_ セクション、選択 **メンテナンスモードを有効にする**.

   メンテナンスモードを有効にすると、 `maint_allowlist` ACL。 次の項目を更新して、 `maint_allowlist` をクリックして、ACL 内の IP アドレスを変更します。

   設定手順について詳しくは、 [メンテナンスモードガイド](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/MAINTENANCE-MODE.md) (Fastly CDN のMagento2 モジュールドキュメント )。
