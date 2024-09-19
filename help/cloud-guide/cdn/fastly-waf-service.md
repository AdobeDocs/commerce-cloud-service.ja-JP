---
title: Web アプリケーションファイアウォール（WAF）
description: Fastly WAF サービスがAdobe Commerce ネットワークまたはサイトに損害を与える前に、悪意のあるリクエストトラフィックを検出、ログに記録、ブロックする方法について説明します。
feature: Cloud, Configuration, Security
exl-id: 40bfe983-7f32-4155-ae77-7cd18866f6e2
source-git-commit: bd03b2278869b40bd4a8c3395f7061d13680696d
workflow-type: tm+mt
source-wordcount: '886'
ht-degree: 0%

---

# Web アプリケーションファイアウォール（WAF）

Fastly を活用した、クラウドインフラストラクチャー上のAdobe Commerceの web アプリケーションファイアウォール（WAF）サービスは、サイトやネットワークに損傷を与える前に、悪意のあるリクエストトラフィックを検出、ログに記録、ブロックします。 WAF サービスは、実稼動環境でのみ使用できます。

WAF サービスには次の利点があります。

- **PCI コンプライアンス** - WAFの有効化により、実稼動環境のAdobe Commerce ストアフロントが PCI DSS 6.6 のセキュリティ要件を確実に満たすようにします。
- **デフォルトのWAF ポリシー** - デフォルトのWAF ポリシーは、Fastly によって設定および管理され、インジェクション攻撃、悪意のある入力、クロスサイトスクリプティング、データの取り出し、HTTP プロトコル違反およびその他の [Adobe Commerce Top Ten](https://owasp.org/www-project-top-ten/) セキュリティ脅威を含む様々な攻撃からOWASP web アプリケーションを保護するようにカスタマイズされたセキュリティルールのコレクションを提供します。
- **WAFのオンボーディングとイネーブルメント** - Adobeは、プロビジョニングが完了してから 2～3 週間以内に、実稼動環境にデフォルトのWAF ポリシーをデプロイし、有効にします。
- **運用・保守サポート**—
   - Adobeを行い、WAF サービスのログとアラートを Fastly で設定および管理します。
   - Adobeは、正当なトラフィックをブロックするWAF サービスの問題に関連するカスタマーサポートチケットを優先度 1 の問題としてトリアージします。
   - WAF サービスバージョンへの自動アップグレードにより、新しい攻撃や進化する攻撃を確実かつ迅速に回避できます。 [WAFのメンテナンスとアップグレード ](#waf-maintenance-and-updates) を参照してください。

>[!TIP]
>
>クラウドインフラストラクチャストア上のAdobe Commerceで PCI への準拠を維持する方法について詳しくは、[PCI コンプライアンス ](https://business.adobe.com/products/magento/pci-compliance.html) を参照してください。

## WAFの有効化

Adobeは、プロビジョニングが完了してから 2～3 週間以内に、新しいアカウントに対してWAF サービスを有効にします。 WAFは、Fastly CDN サービスを通じて実装されます。 ハードウェアやソフトウェアをインストールまたは保守する必要はありません。

>[!NOTE]
>
>WAF サービスを使用する前に、クラウドインフラストラクチャプロジェクト上のAdobe Commerceへのすべての外部トラフィックが、Fastly サービスを通じてルーティングされる必要があります。 [Fastly の設定 ](fastly-configuration.md) を参照してください。

## 仕組み

WAF サービスは Fastly と統合され、Fastly CDN サービス内のキャッシュロジックを使用して、Fastly グローバルノードでトラフィックをフィルタリングします。 [Trustwave SpiderLabs の ModSecurity ルール ](https://github.com/owasp-modsecurity/ModSecurity) とOWASP Top Ten Security Threat に基づくデフォルトのWAF ポリシーを使用して、実稼動環境でWAF サービスを有効にします。

WAF サービスは、WAF ルールセットに対して HTTP および HTTPS トラフィック（GETおよびPOSTリクエスト）をフィルタリングし、悪意のあるトラフィックや特定の規則に準拠しないトラフィックをブロックします。 サービスは、キャッシュの更新を試みるオリジンバインドトラフィックのみをフィルタリングします。 その結果、Fastly キャッシュでの攻撃トラフィックのほとんどを停止し、悪意のある攻撃からオリジントラフィックを保護します。 オリジントラフィックのみを処理することで、WAF サービスはキャッシュのパフォーマンスを維持し、キャッシュされていないリクエストごとに推定 1.5 ～ 20 ミリ秒の待ち時間しか発生しません。

## ブロックされたリクエストのトラブルシューティング

WAF サービスを有効にすると、WAFのルールに対して web および管理者トラフィックがすべてフィルタリングされ、ルールをトリガーするすべての web リクエストがブロックされます。 リクエストがブロックされると、ブロッキングイベントの参照 ID を含んだデフォルトの `403 Forbidden` エラーページがリクエスターに表示されます。

![WAF エラーページ ](../../assets/cdn/fastly-waf-403-error.png)

管理者からこのエラー応答ページをカスタマイズできます。 [WAF応答ページのカスタマイズ ](fastly-custom-response.md#customize-the-waf-error-page) を参照してください。

Adobe Commerce管理ページまたはストアフロントが、正当な URL リクエストに対して `403 Forbidden` エラーページを返した場合は、[Adobe Commerce サポートチケット ](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) を送信します。 エラー応答ページから参照 ID をコピーし、チケットの説明に貼り付けます。

## WAFのメンテナンスとアップデート

Fastly は、商用のサードパーティ、Fastly の調査、オープンソースからのルール更新に基づいて、WAF ルールセットを維持および更新します。 Fastly は、必要に応じて、またはルールの変更がそれぞれのソースから利用可能な場合に、公開されたルールをポリシーに更新します。 また、Fastly では、WAF サービスが有効になった後で、公開されたルールクラスに一致するルールを、任意のサービスのWAF インスタンスに追加することができます。 これらの更新により、新しい攻撃や進化する攻撃に対する迅速な対応が保証されます。

Adobeおよび Fastly は更新プロセスを管理し、更新がブロッキングモードでデプロイされる前に、新規または変更されたWAF ルールが実稼動環境で効果的に機能することを確認します。

## 問題

WAFが正当なリクエストをブロックしている場合、多くの場合、これらは誤検出であり、バイパスするか、WAF サービスで対策を実装する必要があります。 サポートチケットを送信し、影響を受ける URL、エラーを再現するための正確な手順、トランスクリプションエラーを避けるためのエラー参照を（スクリーンショットではなく）テキスト形式で含めます。

## 制限事項

Fastly を利用した標準のWAF サービスは、次の機能をサポートしていません。

- マルウェアまたはボットの軽減に対する保護 – [ アクセス制御リスト ](./fastly-vcl-allowlist.md) またはサードパーティのサービスの使用を検討します。
- レート制限 – Fastly ドキュメントの [ レート制限 ](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/RATE-LIMITING.md) または _Commerce Web API_ セキュリティセクションの [ レート制限 ](https://developer.adobe.com/commerce/webapi/get-started/rate-limiting/) を参照してください。
- 顧客のログエンドポイントの設定 – 別の方法として [PrivateLink サービス ](../development/privatelink-service.md) を参照してください。

WAF サービスでは、IP アドレスに基づくトラフィックのブロックや許可は許可されていませんが、Fastly サービスにアクセス制御リスト（ACL）スニペットとカスタム VCL スニペットを追加して、トラフィックのブロックや許可を行うための IP アドレスと VCL ロジックを指定することができます。 [Custom Fastly VCL スニペット ](fastly-vcl-custom-snippets.md) を参照してください。

WAF サービスでは、TCP、UDP、ICMP の各リクエストのフィルタリングはサポートされていません。 ただし、この機能は、Fastly CDN サービスに含まれる組み込みの DDoS 保護によって提供されます。 [DDoS 保護 ](fastly.md#ddos-protection) を参照してください。
