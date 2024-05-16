---
title: 技術スタック
description: クラウドインフラストラクチャー上のCommerceを構成するテクノロジースタックを参照してください。
feature: Cloud, Iaas, Paas
exl-id: e456db25-c44b-4053-b96d-517d3d1606d0
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '374'
ht-degree: 0%

---

# 技術スタック

次に示すように、クラウドインフラストラクチャー上のAdobe Commerceは 5 つの機能レイヤーと考えることができます。

![クラウドスタック](../../assets/CloudStack.svg)

1. [**クラウドインフラストラクチャ**](pro-architecture.md):Cloud Infrastructure Pro プロジェクト上のAdobe Commerceの基盤として、Amazon Web Services（AWS）またはMicrosoft Azure （IaaS）のいずれかを選択します。

   Adobeでは、仮想コンピューティングリソース（vCPU）の使用状況を定期的に分析し、リソースを自動的に割り当てて、長期使用を最適化し、vCPU の 1 日の最大許容量を超えるリスクを軽減します。 特定の期間にサイトトラフィックの増加が予想される場合は、引き続き以下に対してサポートチケットを開く必要があります。 [一時的なアップサイズをリクエスト](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/how-to/how-to-request-temporary-magento-upsize.html).

1. [**サービスとしてのプラットフォーム**](cloud-architecture.md)：クラウドインフラストラクチャプロジェクトの各Adobe Commerceは、サービスの開発、テスト、統合のための Platform as a Service （PaaS）統合環境を提供します。
1. [**Adobe Commerce**](../project/overview.md)：クラウドインフラストラクチャー上のAdobe Commerceは、PHP、MySQL （MariaDB）、Redis、 [!DNL RabbitMQ]、およびサポートされる検索エンジンテクノロジー。
1. [**パフォーマンスツール**](../monitor/new-relic-service.md):New Relic パフォーマンスツールを使用すると、クラウドインフラストラクチャプロジェクト上のAdobe Commerceからデータを収集、分析、表示することで、アプリケーションおよびインフラストラクチャをデバッグ、モニタリング、管理できます。
1. [**コンテンツ配信ネットワーク（CDN）、web アプリケーションファイアウォール（[!DNL WAF]）、および画像の最適化（IO）**](../cdn/fastly.md):

   * [Fastly CDN](../cdn/fastly.md#ddos-protection) – 次のような分散型サービス拒否（DDoS）攻撃からの組み込み保護機能を備えた安全な CDN サービスを提供します。 [!DNL Ping of Death], [!DNL Smurf] 攻撃、およびその他の Internet Control Message Protocol （ICMP; インターネット制御メッセージ プロトコル）ベースのフラッド攻撃。
   * [Web アプリケーションファイアウォール（WAF）](../cdn/fastly-waf-service.md)—WAF サービスは、実稼動環境のAdobe Commerce ストアフロントの PCI コンプライアンスを確保します。また、WAF ポリシーを使用して、インジェクション攻撃、悪意のある入力、クロスサイトスクリプティング、データ抽出、HTTP プロトコル違反などからAdobe Commerce Web アプリケーションを保護します [[!DNL OWASP] セキュリティ上の脅威トップ 10](https://owasp.org/www-project-top-ten/).
   * [画像の最適化（IO）](../cdn/fastly-image-optimization.md) – リアルタイムの画像操作と最適化を実現し、レスポンシブ Web アプリケーション向けの画像配信を高速化し、画像ソース・セットのメンテナンスを合理化します。 Fastly IO は、画像処理やサイズ変更の負荷を軽減し、サーバーを解放して注文やコンバージョンを効率的に処理します。

モノリシックアプリケーションはリソースを大量に消費するので、拡張や迅速な提供が困難です。 クラウドインフラストラクチャを使用すると、Commerceのお客様は、豊富でインテリジェント、かつ高性能な SaaS ベースのマイクロサービスに比類のないアクセスができます。 参照： [サポート対象のソフトウェアとサービス](cloud-architecture.md#supported-software-and-services).

の使用 [Commerce入門ガイド](../../get-started/overview.md) 新しいクラウドプログラムを設定して管理を開始するには [!DNL Commerce] クラウドネイティブ環境でのアプリケーション。
