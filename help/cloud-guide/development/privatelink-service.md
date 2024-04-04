---
title: PrivateLink サービス
description: PrivateLink サービスを使用して、同じ地域のプライベートクラウドとAdobe Commerceクラウドプラットフォーム間の安全な接続を確立する方法について説明します。
feature: Cloud, Iaas, Security
exl-id: b25548b8-312b-4a74-b242-f4e2ac6cf945
source-git-commit: 5b52157c6c623bb4c765a2d545919df79d81f1da
workflow-type: tm+mt
source-wordcount: '1609'
ht-degree: 0%

---

# PrivateLink サービス

Adobe Commerce on cloud infrastructure は、 [AWS PrivateLink](https://aws.amazon.com/privatelink/) または [Azure プライベートリンク](https://learn.microsoft.com/en-us/azure/private-link/) サービス。 PrivateLink を使用すると、外部システムでホストされるサービスやアプリケーションを使用して、クラウドインフラストラクチャ環境上のAdobe Commerce間で安全でプライベートな通信を確立できます。 Adobe Commerceアプリケーションと外部システムの両方に、同じクラウドリージョン内の同じクラウドプラットフォーム (AWSまたは Azure) 上に設定された Virtual Private Cloud(VPC) エンドポイントを通じてアクセスできる必要があります。

>[!TIP]
>
>PrivateLink は、データベースやファイル転送など、HTTP(S) 以外の統合に対する接続を保護するのに最適です。 アプリケーションをAdobe Commerce API と統合する予定がある場合は、 [AdobeAPI メッシュ](https://developer.adobe.com/graphql-mesh-gateway/gateway/create-mesh/) in _Adobe Developer App Builder の API メッシュ_.

## 機能とサポート

クラウドインフラストラクチャプロジェクト上のAdobe Commerceに対する PrivateLink サービスの統合には、次の機能とサポートが含まれています。

- 同じクラウドリージョン内の同じクラウドプラットフォーム (AWSまたは Azure) 上の顧客 Virtual Private Cloud(VPC) とAdobeVPC との間の安全な接続。
- エンドポイントとお客様の VPC で利用可能なエンドポイントサービス間でのAdobeまたは双方向通信のサポート。
- サービスの有効化：

   - クラウドインフラストラクチャ環境のAdobe Commerceで必要なポートを開く
   - 顧客とAdobeVPC 間の初期接続を確立する
   - イネーブルメント中の接続の問題のトラブルシューティング

## 制限事項

- PrivateLink のサポートは、実稼動環境およびステージング環境でのみ利用できます。 ローカル環境や統合環境、またはスタータープロジェクトでは使用できません。
- PrivateLink を使用して SSH 接続を確立することはできません。 詳しくは、 [SSH キーを有効にする](secure-connections.md).
- Adobe Commerceのサポートでは、最初にを有効にする以外に、AWS PrivateLink の問題のトラブルシューティングは行われません。
- お客様は、独自の VPC の管理に関連するコストを責任を負います。
- Azure Private Link を介してクラウドインフラストラクチャ上のAdobe Commerceに接続する際に、HTTPS プロトコル（ポート 443）を使用できません。原因： [精密な起源のクローキング](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/faq/fastly-origin-cloaking-enablement-faq.html). この制限は、AWS PrivateLink には適用されません。
- PrivateDNS は使用できません。

## PrivateLink の接続タイプ

次のネットワーク図に示すように、PrivateLink の接続タイプは 2 つあります。これらを使用して、Cloud 環境の外部でホストされる外部システムとストア間の安全な通信を確立できます。

![PrivateLink のネットワーク図](../../assets/privatelink-architecture-diagram.png)

クラウドインフラストラクチャ環境上のAdobe Commerceに最も適した PrivateLink 接続タイプを 1 つ選択します。

- **単方向プライベートリンク** — クラウドインフラストラクチャストア上のAdobe Commerceからデータを安全に取得するには、この設定を選択します。
- **双方向 PrivateLink** — クラウドインフラストラクチャ環境上のAdobe Commerce外のシステムとの間に安全な接続を確立するには、この設定を選択します。 双方向オプションには、次の 2 つの接続が必要です。

   - 顧客 VPC とAdobeVPC 間の接続
   - AdobeVPC と顧客 VPC 間の接続

>[!TIP]
>
>PrivateLink の接続タイプの選択や VPC の設定と管理に関するヘルプについては、ネットワーク管理者またはクラウドプラットフォームプロバイダに相談してください。 Cloud Platform PrivateLink のドキュメントを参照してください。 [AWS PrivateLink](https://aws.amazon.com/privatelink/) または [Azure プライベートリンク](https://learn.microsoft.com/en-us/azure/private-link/).

## PrivateLink の有効化をリクエスト

>[!WARNING]
>
>PrivateLink を有効にすると、最大で _5_ 営業日。 不完全な情報や不正確な情報を提供すると、プロセスが遅れる可能性があります。

### 前提条件

![check](../../assets/fix.svg) クラウドインフラストラクチャインスタンス上のAdobe Commerceと同じ地域の Cloud アカウント (AWSまたは Azure)。

![check](../../assets/fix.svg) PrivateLink を介して接続するサービスをホストする顧客環境内の VPC。 VPC の設定に関するヘルプについては、 AWSまたは Azure のドキュメントを参照するか、ネットワーク管理者に問い合わせてください。

![check](../../assets/fix.svg) 双方向の PrivateLink 接続の場合は、PrivateLink 有効化を要求する前に、アプリケーションまたはサービスのエンドポイントサービス設定を作成し、VPC 環境にエンドポイントを作成する必要があります。 詳しくは、 [双方向の PrivateLink 接続のセットアップ](#set-up-for-bidirectional-privatelink-connections).

PrivateLink の有効化に必要な次のデータを収集します。

- **Customer Cloud アカウント番号** (AWSまたは Azure)- Adobe Commerce on cloud infrastructure インスタンスと同じ地域に存在する必要があります。
- **クラウド地域** — 検証のためにアカウントがホストされるクラウドリージョンを指定します。
- **サービスと通信ポート**—Adobeは、VPC 間のサービス通信を可能にするポートを開く必要があります（SQL ポート 3306、SFTP ポート 222 など）。
- **プロジェクト ID** — クラウドインフラストラクチャ上のAdobe Commerce Pro プロジェクト ID を指定します。 次の情報を使用して、プロジェクト ID やその他のプロジェクト情報を取得できます [Cloud CLI](../dev-tools/cloud-cli-overview.md) コマンド： `magento-cloud project:info`
- **接続タイプ** — 接続タイプに対して単方向または双方向を指定します。
- **エンドポイントサービス** — 双方向の PrivateLink 接続の場合、Adobeが接続する必要のある VPC エンドポイントサービスの DNS URL を指定します。次に例を示します。 `com.amazonaws.vpce.<cloud-region>.vpce-svc-<service-id>`
- **エンドポイントサービスへのアクセスが許可されました** — 外部サービスに接続するには、エンドポイントサービスが次のAWSアカウントプリンシパルにアクセスできるようにします。 `arn:aws:iam::402592597372:root`

  >[!WARNING]
  >
  >エンドポイントサービスへのアクセスが提供されない場合、VPC 内のサービスへの双方向の PrivateLink 接続は次のようになります。 **not** が追加され、設定が遅れます。

#### Azure プライベートリンクの有効化に関するその他の前提条件

- クラスタ ID を指定します。SSH を使用してリモートにログインし、次のコマンドを使用します。 `cat /etc/platform_cluster`
- 外部サービスをAdobe Commerce Pro クラスターに接続するには、次が必要です。

   - 新しい外部プライベートエンドポイントに公開する Pro クラスター上のポートのリスト
   - プライベートエンドポイント接続用の Azure サブスクリプション ID のリスト

- Adobe Commerce Pro クラスターを外部サービスに接続するには、次が必要です。

   - ターゲットサービスのリソース ID のリスト。 外部プライベートリンクサービス ID は次のようになります。

  ```text
  /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/privateLinkServices/{svcNameID}
  ```

### 有効化ワークフロー

次のワークフローでは、クラウドインフラストラクチャ上のAdobe Commerceとの PrivateLink の統合を有効にするプロセスについて説明します。

1. **顧客** 件名行で PrivateLink の有効化をリクエストするサポートチケットを送信します `PrivateLink support for <company>`. 次を含める： [イネーブルメントに必要なデータ](#prerequisites) チケットの中に。 Adobeは、サポートチケットを使用して、イネーブルメントプロセス中にコミュニケーションを調整します。

1. **Adobe** AdobeVPC のエンドポイントサービスに対する顧客アカウントのアクセスを有効にします。

   - お客様のAWSまたは Azure アカウントから開始されたリクエストを受け入れるように、Adobeエンドポイントサービス設定を更新します。
   - サポートチケットを更新し、接続先のAdobeVPC エンドポイントのサービス名を指定します。例： `com.amazonaws.vpce.<cloud-region>.vpce-svc-<service-id>`.

1. **顧客** Adobeエンドポイントサービスを Cloud アカウント (AWSまたは Azure) に追加します。このアカウントは、Adobeへの接続リクエストをトリガーします。 手順については、クラウドプラットフォームのドキュメントを参照してください。

   - AWSについては、 [インターフェイスエンドポイント接続要求の受け入れと拒否].
   - Azure の場合は、 [接続リクエストの管理].

1. **Adobe** 接続リクエストを承認します。

1. 接続リクエストの承認後、 **顧客** [接続を検証します。](#test-vpc-endpoint-service-connection) VPC とAdobeVPC の間で

1. 双方向接続を有効にするための追加手順：

   - **Adobe** は、Adobeアカウントプリンシパル (AWSまたは Azure アカウントのルートユーザー ) を提供し、お客様の VPC エンドポイントサービスへのアクセスを要求します。
   - **顧客** は、顧客 VPC 内のエンドポイントサービスへのAdobeアクセスを有効にします。 これは、Adobeアカウントプリンシパルが `arn:aws:iam::402592597372:root`( 前述の **エンドポイントサービスへのアクセスが許可されました** 前提条件。

      - 顧客アカウントから開始されたリクエストを受け入れるように、Adobeエンドポイントサービス設定を更新します。 手順については、クラウドプラットフォームのドキュメントを参照してください。

         - AWSについては、 [エンドポイントサービスの権限の追加と削除].
         - Azure の場合は、 [プライベートエンドポイント接続の管理]

      - Adobeに、顧客 VPC のエンドポイントサービス名を指定します。

   - **Adobe** 顧客エンドポイントサービスをAdobeプラットフォームアカウント (AWSまたは Azure) に追加し、顧客 VPC への接続リクエストをトリガーします。
   - **顧客** 接続から接続リクエストを承認し、Adobeを完了します。
   - **顧客** [接続を検証します。](#test-vpc-endpoint-service-connection) AdobeVPC から。

## VPC エンドポイントサービス接続のテスト

Telnet アプリケーションを使用して、VPC エンドポイントサービスへの接続をテストできます。

**VPC エンドポイントサービスへの接続をテストするには**:

1. プロジェクトのルートディレクトリから、 **チェックアウト** PrivateLink エンドポイントサービスにアクセスするように設定されたステージング環境または実稼動環境。

   ```bash
   magento-cloud environment:checkout <environment-id>
   ```

1. 次の CURL コマンドを実行します。

   ```bash
   curl -v telnet://<endpoint-service-dns-url>:<port>/
   ```

   例：

   ```terminal
   $ curl -v telnet://vpce-007ffnb9qkcnjgult-yfhmywqh.vpce-svc-083cqvm2ta3rxqat5v.us-east-1.vpce.amazonaws.com:80 -vvv
   ```

   成功した応答の例：

   ```terminal
   * Rebuilt URL to: telnet://vpce-007ffnb9qkcnjgult-yfhmywqh.vpce-svc-083cqvm2ta3rxqat5v.us-east-1.vpce.amazonaws.com:80
   * Connected to vpce-0088d56482571241d-yfhmywqh.vpce-svc-083cqvm2ta3rxqat5v.us-east-1.vpce. amazonaws.com (191.210.82.246) port 80 (#0)
   ```

   失敗した応答の例：

   ```terminal
   Failed to connect to vpce-007ffnb9qkcnjgult-yfhmywqh.vpce-svc-083cqvm2ta3rxqat5v.ap-southeast-1.vpce.amazonaws.com port 80: Connection timed out
   * Closing connection 0
   ```

1. サービスが VM でリッスンしていることを確認します。

   ```bash
   netstat -na | grep <port>
   ```

1. パッケージのフローを確認します。

   ```bash
   tcpdump -i <ethernet-interface> -tt -nn port <destination-port> and host <source-host>
   ```

   次の内部設定を調べて、設定が有効であることを確認します。

   - エンドポイントとエンドポイントサービスの設定
   - ネットワークロードバランサー (NLB) の設定
   - NLB のターゲットグループと、それらが正常であることを確認します
   - 各 VM からの netcat/curl エンドポイント URL（上記を参照）

   接続の問題のトラブルシューティングに関するヘルプについては、次の記事を参照してください。

   - [AWS：エンドポイントサービス接続のトラブルシューティング]
   - [Amazon:Azure Private Link の接続に関する問題のトラブルシューティング]

   エラーを解決できない場合は、Adobe Commerceサポートチケットを更新して、接続の確立に関するヘルプをリクエストします。

## PrivateLink 設定の変更

[Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) をクリックして、既存の PrivateLink 設定を変更します。 例えば、次のような変更をリクエストできます。

- PrivateLink 接続をクラウドインフラストラクチャ Pro 実稼動またはステージング環境のAdobe Commerceから削除します。
- 顧客エンドポイントサービスにアクセスするための顧客の CloudAdobeアカウント番号を変更します。
- AdobeVPC から、お客様の VPC 環境で使用可能な他のエンドポイントサービスへの PrivateLink 接続を追加または削除します。

## 双方向の PrivateLink 接続のセットアップ

顧客 VPC は、双方向の PrivateLink 接続をサポートするために、次のリソースを使用できる必要があります。

- ネットワークロードバランサー (NLB)
- 顧客 VPC からのアプリケーションまたはサービスへのアクセスを可能にするエンドポイントサービス設定
- An [インターフェイスエンドポイント] (AWS) または [プライベートエンドポイント] (Azure)Adobeが VPC でホストされるエンドポイントサービスに接続できるようにする

これらのリソースがカスタマー VPC で使用できない場合は、Cloud Platform アカウントにサインインして設定を追加する必要があります。

- Amazon VPC コンソール — `https://console.aws.amazon.com/vpc/`
- Azure ポータル — `https://portal.azure.com`

PrivateLink の設定手順については、使用するクラウドプラットフォームのドキュメントを参照してください。

- **AWS PrivateLink のドキュメント**
   - [ネットワークロードバランサーの作成]
   - [エンドポイントサービス設定の作成]
   - [インターフェイスエンドポイントの作成]
   - [インターフェイスエンドポイントのライフサイクル]

- **Azure PrivateLink のドキュメント**
   - [ロードバランサーの作成]
   - [Azure プライベートリンクワークフロー]

<!--Link definitions-->

[インターフェイスエンドポイント接続要求の受け入れと拒否]: https://docs.aws.amazon.com/vpc/latest/userguide/accept-reject-endpoint-requests.html
[エンドポイントサービスの権限の追加と削除]: https://docs.aws.amazon.com/vpc/latest/userguide/add-endpoint-service-permissions.html
[Amazon:Azure Private Link の接続に関する問題のトラブルシューティング]: https://docs.microsoft.com/en-us/azure/private-link/troubleshoot-private-link-connectivity
[AWS：エンドポイントサービス接続のトラブルシューティング]: https://aws.amazon.com/premiumsupport/knowledge-center/connect-endpoint-service-vpc/
[Azure プライベートリンクワークフロー]: https://docs.microsoft.com/en-us/azure/private-link/private-link-service-overview#workflow
[ロードバランサーの作成]: https://docs.microsoft.com/en-us/azure/load-balancer/quickstart-load-balancer-standard-public-portal
[ネットワークロードバランサーの作成]: https://docs.aws.amazon.com/elasticloadbalancing/latest/network/create-network-load-balancer.html
[エンドポイントサービス設定の作成]: https://docs.aws.amazon.com/vpc/latest/userguide/create-endpoint-service.html
[インターフェイスエンドポイントの作成]: https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#create-interface-endpoint
[interface endpoint lifecycle]: https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#vpce-interface-lifecycle
[インターフェイスエンドポイント]: https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html
[プライベートエンドポイント接続の管理]: https://docs.microsoft.com/en-us/azure/private-link/manage-private-endpoint
[接続リクエストの管理]: https://docs.microsoft.com/en-us/azure/private-link/private-link-service-overview#manage-your-connection-requests
[プライベートエンドポイント]: https://docs.microsoft.com/en-us/azure/private-link/private-endpoint-overview
