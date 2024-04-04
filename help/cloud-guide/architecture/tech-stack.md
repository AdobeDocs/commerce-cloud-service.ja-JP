---
title: 技術スタック
description: Cloud インフラストラクチャ上のコマースを形成するテクノロジースタックを確認します。
feature: Cloud, Iaas, Paas
exl-id: e456db25-c44b-4053-b96d-517d3d1606d0
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '374'
ht-degree: 0%

---

# 技術スタック

次に示すように、クラウドインフラストラクチャ上のAdobe Commerceは 5 つの機能レイヤーと考えてください。

![クラウドスタック](../../assets/CloudStack.svg)

1. [**クラウドインフラストラクチャ**](pro-architecture.md)：クラウドインフラストラクチャ Pro プロジェクト上のAdobe Commerceの基盤として、Amazon Web Services(AWS) またはMicrosoft Azure を Infrastructure as a Service(IaaS) として選択します。

   Adobeは、仮想計算リソース (vCPU) の使用状況を定期的に分析し、リソースを自動的に割り当てて長期的な使用状況を最適化し、年間最大 vCPU 使用量を超えるリスクを軽減します。 特定の期間でサイトトラフィックが増加すると予想される場合は、次の期間に対してサポートチケットを開く必要があります。 [一時的なアップサイズを要求する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/how-to/how-to-request-temporary-magento-upsize.html).

1. [**Platform as a Service**](cloud-architecture.md)：各Adobe Commerce on cloud infrastructure プロジェクトは、サービスの開発、テスト、統合を行う Platform as a Service(PaaS) 統合環境を提供します。
1. [**Adobe Commerce**](../project/overview.md):Adobe Commerce on cloud infrastructure は、PHP、MySQL(MariaDB)、Redis を含む、事前にプロビジョニングされたインフラストラクチャを提供します。 [!DNL RabbitMQ]、およびサポートされる検索エンジンテクノロジー。
1. [**パフォーマンスツール**](../monitor/new-relic-service.md):New Relicのパフォーマンスツールを使用すると、Adobe Commerceからクラウドインフラストラクチャプロジェクト上のデータを収集、分析、表示することで、アプリケーションとインフラストラクチャをデバッグ、監視、管理できます。
1. [**コンテンツ配信ネットワーク (CDN)、Web アプリケーションファイアウォール ([!DNL WAF])、および画像最適化 (IO)**](../cdn/fastly.md):

   * [Fastly CDN](../cdn/fastly.md#ddos-protection) — のような分散型 DoS(DoS) 攻撃からの保護を組み込んだ、セキュアな CDN サービスを提供します。 [!DNL Ping of Death], [!DNL Smurf] 攻撃、およびその他の ICMP(Internet Control Message Protocol) ベースのフラッド攻撃。
   * [Web アプリケーションファイアウォール (WAF)](../cdn/fastly-waf-service.md)—WAF サービスは、インジェクション攻撃、悪意のある入力、クロスサイトスクリプティング、データ抽出、HTTP プロトコル違反などからAdobe Commerce Web アプリケーションを保護する実稼動環境および WAF ポリシーの PCI コンプライアンスを確保します。 [[!DNL OWASP] セキュリティ上の脅威トップ 10](https://owasp.org/www-project-top-ten/).
   * [画像の最適化 (IO)](../cdn/fastly-image-optimization.md) — リアルタイムの画像操作と最適化を提供し、画像の配信を高速化し、レスポンシブ Web アプリケーション用の画像ソースセットのメンテナンスを簡単にします。 Fastly IO は、画像処理とサイズ変更の負荷をオフロードし、サーバを解放して注文とコンバージョンを効率的に処理します。

モノリシックアプリケーションは、リソースを大量に消費し、迅速に拡張および提供するのが困難です。 Cloud インフラストラクチャを使用すると、Commerce のお客様は、豊富でインテリジェントでパフォーマンスの高い SaaS ベースのマイクロサービスに比類のないアクセスを得ることができます。 詳しくは、 [サポートされるソフトウェアとサービス](cloud-architecture.md#supported-software-and-services).

以下を使用します。 [コマース入門ガイド](../../get-started/overview.md) 新しいクラウドプログラムを設定し、 [!DNL Commerce] クラウドネイティブ環境のアプリケーション。
