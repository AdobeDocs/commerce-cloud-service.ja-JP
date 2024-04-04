---
title: Fastly サービスの概要
description: クラウドインフラストラクチャ上のAdobe Commerceに含まれる Fastly サービスが、Adobe Commerceサイトのコンテンツ配信操作の最適化とセキュリティ保護に役立つ仕組みについて説明します。
feature: Cloud, Configuration, Iaas, Paas, Cache, Security, Services
exl-id: dc4500bf-f037-47f0-b7ec-5cd1291f73a1
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '1392'
ht-degree: 0%

---

# Fastly サービスの概要

>[!WARNING]
>
>クラウドプラットフォームにデプロイされたAdobe Commerceサイトの PCI コンプライアンスを維持するには、スターターメインブランチ、Pro Production および Pro Staging 環境に Fastly を設定します。 ヘッドレスデプロイメントでAdobe Commerceを使用する場合は、 Fastly を使用してGraphQLの応答をキャッシュすることを強くお勧めします。 詳しくは、 [Fastly を使用したキャッシュ](https://developer.adobe.com/commerce/webapi/graphql/usage/caching/#caching-with-fastly) （内） *GraphQL Developer Guide*.

Fastly は、クラウドインフラストラクチャプロジェクト上のAdobe Commerce向けに、コンテンツ配信操作を最適化およびセキュリティで保護するための以下のサービスを提供します。 これらのサービスは、クラウドインフラストラクチャ上のAdobe Commerceに追加費用なしで組み込まれます。

- **コンテンツ配信ネットワーク (CDN)** — 設定したバックエンドデータセンターでサイトページ、アセット、CSS などをキャッシュするワニスベースのサービス。 お客様がサイトやストアにアクセスすると、キャッシュされたページの読み込みを高速化するために、要求が Fastly にヒットします。 CDN サービスは次の機能を提供します。

- **キャッシュ管理** — 帯域幅の負荷とコストを削減するために設定したバックエンドデータセンターで、サイトページ、アセット、CSS などをキャッシュします。

   - 用途 [完全なカスタム VCL スニペット](fastly-vcl-custom-snippets.md) （Vanrish 2.1 準拠）：キャッシュが要求に対してどのように応答するかを変更します。

   - 設定 [GeoIP サービスのサポート](fastly-custom-cache-configuration.md#configure-geoip-handling)

   - [TLS に暗号化されていない要求を強制的に送信](fastly-custom-cache-configuration.md#force-tls)

   - [Fastly タイムアウトをカスタマイズ](fastly-custom-cache-configuration.md#extend-fastly-timeout) 一括操作リクエストで 503 応答を防ぐための設定

   - 作成 [カスタムエラー応答ページ](fastly-custom-response.md)

- **セキュリティ**—Adobe Commerceサイトの Fastly サービスを有効にすると、サイトやネットワークを保護するための追加のセキュリティ機能が利用できます。

   - [Web アプリケーションファイアウォール](fastly-waf-service.md) (WAF) — 悪意のあるトラフィックをブロックする PCI 準拠の保護を提供し、クラウドインフラストラクチャサイトおよびネットワーク上の実稼動Adobe Commerceに損害を与える可能性がある、管理 Web アプリケーションファイアウォールサービス。 WAF サービスは、Pro および Starter 実稼動環境でのみ使用できます。

   - [分散型サービス拒否 (DDoS) 保護](#ddos-protection)- Ping of Death、Smurf 攻撃、その他の ICMP ベースのフラッド攻撃などの一般的な攻撃に対する組み込みの DDoS 保護。

   - [SSL/TLS 証明書](fastly-configuration.md#provision-ssltls-certificates)—Fastly サービスでは、HTTPS 経由のセキュリティで保護されたトラフィックを提供するために、SSL/TLS 証明書が必要です。

     Adobe Commerceは、ドメイン検証済みの証明書を提供します。ステージング環境および実稼動環境ごとに、SSL/TLS 証明書を暗号化します。 Adobe Commerceは、Fastly の設定プロセス中に、ドメインの検証と証明書のプロビジョニングを完了します。

- **原点のクローキング** — トラフィックが Fastly WAF をバイパスするのを防ぎ、オリジンサーバーの IP アドレスを非表示にして、直接アクセスや DDoS 攻撃から保護します。

  Origin のクローキングは、クラウドインフラストラクチャの Pro 実稼動プロジェクトのAdobe Commerceで、デフォルトで有効になっています。 クラウドインフラストラクチャのスターター実稼働プロジェクトでAdobe Commerceのオリジンクローキングを有効にするには、 [Adobe Commerceサポートチケット](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket). キャッシュが不要なトラフィックがある場合は、Fastly サービス設定をカスタマイズして、 [Fastly キャッシュをバイパスする](fastly-vcl-bypass-to-origin.md).

- **[画像の最適化](fastly-image-optimization.md)** — 画像処理とサイズ変更の負荷を Fastly サービスにオフロードして、サーバが注文とコンバージョンをより効率的に処理できるようにします。

- **[Fastly CDN と WAF のログ](../monitor/new-relic-service.md#new-relic-log-management)**— Adobe Commerce on cloud infrastructure Pro プロジェクトの場合、New Relic Logs サービスを使用して、Fastly CDN および WAF ログデータを確認および分析できます。

## Magento2 の Fastly CDN モジュール

クラウドインフラストラクチャ上のAdobe Commerce向けの Fastly サービスでは、 [Magento2 の Fastly CDN モジュール] 次の環境にインストール： Pro Staging および Production、Starter Production (`master` 分岐 )。

Adobe Commerceプロジェクトの初期プロビジョニングまたはアップグレード時に、Adobeは、ステージング環境および実稼動環境に Fastly CDN モジュールの最新バージョンをインストールします。 Fastly がモジュールの更新をリリースすると、環境に関する通知を管理画面で受け取ります。 Adobeでは、最新リリースを使用するように環境を更新することをお勧めします。 詳しくは、 [Fastly のアップグレード](fastly-configuration.md#upgrade-the-fastly-module).

## Fastly サービスアカウントと資格情報

Adobeインフラストラクチャプロジェクトに関するコマースには、専用の Fastly アカウントまたはアカウント所有者は必要ありません。 代わりに、各ステージング環境と実稼動環境には、管理者から Fastly サービスを設定および管理するための一意の Fastly 資格情報（API トークンとサービス ID）が割り当てられています。 また、Fastly API リクエストを送信するには、資格情報も必要です。

プロジェクトのプロビジョニング中に、Adobeはクラウドインフラストラクチャ上のAdobe Commerceの Fastly サービスアカウントにプロジェクトを追加し、ステージング環境および実稼動環境の設定に Fastly 資格情報を追加します。 詳しくは、 [Fastly 認証情報の取得](fastly-configuration.md#get-fastly-credentials).

### Fastly API トークンの変更

Adobe Commerceサポートチケットを送信して、Fastly API トークンの資格情報を変更します。 新しいトークンを受け取ったら、新しいトークンを使用するようにステージング環境または実稼動環境を更新します。

**Fastly API トークンの資格情報を変更するには、以下を実行します。**:

1. [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) 新しい Fastly API 資格情報をリクエストしています。

   クラウドインフラストラクチャ上のAdobe Commerceプロジェクト ID と、新しい資格情報が必要な環境を含めます。

1. 新しい API トークンを受け取ったら、 [Fastly 資格情報の設定](fastly-configuration.md#test-the-fastly-credentials) 管理者または [[!DNL Cloud Console] 環境変数](../project/overview.md#configure-environment).

1. [新しい秘密鍵証明書をテストします。](fastly-configuration.md#test-the-fastly-credentials).

1. 秘密鍵証明書を更新したら、 Adobe Commerceサポートチケットを送信して、古い API トークンを削除します。

### 複数の Fastly アカウントと割り当てられたドメイン

Fastly では、apex ドメインと関連するサブドメインを 1 つの Fastly サービスおよびアカウントに割り当てることができます。 Adobe Commerceサイトで使用したのと同じ apex およびサブドメインをリンクする既存の Fastly アカウントがある場合、次のオプションがあります。

- クラウドインフラストラクチャプロジェクト環境でAdobe Commerceの Fastly サービス資格情報をリクエストする前に、既存のアカウントから apex とサブドメインを削除します。 詳しくは、 [ドメインの操作] Fastly のドキュメントで。

  このオプションを使用して、apex ドメインとすべてのサブドメインを、クラウドインフラストラクチャ上のAdobe Commerceの Fastly サービスアカウントにリンクします。

- apex とサブドメインを異なるアカウントにリンクできるように、Adobe Commerceサポートチケットを送信してドメインのデリゲーションをリクエストします。

  Adobe CommerceサイトとAdobe Commerce以外のサイトに複数のサブドメインを持つ apex ドメインがあり、これらのサブドメインを異なる Fastly アカウントにリンクする場合は、このオプションを使用します。

#### ドメインのデリゲーションをリクエスト

*シナリオ 1:*

apex ドメイン (`testweb.com` および `www.testweb.com`) は、既存の Fastly アカウントにリンクされています。 次のサブドメインを使用して設定されたAdobe Commerce on cloud infrastructure プロジェクトがあります。 `mcstaging.testweb.com` および `mcprod.testweb.com`. apex ドメインをクラウドインフラストラクチャ上のAdobe Commerceの Fastly サービスアカウントに移動しない。

を送信 [Fastly サポートチケット] サブドメインを既存の Fastly アカウントからクラウドインフラストラクチャ上のAdobe Commerceの Fastly アカウントにデリゲートするように要求します。 チケットにAdobe Commerceプロジェクト ID を含めます。

デリゲーションが完了したら、プロジェクトのサブドメインを、クラウドインフラストラクチャ上のAdobe Commerceの Fastly サービスアカウントに追加できます。 詳しくは、 [Fastly 認証情報の取得](fastly-configuration.md#get-fastly-credentials).

*シナリオ 2:*

apex ドメイン (`testweb.com` および `www.testweb.com`) は、Adobe Commerce on cloud infrastructure Fastly サービスアカウントにリンクされています。 の Fastly サービスを管理する `service.testweb.com` および `product-updates.testweb.com` 別の Fastly アカウントのサブドメイン。

Adobe Commerceサポートチケットを送信し、サブドメインをクラウドインフラストラクチャ Fastly サービスアカウント上のAdobe Commerceから Fastly アカウントにデリゲートするようにリクエストします。 チケットに Fastly アカウントのサービス ID を含めます。

## DDoS 保護

DDOS 保護は、Fastly CDN サービスに組み込まれています。 Adobe Commerceサイトの Fastly サービスを有効にしたら、Fastly はすべてのウェブおよび管理トラフィックをフィルタリングして、潜在的な攻撃を検出し、ブロックします。

- 攻撃のターゲティングレイヤ 3 または 4 の場合、Fastly サービスは、ポートとプロトコルに基づいてトラフィックを除外し、HTTP または HTTPS リクエストのみを調べます。 ICMP、UDP、およびその他のネットワーク開始攻撃は、私たちのネットワークエッジで廃棄されます。 これには、SSDP や NTP などの UDP サービスを使用する反射および増幅攻撃が含まれます。 このレベルの保護を提供することで、Ping of Death、Smurf 攻撃、その他の ICMP ベースの洪水など、複数の一般的な攻撃を効果的に防止できます。

  キャッシュ層で TCP レベルの攻撃を確実に管理します。 この戦略は、SYN フラッド攻撃と、Fastly システム内の TCP スタック、リソース攻撃、TLS 攻撃を含む多くのバリアントに対応するために必要な規模とコンテキストをクライアントごとに提供します。

- Fastly は、レイヤ 7 攻撃に対する保護も提供します。 ストアでパフォーマンスの問題が発生しており、レイヤ 7 DOS 攻撃が疑われる場合は、Adobe Commerceサポートチケットを送信してください。 Adobeは、Fastly サービスにカスタムルールを作成して適用し、ヘッダー、ペイロード、または攻撃トラフィックを識別する属性の組み合わせに基づいて、悪意のあるリクエストを調べて除外できます。 詳しくは、 [DDoS 攻撃の確認] および [悪意のあるトラフィックをブロックする方法] （内） *Adobe Commerce Help Center*.

<!--Link definitions-->

[Caching with Fastly]: https://developer.adobe.com/commerce/webapi/graphql/usage/caching/#caching-with-fastly

[DDoS 攻撃の確認]: https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/checking-for-ddos-attack-from-cli.html

[Magento2 の Fastly CDN モジュール]: https://github.com/fastly/fastly-magento2

[Fastly サポートチケット]: https://docs.fastly.com/products/support-description-and-sla#support-requests

[悪意のあるトラフィックをブロックする方法]: https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/how-to/block-malicious-traffic-for-magento-commerce-on-fastly-level.html

[ドメインの操作]: https://docs.fastly.com/en/guides/working-with-domains
