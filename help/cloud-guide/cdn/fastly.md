---
title: Fastly サービスの概要
description: クラウドインフラストラクチャー上のAdobe Commerceに含まれる Fastly サービスが、Adobe Commerce サイトのコンテンツ配信操作を最適化し、安全を確保する上でどのように役立つかを説明します。
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
>クラウドプラットフォームにデプロイされたAdobe Commerce サイトの PCI コンプライアンスを維持するには、スターターメインブランチ、実稼動、ステージング環境に Fastly を設定します。 ヘッドレスデプロイメントでAdobe Commerceを使用する場合は、Fastly を使用してGraphQLの応答をキャッシュすることを強くお勧めします。 参照： [Fastly を使用したキャッシュ](https://developer.adobe.com/commerce/webapi/graphql/usage/caching/#caching-with-fastly) が含まれる *GraphQL開発者ガイド*.

Fastly は、クラウドインフラストラクチャプロジェクト上のAdobe Commerceのコンテンツ配信操作を最適化および保護するために、次のサービスを提供しています。 これらのサービスは、クラウドインフラストラクチャー上のAdobe Commerceに追加費用なしで含まれています。

- **コンテンツ配信ネットワーク（CDN）** – 設定したバックエンドデータセンターにサイトページ、アセット、CSS などをキャッシュする、ワニスをベースにしたサービス。 顧客がサイトにアクセスして保存すると、リクエストは Fastly にヒットし、キャッシュされたページをより迅速に読み込みます。 CDN サービスは次の機能を提供します。

- **キャッシュ管理** – 帯域幅の負荷とコストを削減するために設定したバックエンド・データ・センターに、サイト・ページ、アセット、CSS などをキャッシュします

   - 使用方法 [Fastly カスタム VCL スニペット](fastly-vcl-custom-snippets.md) （Varnish 2.1 準拠） キャッシュがリクエストに対してどのように応答するかを変更します。

   - の設定 [GeoIP サービスのサポート](fastly-custom-cache-configuration.md#configure-geoip-handling)

   - [暗号化されていない要求を TLS に強制的に送信する](fastly-custom-cache-configuration.md#force-tls)

   - [Fastly タイムアウトのカスタマイズ](fastly-custom-cache-configuration.md#extend-fastly-timeout) 一括操作リクエストで 503 応答できないようにする設定

   - 作成 [カスタムエラー応答ページ](fastly-custom-response.md)

- **セキュリティ**—Adobe Commerce サイトに対して Fastly サービスを有効にすると、サイトとネットワークを保護するためのその他のセキュリティ機能を使用できるようになります。

   - [Web アプリケーションファイアウォール](fastly-waf-service.md) （WAF） – PCI 準拠の保護機能を提供する Managed Web Application Firewall サービスで、クラウドインフラストラクチャサイトおよびネットワーク上の実稼動Adobe Commerceに損害を与える前に、悪意のあるトラフィックをブロックします。 WAF サービスは、Pro および Starter 実稼動環境でのみ使用できます。

   - [分散型サービス拒否（DDoS）保護](#ddos-protection)—Ping of Death、Smurf 攻撃、その他の ICMP ベースのフラッド攻撃などの一般的な攻撃に対する組み込みの DDoS 保護。

   - [SSL/TLS 証明書](fastly-configuration.md#provision-ssltls-certificates)- Fastly サービスは、HTTPS 経由で安全なトラフィックを提供するために SSL/TLS 証明書を必要とします。

     Adobe Commerceは、ステージング環境と実稼動環境ごとに、ドメインで検証された Let&#39;s Encrypt SSL/TLS 証明書を提供します。 Adobe Commerceは、Fastly のセットアッププロセス中に、ドメインの検証と証明書のプロビジョニングを完了します。

- **原点クローキング**- トラフィックが Fastly WAF をバイパスするのを防ぎ、オリジンサーバーの IP アドレスを非表示にして、直接アクセスや DDoS 攻撃から保護します。

  Cloud infrastructure Pro 実稼働プロジェクトのAdobe Commerceでは、オリジンクロークがデフォルトで有効になっています。 クラウドインフラストラクチャー上のAdobe Commerceでオリジンクロークを有効にするには、スターター実稼動プロジェクトで、 [Adobe Commerce サポートチケット](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket). キャッシュを必要としないトラフィックがある場合、Fastly サービス設定をカスタマイズして、リクエストが次を行うことを許可できます [fastly キャッシュをバイパス](fastly-vcl-bypass-to-origin.md).

- **[画像の最適化](fastly-image-optimization.md)**- サーバーが注文やコンバージョンをより効率的に処理できるように、画像処理やサイズ変更の負荷を Fastly サービスにオフロードします。

- **[Fastly CDN と WAF のログ](../monitor/new-relic-service.md#new-relic-log-management)**—Cloud Infrastructure Pro プロジェクトのAdobe Commerceの場合、New Relic ログサービスを使用して、Fastly CDN および WAF ログデータを確認し、分析できます。

## Magento 2 用 Fastly CDN モジュール

クラウドインフラストラクチャー上のAdobe Commerce用 Fastly サービスでは、 [Magento 2 用 Fastly CDN モジュール] 以下の環境にインストールします。Pro ステージング環境と実稼動環境、Starter 実稼動環境（`master` ブランチ）に含まれます。

Adobe Commerce プロジェクトの初期プロビジョニングまたはアップグレード時に、Adobeは、最新バージョンの Fastly CDN モジュールをステージング環境および実稼動環境にインストールします。 Fastly がモジュールのアップデートをリリースすると、お使いの環境の管理者で通知が届きます。 Adobeでは、最新のリリースを使用するように環境を更新することをお勧めします。 参照： [Fastly へのアップグレード](fastly-configuration.md#upgrade-the-fastly-module).

## Fastly サービスアカウントと資格情報

クラウドインフラストラクチャプロジェクトのAdobeCommerces では、専用の Fastly アカウントまたはアカウントオーナーは必要ありません。 代わりに、各ステージング環境と実稼動環境には、管理者から Fastly サービスを設定および管理するための一意の Fastly 資格情報（API トークンとサービス ID）があります。 また、Fastly API リクエストを送信するには、資格情報も必要です。

プロジェクトのプロビジョニング時に、Adobeはプロジェクトをクラウドインフラストラクチャ上のAdobe Commerce用の Fastly サービスアカウントに追加し、Fastly 資格情報をステージング環境と実稼動環境の設定に追加します。 参照： [Fastly 資格情報の取得](fastly-configuration.md#get-fastly-credentials).

### Fastly API トークンの変更

Adobe Commerce サポートチケットを送信して、Fastly API トークン資格情報を変更します。 新しいトークンを受け取ったら、新しいトークンを使用するようにステージング環境または実稼動環境を更新します。

**Fastly API トークン資格情報を変更するには**:

1. [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) 新しい Fastly API 資格情報のリクエスト。

   クラウドインフラストラクチャプロジェクト ID 上のAdobe Commerceと、新しい資格情報を必要とする環境を含めます。

1. 新しい API トークンを受け取ったら、で API トークンの値を更新します。 [Fastly 資格情報設定](fastly-configuration.md#test-the-fastly-credentials) 管理画面または [[!DNL Cloud Console] 環境変数](../project/overview.md#configure-environment).

1. [新しい資格情報のテスト](fastly-configuration.md#test-the-fastly-credentials).

1. 資格情報を更新したら、古い API トークンを削除するためのAdobe Commerce サポートチケットを送信します。

### 複数の Fastly アカウントと割り当てられたドメイン

Fastly では、apex ドメインと関連するサブドメインを 1 つの Fastly サービスおよびアカウントに割り当てることができます。 Adobe Commerce サイトで使用されるのと同じ apex およびサブドメインをリンクする既存の Fastly アカウントがある場合は、次のオプションがあります。

- クラウドインフラストラクチャプロジェクト環境でAdobe Commerceの Fastly サービス資格情報をリクエストする前に、apex とサブドメインを既存のアカウントから削除します。 参照： [ドメインの操作] を Fastly のドキュメントで確認できます。

  apex ドメインとすべてのサブドメインを、クラウドインフラストラクチャー上のAdobe Commerceの Fastly サービスアカウントにリンクするには、このオプションを使用します。

- Adobe Commerce サポートチケットを送信して、apex とサブドメインを異なるアカウントにリンクできるように、ドメインのデリゲーションをリクエストします。

  Adobe Commerce サイトとAdobe Commerce以外のサイトに対して複数のサブドメインを持つ apex ドメインがあり、これらのサブドメインを異なる Fastly アカウントにリンクする場合は、このオプションを使用します。

#### ドメインのデリゲーションをリクエスト

*シナリオ 1:*

apex ドメイン （`testweb.com` および `www.testweb.com`）は既存の Fastly アカウントにリンクされています。 Adobe Commerce on cloud infrastructure プロジェクトが次のサブドメインで設定されている。 `mcstaging.testweb.com` および `mcprod.testweb.com`. Apex ドメインを、クラウドインフラストラクチャ上のAdobe Commerceの Fastly サービスアカウントに移動することは望ましくありません。

を送信 [Fastly サポートチケット] サブドメインを既存の Fastly アカウントからクラウドインフラストラクチャ上のAdobe Commerceの Fastly アカウントにデリゲートするようにリクエストします。 チケットにAdobe Commerce プロジェクト ID を含めます。

デリゲーションが完了したら、クラウドインフラストラクチャー上のAdobe Commerce用 Fastly サービスアカウントにプロジェクトのサブドメインを追加できます。 参照： [Fastly 資格情報の取得](fastly-configuration.md#get-fastly-credentials).

*シナリオ 2:*

apex ドメイン （`testweb.com` および `www.testweb.com`）はAdobe Commerce on cloud infrastructure Fastly サービスアカウントにリンクされています。 用の Fastly サービスを管理する場合 `service.testweb.com` および `product-updates.testweb.com` 別の Fastly アカウントのサブドメイン。

サブドメインをAdobe Commerce on cloud infrastructure Fastly サービスアカウントから Fastly アカウントにデリゲートするように求めるAdobe Commerce サポートチケットを送信します。 Fastly アカウントのサービス ID をチケットに含めます。

## DDoS 保護

DDOS 保護は、Fastly CDN サービスに組み込まれています。 Adobe Commerce サイトに対して Fastly サービスを有効にすると、Fastly はすべての web および管理者トラフィックをフィルタリングし、潜在的な攻撃を検出してブロックします。

- レイヤー 3 または 4 をターゲットとする攻撃の場合、Fastly サービスは、ポートとプロトコルに基づいてトラフィックをフィルターで除外し、HTTP または HTTPS リクエストのみを調べます。 ICMP、UDP、およびその他のネットワークによる攻撃は、ネットワーク エッジで廃棄されます。 これには、SSDP や NTP などの UDP サービスを使用する反射および増幅攻撃が含まれます。 このレベルの保護を提供することにより、Ping of Death、Smurf 攻撃、その他の ICMP ベースのフラッドなど、複数の一般的な攻撃を効果的にブロックします。

  Fastly は、キャッシュレイヤーで TCP レベルの攻撃を管理します。 この戦略は、SYN フラッド攻撃とその多くのバリアント（TCP スタック、リソース攻撃、Fastly システム内の TLS 攻撃など）に対処するために、クライアントごとに必要な規模とコンテキストを提供します。

- また、Fastly はレイヤー 7 攻撃に対する保護も提供します。 ストアでパフォーマンスの問題が発生しており、レイヤー 7 DDoS 攻撃が疑われる場合は、Adobe Commerce サポートチケットを送信します。 Adobeでは、カスタムルールを作成して Fastly サービスに適用し、攻撃トラフィックを特定するヘッダー、ペイロード、または属性の組み合わせに基づいて、悪意のあるリクエストを検査して除外できます。 参照： [DDoS 攻撃の確認] および [悪意のあるトラフィックをブロックする方法] が含まれる *Adobe Commerceヘルプセンター*.

<!--Link definitions-->

[Caching with Fastly]: https://developer.adobe.com/commerce/webapi/graphql/usage/caching/#caching-with-fastly

[DDoS 攻撃の確認]: https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/checking-for-ddos-attack-from-cli.html

[Magento 2 用 Fastly CDN モジュール]: https://github.com/fastly/fastly-magento2

[Fastly サポートチケット]: https://docs.fastly.com/products/support-description-and-sla#support-requests

[悪意のあるトラフィックをブロックする方法]: https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/how-to/block-malicious-traffic-for-magento-commerce-on-fastly-level.html

[ドメインの操作]: https://docs.fastly.com/en/guides/working-with-domains
