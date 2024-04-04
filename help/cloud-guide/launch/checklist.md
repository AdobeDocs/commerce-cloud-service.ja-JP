---
title: 起動チェックリスト
description: サイト開始のチェックリスト項目を確認します。
exl-id: 4525742e-18c5-40d1-975d-00ba3f3a51a0
source-git-commit: 5b0a691a4355f5eda31d42cd3da9925439dfb510
workflow-type: tm+mt
source-wordcount: '1104'
ht-degree: 0%

---

# 起動チェックリスト

実稼動環境にデプロイする前に、 [起動チェックリスト](../../assets/adobe-commerce-cloud-prelaunch-checklist.pdf)をクリックし、これらの手順に従って、必要な設定とテストがすべて完了していることを確認します。 Starter と Pro の完全なデプロイメントプロセスの概要については、 [ストアをデプロイ](../deploy/staging-production.md).

## 実稼動環境での完全なテスト

詳しくは、 [デプロイメントをテスト](../test/staging-and-production.md) サイト、ストア、環境のすべての側面をテストする場合。 これらのテストには、Fastly、User Acceptorment Tests(UAT)、およびパフォーマンステストの検証が含まれます。

## TLS と Fastly

Adobeは、各環境に対して「 Let&#39;s Encrypt SSL/TLS 証明書」を提供します。 この証明書は、HTTPS 経由でセキュリティで保護されたトラフィックを提供するために Fastly で必要です。

この証明書を使用するには、Adobeがドメインの検証を完了し、証明書を環境に適用できるように、DNS 構成を更新する必要があります。 各環境には、その環境にデプロイされたクラウドインフラストラクチャサイト上のAdobe Commerceのドメインをカバーする一意の証明書があります。 設定の更新を完了し、 [プロセスの設定](../cdn/fastly-configuration.md).

## 実稼動設定で DNS 構成を更新する

サイトを起動する準備が整ったら、DNS 設定を更新して、Fastly サービスを通じて実稼動環境からトラフィックをルーティングする必要があります。

**前提条件：**

- [開発環境での Fastly のセットアップとテスト](../cdn/fastly-configuration.md#)

- 実稼動環境の設定が、必要なすべてのドメインで更新されました

  通常は、カスタマーテクニカルアドバイザーと協力して、店舗に必要なすべてのトップレベルドメインとサブドメインを追加します。 実稼動環境のドメインを追加または変更するには、次の手順に従います。 [Adobe Commerceサポートチケットを送信する](https://support.magento.com/hc/en-us/articles/360019088251). プロジェクトの設定が更新されたことを確認します。

  スタータープロジェクトでは、ドメインをプロジェクトに追加する必要があります。 詳しくは、 [ドメインの管理](../cdn/fastly-custom-cache-configuration.md#manage-domains).

- 実稼動環境用にプロビジョニングされた SSL/TLS 証明書。

  Fastly の設定プロセス中に実稼動ドメインの ACME チャレンジレコードを追加した場合、DNS 設定を更新して Fastly サービスにトラフィックをルーティングすると、Adobeは SSL/TLS 証明書を実稼動環境に自動的にアップロードします。 証明書を事前にプロビジョニングしていない場合、またはドメインを更新した場合、Adobeはドメイン検証を完了し、証明書をプロビジョニングする必要があります。これには最大 12 時間かかる場合があります。

### サイト起動の DNS 構成を更新するには：

1. 本番サイトの次の DNS 設定を更新します。

   - 既存のサイトから移行する場合は特に、必要なリダイレクトをすべて設定する

   - ゾーンのルートリソースレコードをホスト名に対応するように設定します。

   - 有効期間 (TTL) の値を小さくして、DNS 情報を更新し、お客様が正しい実稼動ストアを指すようにします。

     DNS レコードを切り替える際には、TTL 値を大幅に低くすることをお勧めします。 この値は、DNS レコードをキャッシュする時間を DNS に示します。 短縮されると、DNS の更新が速くなります。 例えば、TTL 値をサイトの更新時に 3 日から 10 分に変更できます。 TTL 値を短くすると、DNS インフラストラクチャに負荷がかかることに注意してください。 サイトを起動した後に、以前より高い値に戻します。


1. CNAME レコードを追加して、実稼動環境のサブドメインを Fastly サービスに指定します。 `prod.magentocloud.map.fastly.net`例：

   | ドメインまたはサブドメイン | CNAME |
   | ----------------------- | -------------------------------- |
   | `www.<domain-name>.com` | prod.magentocloud.map.fastly.net |
   | `mystore.<domain-name>.com` | prod.magentocloud.map.fastly.net |

1. 必要に応じて、apex ドメイン (`<domain-name>.com`) を次の Fastly IP アドレスに送信する必要があります。

   | Apex ドメイン | 名前 |
   | --------------- | ----------------- |
   | `<domain-name>.com` | `151.101.1.124` |
   | `<domain-name>.com` | `151.101.65.124` |
   | `<domain-name>.com` | `151.101.129.124` |
   | `<domain-name>.com` | `151.101.193.124` |

>[!IMPORTANT]
>
>DNS の手順 ( [RFC1034](https://www.rfc-editor.org/rfc/rfc1912) (**セクション 2.4**) には次のような内容を記述します。
>_CNAME レコードは、他のデータと共存できません。 つまり、suzy.podunk.xx が sue.podunk.xx のエイリアスの場合、 suzy.podunk.eduの MX レコードや A レコード、TXT レコードを持つことはできません。_
>
>この理由から、DNS レコードはタイプである必要があります `CNAME` （サブドメインの場合）および（タイプの場合） `A` apex ドメイン（ルートドメイン）の場合。 このルールを破棄すると、MX や NS などの他のレコードを追加できなくなるので、メールサービスまたは DNS の伝播が中断される可能性があります。 一部の DNS プロバイダーは、内部カスタマイズを使用してこれを回避する場合がありますが、標準に従うことで、安定性と柔軟性（DNS プロバイダーの変更など）が確保されます。

1. ベース URL を更新します。

   - SSH を使用して実稼動環境にログインします。

     ```bash
     magento-cloud ssh -e production
     ```

   - CLI を使用して、ストアのベース URL を変更します。

     ```bash
     php bin/magento setup:store-config:set --base-url="https://www.<domain-name>.com/"
     ```

   **注意**：管理者からベース URL を更新することもできます。 詳しくは、 [ストアの URL](https://experienceleague.adobe.com/docs/commerce-admin/stores-sales/site-store/store-urls.html) （内） _Adobe Commerce Stores and Purchase Experience ガイド_.

1. サイトが更新されるまで数分待ちます。

1. サイトをテストします。

## 実稼動設定を検証

最終パスを作成して、1 つ以上のストアの実稼動設定を検証します。 実稼動環境で設定を更新できます。 設定が読み取り専用の場合は、SSH 接続を開き、CLI コマンドを使用して設定を変更するか、ローカル環境で設定を変更する必要が生じる場合があります。 更新が完了したら、変更をステージング環境と実稼動環境にデプロイできます。

推奨される変更とチェックは次のとおりです。

- [送信メールのテストが完了しました](../project/outgoing-emails.md)

- [管理者資格情報とベース管理者 URL のセキュア設定](https://docs.magento.com/user-guide/stores/security-admin.html)

- [Web 用にすべての画像を最適化](../cdn/fastly-image-optimization.md)

- [HTML、JavaScript、CSS の縮小設定を確認する](../deploy/static-content.md)

## Fastly キャッシュの検証

- 実稼動サイトで Fastly キャッシュが正しく動作していることをテストおよび確認します。 詳細なテストとチェックについては、 [Fastly テスト](../test/staging-and-production.md#check-fastly-caching).

- [Fastly CDN Module for Commerce の最新バージョンが実稼動環境にインストールされていることを確認します。](../cdn/fastly-configuration.md#upgrade-the-fastly-module)

- [Fastly VCL コードの最新バージョンがアップロードされていることを確認します。](../cdn/fastly-configuration.md#upload-vcl-to-fastly)

## パフォーマンステスト

次の項目を確認することをお勧めします。 [Performance Toolkit](https://github.com/magento/magento2/tree/2.4/setup/performance-toolkit) オプションを使用できます。

また、次のサードパーティオプションを使用してテストすることもできます。

- [攻城](https://www.joedog.org/siege-home/)：ストアを制限にプッシュするためのトラフィックシェーピングとテストソフトウェア。 設定可能な数のシミュレーションされたクライアントを使用してサイトにヒットします。 Siege は、基本的な認証、cookie、HTTP、HTTPS、FTP プロトコルをサポートします。

- [Jmeter](https://jmeter.apache.org/):Flash セールスなど、スパイク付きトラフィックのパフォーマンスを測定するのに役立つ優れた読み込みテストです。 サイトに対して実行するカスタムテストを作成します。

- [New Relic](https://support.newrelic.com/s/) （提供）：データの送信、クエリ、Redis など、アクションごとの追跡された時間により、パフォーマンスが低下する原因となるサイトのプロセスや領域を特定するのに役立ちます。

- [WebPageTest](https://www.webpagetest.org/) および [Pingdom](https://www.pingdom.com/)：サイトページのリアルタイム分析で、様々な接触チャネルでの読み込み時間を把握できます。 Pingdom は料金がかかる場合があります。 WebPageTest は無料のツールです。

## セキュリティ設定

- [セキュリティスキャンの設定](overview.md#set-up-the-security-scan-tool)

- [管理者ユーザー向けのセキュア設定](https://docs.magento.com/user-guide/stores/security-admin.html)

- [管理 URL のセキュア設定](https://docs.magento.com/user-guide/stores/store-urls-custom-admin.html)

- [Adobe Commerce on cloud infrastructure プロジェクトに存在しなくなったユーザーをすべて削除する](../project/user-access.md)

- [二段階認証の設定](https://devdocs.magento.com/guides/v2.4/security/two-factor-authentication.html)

## パフォーマンスの監視

Pro および Starter 環境でのパフォーマンス監視にNew Relicサービスを使用できます。 Pro プランのアカウントでは、New Relic APM および Infrastructure エージェントを使用してアプリケーションとインフラストラクチャのパフォーマンスを監視するAdobe Commerceのアラートポリシーに Managed Alerts を提供しています。 これらのサービスの使用について詳しくは、 [管理されたアラートでパフォーマンスを監視する](../monitor/investigate-performance.md#monitor-performance-with-managed-alerts).

### 次の手順

[起動手順](steps.md)
