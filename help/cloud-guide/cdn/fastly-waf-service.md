---
title: Web アプリケーションファイアウォール（WAF）
description: Adobe Commerce ネットワークまたはサイトに損害を与える前に、Fastly WAF サービスが悪意のあるリクエストトラフィックを検出、ログ記録、ブロックする仕組みについて説明します。
feature: Cloud, Configuration, Security
exl-id: 40bfe983-7f32-4155-ae77-7cd18866f6e2
source-git-commit: 6b01bf91c3bf63ba268d0f8e10e19db4cb355997
workflow-type: tm+mt
source-wordcount: '824'
ht-degree: 0%

---

# Web アプリケーションファイアウォール（WAF）

Fastly を活用した、クラウドインフラストラクチャー上のAdobe Commerceの web アプリケーションファイアウォール（WAF）サービスは、サイトやネットワークに損傷を与える前に、悪意のあるリクエストトラフィックを検出、ログに記録、ブロックします。 WAF サービスは、実稼動環境でのみ使用できます。

WAF サービスには次の利点があります。

- **PCI コンプライアンス**—WAF のイネーブルメントにより、実稼動環境のAdobe Commerce ストアフロントが PCI DSS 6.6 のセキュリティ要件を確実に満たすようになります。
- **デフォルトの WAF ポリシー** – デフォルトの WAF ポリシーは、Fastly で設定および管理され、インジェクション攻撃、悪意のある入力、クロスサイトスクリプティング、データ抽出、HTTP プロトコル違反などの、様々な攻撃からAdobe Commerce web アプリケーションを保護するようにカスタマイズされたセキュリティルールのコレクションを提供します [OWASP トップ 10](https://owasp.org/www-project-top-ten/) セキュリティの脅威。
- **WAF のオンボーディングと有効化**—Adobeは、プロビジョニングが完了してから 2～3 週間以内に、本番システム環境にデフォルトの WAF ポリシーを導入して有効にします。
- **運用・保守サポート**—
   - Adobeと Fastly は、WAF サービスのログとアラートをセットアップおよび管理します。
   - Adobeは、WAF サービスの問題に関連するカスタマーサポートチケットをトリアージし、正規のトラフィックをブロックする問題を優先度 1 の問題として処理します。
   - WAF サービスバージョンの自動アップグレードにより、新規または進化する攻撃を迅速に確実に検出できます。 参照： [WAF のメンテナンスとアップグレード](#waf-maintenance-and-updates).

>[!TIP]
>
>クラウドインフラストラクチャストア上のAdobe Commerceで PCI コンプライアンスを維持する方法について詳しくは、以下を参照してください [PCI コンプライアンス](https://business.adobe.com/products/magento/pci-compliance.html).

## WAF の有効化

Adobeは、プロビジョニングが完了してから 2～3 週間以内に、新しいアカウントに対して WAF サービスを有効にします。 WAF は Fastly CDN サービスを通じて実装されます。 ハードウェアやソフトウェアをインストールまたは保守する必要はありません。

>[!NOTE]
>
>WAF サービスを使用する前に、クラウドインフラストラクチャプロジェクト上のAdobe Commerceへのすべての外部トラフィックを、Fastly サービスを通じてルーティングする必要があります。 参照： [Fastly の設定](fastly-configuration.md).

## 仕組み

WAF サービスは Fastly と統合され、Fastly CDN サービス内のキャッシュロジックを使用して、Fastly グローバルノードでトラフィックをフィルタリングします。 実稼動環境では、に基づいたデフォルトの WAF ポリシーで WAF サービスを有効にします。 [Trustwave SpiderLabs の ModSecurity ルール](https://github.com/owasp-modsecurity/ModSecurity) OWASP Top Ten のセキュリティ上の脅威

WAF サービスは、WAF ルールセットに対して HTTP および HTTPS トラフィック（GETおよびPOSTリクエスト）をフィルタリングし、悪意のあるトラフィックや特定の規則に準拠しないトラフィックをブロックします。 サービスは、キャッシュの更新を試みるオリジンバインドトラフィックのみをフィルタリングします。 その結果、Fastly キャッシュでの攻撃トラフィックのほとんどを停止し、悪意のある攻撃からオリジントラフィックを保護します。 オリジントラフィックのみを処理することで、WAF サービスはキャッシュのパフォーマンスを維持し、キャッシュされていないリクエストごとに推定 1.5 ミリ秒から 20 ミリ秒の待ち時間しか発生しません。

## ブロックされたリクエストのトラブルシューティング

WAF サービスを有効にすると、WAF ルールに対して web および管理者のすべてのトラフィックがフィルタリングされ、ルールをトリガーする web リクエストがブロックされます。 リクエストがブロックされると、リクエスターにはデフォルトが表示されます `403 Forbidden` ブロックイベントの参照 ID を含むエラーページ。

![WAF エラーページ](../../assets/cdn/fastly-waf-403-error.png)

管理者からこのエラー応答ページをカスタマイズできます。 参照： [WAF 応答ページのカスタマイズ](fastly-custom-response.md#customize-the-waf-error-page).

Adobe Commerce管理ページまたはストアフロントがを返す場合 `403 Forbidden` エラーページ正当な URL リクエストに応答して、以下を送信します。 [Adobe Commerce サポートチケット](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket). エラー応答ページから参照 ID をコピーし、チケットの説明に貼り付けます。

## WAF のメンテナンスとアップデート

Fastly は、商用のサードパーティ、Fastly のリサーチ、およびオープンソースからのルール更新に基づいて、WAF ルールセットを維持および更新します。 Fastly は、必要に応じて、またはルールの変更がそれぞれのソースから利用可能な場合に、公開されたルールをポリシーに更新します。 また、Fastly は、WAF サービスが有効になった後に、公開されたルールのクラスに一致するルールを、任意のサービスの WAF インスタンスに追加できます。 これらの更新により、新しい攻撃や進化する攻撃に対する迅速な対応が保証されます。

Adobeおよび Fastly は更新プロセスを管理し、更新がブロッキングモードでデプロイされる前に、新規または変更された WAF ルールが実稼動環境で効果的に機能することを確認します。

## 制限事項

Fastly を利用した標準の WAF サービスでは、次の機能はサポートされていません。

- マルウェアまたはボットの軽減に対する保護 – の使用を検討 [アクセス制御リスト](./fastly-vcl-allowlist.md) またはサードパーティのサービスです。
- レート制限 – を参照 [レート制限](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/RATE-LIMITING.md) Fastly ドキュメントで、または [レート制限](https://developer.adobe.com/commerce/webapi/get-started/rate-limiting/) が含まれる _Commerce Web API_ セキュリティ セクション。
- 顧客のログエンドポイントの設定 – を参照してください [PrivateLink サービス](../development/privatelink-service.md) 代替手段として。

WAF サービスでは、IP アドレスに基づくトラフィックのブロックや許可は許可されませんが、Fastly サービスに ACL （アクセス制御リスト）スニペットとカスタム VCL スニペットを追加して、トラフィックのブロックや許可を行うための IP アドレスと VCL ロジックを指定することができます。 参照： [カスタム Fastly VCL スニペット](fastly-vcl-custom-snippets.md).

TCP、UDP、または ICMP 要求のフィルタリングは、WAF サービスではサポートされていません。 ただし、この機能は、Fastly CDN サービスに含まれる組み込みの DDoS 保護によって提供されます。 参照： [DDoS 保護](fastly.md#ddos-protection).
