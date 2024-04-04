---
title: Web アプリケーションファイアウォール (WAF)
description: Fastly WAF サービスがAdobe Commerceネットワークまたはサイトに損害を与える可能性のある前に、悪意のあるリクエストトラフィックを検出、ログ、およびブロックする方法について説明します。
feature: Cloud, Configuration, Security
exl-id: 40bfe983-7f32-4155-ae77-7cd18866f6e2
source-git-commit: 6b01bf91c3bf63ba268d0f8e10e19db4cb355997
workflow-type: tm+mt
source-wordcount: '824'
ht-degree: 0%

---

# Web アプリケーションファイアウォール (WAF)

Fastly を活用した、クラウドインフラストラクチャ上のAdobe Commerceの Web アプリケーションファイアウォール (WAF) サービスは、悪意のあるリクエストトラフィックを検出、ログ、およびブロックしてから、サイトやネットワークに損害を与えます。 WAF サービスは、実稼動環境でのみ使用できます。

WAF サービスには次のような利点があります。

- **PCI コンプライアンス**—WAF の有効化により、実稼動環境のAdobe Commerceストアフロントが PCI DSS 6.6 のセキュリティ要件を満たすようになります。
- **デフォルトの WAF ポリシー** — デフォルトの WAF ポリシーは、Fastly が設定および維持し、インジェクション攻撃、悪意のある入力、クロスサイトスクリプティング、データ抽出、HTTP プロトコル違反など、Adobe Commerce Web アプリケーションを様々な攻撃から保護するためのセキュリティ規則を提供します [OWASP トップ 10](https://owasp.org/www-project-top-ten/) セキュリティ上の脅威。
- **WAF のオンボーディングと有効化**—Adobeは、プロビジョニングが完了してから 2～3 週間以内に、実稼動環境でデフォルトの WAF ポリシーをデプロイし、有効にします。
- **運用とメンテナンスのサポート**—
   - Adobeと Fastly の設定および管理 WAF サービスのログとアラート。
   - Adobeは、WAF サービスの問題に関連するカスタマーサポートチケットをトライアジングし、正当なトラフィックを優先度 1 の問題としてブロックします。
   - WAF サービスバージョンへの自動アップグレードにより、新しい悪用や進化する悪用に即座に対応できます。 詳しくは、 [WAF のメンテナンスとアップグレード](#waf-maintenance-and-updates).

>[!TIP]
>
>クラウドインフラストラクチャストアでのAdobe Commerceの PCI コンプライアンスの維持について詳しくは、 [PCI コンプライアンス](https://business.adobe.com/products/magento/pci-compliance.html).

## WAF の有効化

Adobeは、プロビジョニングが完了してから 2 ～ 3 週間以内に新規アカウントで WAF サービスを有効にします。 WAF は Fastly CDN サービスを通じて実装されます。 ハードウェアやソフトウェアをインストールまたは保守する必要はありません。

>[!NOTE]
>
>WAF サービスを使用する前に、すべての外部トラフィックをクラウドインフラストラクチャプロジェクト上のAdobe Commerceにルーティングする必要があります。 詳しくは、 [Fastly の設定](fastly-configuration.md).

## 仕組み

WAF サービスは Fastly と統合され、Fastly CDN サービス内のキャッシュロジックを使用して、Fastly グローバルノードでトラフィックをフィルタリングします。 実稼動環境で WAF サービスを有効にし、デフォルトの WAF ポリシーを次に基づいて設定します。 [Trustwave SpiderLabs の ModSecurity ルール](https://github.com/owasp-modsecurity/ModSecurity) OWASP Top Ten のセキュリティ上の脅威。

WAF サービスは、HTTP および HTTPS トラフィック (GETおよびPOST要求 ) を WAF ルールセットに対してフィルタリングし、悪意のあるトラフィックや特定のルールに準拠していないトラフィックをブロックします。 このサービスは、キャッシュの更新を試みる接触チャネルに対するトラフィックのみをフィルタリングします。 その結果、Fastly キャッシュでの攻撃トラフィックのほとんどを停止し、悪意のある攻撃からオリジントラフィックを保護します。 WAF サービスは、接触チャネルトラフィックのみを処理することで、キャッシュパフォーマンスを維持し、キャッシュされていないリクエストごとに、推定 1.5 ミリ秒～ 20 ミリ秒の待ち時間のみが発生します。

## ブロックされた要求のトラブルシューティング

WAF サービスが有効な場合、すべての Web および管理トラフィックが WAF ルールに対してフィルタリングされ、ルールをトリガーする Web リクエストがブロックされます。 リクエストがブロックされると、リクエスト元にデフォルトの `403 Forbidden` ブロックイベントの参照 ID を含むエラーページ。

![WAF エラーページ](../../assets/cdn/fastly-waf-403-error.png)

このエラー応答ページは、管理者からカスタマイズできます。 詳しくは、 [WAF 応答ページのカスタマイズ](fastly-custom-response.md#customize-the-waf-error-page).

Adobe Commerceの管理ページまたはストアフロントが `403 Forbidden` 正当な URL リクエストに応答するエラーページ、 [Adobe Commerceサポートチケット](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket). エラー応答ページから参照 ID をコピーし、チケットの説明に貼り付けます。

## WAF のメンテナンスと更新

商用サードパーティ、Fastly リサーチ、オープンソースからのルール更新に基づいて、WAF ルールセットを厳密に管理および更新します。 必要に応じて、公開されたルールをポリシーに更新するか、ルールの変更がそれぞれのソースから利用できる場合に更新します。 また、Fastly は、WAF サービスを有効にした後、任意のサービスの WAF インスタンスに、公開されたルールクラスに一致するルールを追加できます。 これらの更新により、新しい悪用事項や進化する悪用事項を即時に対象にすることができます。

Adobeと Fastly は、更新プロセスを管理し、更新がブロックモードでデプロイされる前に、新しい WAF ルールや変更された WAF ルールが実稼動環境で効果的に機能するようにします。

## 制限事項

Fastly が提供する標準の WAF サービスは、次の機能をサポートしていません。

- マルウェアやボットの軽減に対する保護 — 使用を検討 [アクセス制御リスト](./fastly-vcl-allowlist.md) またはサードパーティのサービス
- レート制限 — 詳しくは、 [レート制限](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/RATE-LIMITING.md) Fastly ドキュメントのを参照するか、 [レート制限](https://developer.adobe.com/commerce/webapi/get-started/rate-limiting/) （内） _Commerce Web API_ セキュリティセクション。
- 顧客のログエンドポイントの設定 — 詳しくは、 [PrivateLink サービス](../development/privatelink-service.md) 別の方法として。

WAF サービスでは IP アドレスに基づくトラフィックのブロックや許可はできませんが、Fastly サービスにアクセス制御リスト (ACL) とカスタム VCL スニペットを追加して、IP アドレスとトラフィックのブロックや許可の VCL ロジックを指定できます。 詳しくは、 [カスタム Fastly VCL スニペット](fastly-vcl-custom-snippets.md).

TCP、UDP、または ICMP 要求のフィルタリングは、WAF サービスではサポートされていません。 ただし、この機能は Fastly CDN サービスに含まれる組み込みの DDoS 保護によって提供されます。 詳しくは、 [DDoS 保護](fastly.md#ddos-protection).
