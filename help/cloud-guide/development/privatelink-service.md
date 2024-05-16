---
title: PrivateLink サービス
description: PrivateLink サービスを使用して、同じリージョンのプライベートクラウドとAdobe Commerce クラウドプラットフォームの間に安全な接続を確立する方法を説明します。
feature: Cloud, Iaas, Security
exl-id: b25548b8-312b-4a74-b242-f4e2ac6cf945
source-git-commit: 5b52157c6c623bb4c765a2d545919df79d81f1da
workflow-type: tm+mt
source-wordcount: '1609'
ht-degree: 0%

---

# PrivateLink サービス

クラウドインフラストラクチャー上のAdobe Commerceは、との統合をサポートします。 [AWS PrivateLink](https://aws.amazon.com/privatelink/) または [Azure プライベートリンク](https://learn.microsoft.com/en-us/azure/private-link/) サービス。 PrivateLink を使用すると、クラウドインフラストラクチャ環境上のAdobe Commerceと、外部システムでホストされるサービスおよびアプリケーションとの間に、安全なプライベート通信を確立できます。 Adobe Commerce アプリケーションと外部システムの両方に、同じクラウドリージョン内の同じクラウドプラットフォーム（AWSまたは Azure）上に設定された Virtual Private Cloud （VPC）エンドポイントを通じてアクセスできる必要があります。

>[!TIP]
>
>PrivateLink は、データベースやファイル転送などの非 HTTP （S）統合用の接続を保護するのに最適です。 アプリケーションをAdobe Commerce API と統合する場合は、 [AdobeAPI メッシュ](https://developer.adobe.com/graphql-mesh-gateway/gateway/create-mesh/) 。対象： _Adobe Developer App Builder の API メッシュ_.

## 機能とサポート

クラウドインフラストラクチャプロジェクト上のAdobe Commerce向け PrivateLink サービス統合には、次の機能およびサポートが含まれます。

- お客様の Virtual Private Cloud （VPC）と、同じクラウドリージョン内の同じクラウドプラットフォーム（AWSまたは Azure）上のAdobeVPC との安全な接続。
- AdobeVPC とお客様 VPC のエンドポイントサービス間での一方向性または双方向通信のサポート。
- サービス有効化：

   - クラウドインフラストラクチャー上のAdobe Commerceで必要なポートを開きます。
   - お客様とAdobeの VPC 間の初期接続の確立
   - イネーブルメント中の接続の問題のトラブルシューティング

## 制限事項

- PrivateLink のサポートは、実稼動環境およびステージング環境でのみ使用できます。 ローカル環境、統合環境、スタータープロジェクトでは使用できません。
- PrivateLink を使用して SSH 接続を確立することはできません。 参照： [SSH キーを有効にする](secure-connections.md).
- Adobe Commerce サポートでは、最初のイネーブルメント以降のAWS PrivateLink の問題のトラブルシューティングには対応しません。
- お客様は、自社の VPC の管理に関連するコストを負担します。
- 以下の理由により、HTTPS プロトコル（ポート 443）を使用して、Azure プライベートリンク経由でクラウドインフラストラクチャ上のAdobe Commerceに接続することはできません [Fastly origin クローキング](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/faq/fastly-origin-cloaking-enablement-faq.html). この制限は、AWS PrivateLink には適用されません。
- PrivateDNS は使用できません。

## PrivateLink 接続タイプ

次のネットワーク図に示すように、2 つの PrivateLink 接続タイプを使用して、ストアとクラウド環境の外部でホストされる外部システムとの間の安全な通信を確立できます。

![PrivateLink ネットワーク図](../../assets/privatelink-architecture-diagram.png)

クラウドインフラストラクチャ環境のAdobe Commerceに最適な PrivateLink 接続タイプを次の中から 1 つ選びます。

- **単方向プライベートリンク** – この設定を選択すると、クラウドインフラストラクチャストア上のAdobe Commerceから安全にデータを取得できます。
- **双方向 PrivateLink** – この設定を選択すると、クラウドインフラストラクチャー上のAdobe Commerceの外部システムとの間でセキュアな接続を確立できます。 双方向オプションには、次の 2 つの接続が必要です。

   - お客様の VPC とAdobe VPC の間の接続
   - AdobeVPC とお客様 VPC の間の接続

>[!TIP]
>
>PrivateLink 接続タイプの選択に関するヘルプや、VPC のセットアップと管理に関するヘルプは、ネットワーク管理者またはクラウドプラットフォームプロバイダーにお問い合わせください。 詳しくは、Cloud Platform PrivateLink のドキュメントを参照してください。 [AWS PrivateLink](https://aws.amazon.com/privatelink/) または [Azure プライベートリンク](https://learn.microsoft.com/en-us/azure/private-link/).

## PrivateLink 有効化のリクエスト

>[!WARNING]
>
>PrivateLink の有効化には、最大で時間がかかる場合があります _5_ 営業日。 不完全な情報や不正確な情報を提供すると、プロセスが遅れる場合があります。

### 前提条件

![チェック](../../assets/fix.svg) クラウドインフラストラクチャインスタンス上のAdobe Commerceと同じリージョンにあるクラウドアカウント（AWSまたは Azure）。

![チェック](../../assets/fix.svg) PrivateLink 経由で接続するサービスをホストする、お客様の環境内の VPC。 VPC 設定のヘルプについては、AWSまたは Azure のドキュメントを参照するか、ネットワーク管理者にお問い合わせください。

![チェック](../../assets/fix.svg) 双方向の PrivateLink 接続の場合、PrivateLink 有効化をリクエストする前に、アプリケーションまたはサービスのエンドポイントサービス設定を作成し、VPC 環境にエンドポイントを作成する必要があります。 参照： [双方向の PrivateLink 接続用の設定](#set-up-for-bidirectional-privatelink-connections).

PrivateLink の有効化に必要な次のデータを収集します。

- **顧客クラウドアカウント番号** （AWSまたは Azure） – クラウドインフラストラクチャインスタンス上のAdobe Commerceと同じリージョンにある必要があります
- **クラウド地域** – 検証目的でアカウントがホストされているクラウド地域を指定します
- **サービスおよび通信ポート**— VPC 間のサービス通信を有効にするには、Adobeがポートを開く必要があります。たとえば、SQL ポート 3306、SFTP ポート 2222 などです。
- **プロジェクト ID**—Adobe Commerce on cloud infrastructure Pro プロジェクト ID を指定します。 以下を使用して、プロジェクト ID およびその他のプロジェクト情報を取得できます [クラウド CLI](../dev-tools/cloud-cli-overview.md) コマンド： `magento-cloud project:info`
- **接続タイプ** – 接続タイプに単方向または双方向を指定します。
- **エンドポイントサービス** – 双方向の PrivateLink 接続の場合、Adobeが接続する必要がある VPC エンドポイントサービスの DNS URL を指定します。次に例を示します。 `com.amazonaws.vpce.<cloud-region>.vpce-svc-<service-id>`
- **Endpoint service アクセスが許可されました** – 外部サービスに接続するには、エンドポイントサービスが次のAWS アカウントプリンシパルにアクセスできるようにします。 `arn:aws:iam::402592597372:root`

  >[!WARNING]
  >
  >エンドポイントサービスへのアクセス権が提供されていない場合、VPC のサービスへの双方向の PrivateLink 接続は次のようになります **ではない** を追加しました。これにより、セットアップが遅れます。

#### Azure プライベートリンクの有効化に固有のその他の前提条件

- クラスター ID を指定します。SSH を使用して、リモートにログインし、次のコマンドを使用します。 `cat /etc/platform_cluster`
- 外部サービスをAdobe Commerce Pro クラスターに接続するには、次のものが必要です。

   - 新しい外部プライベートエンドポイントに公開する Pro クラスター上のポートのリスト
   - プライベートエンドポイント接続の Azure サブスクリプション ID のリスト

- Adobe Commerce Pro クラスターを外部サービスに接続するには、次が必要です。

   - ターゲットサービスのリソース ID のリスト。 外部プライベートリンクサービス ID は次のようになります。

  ```text
  /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/privateLinkServices/{svcNameID}
  ```

### イネーブルメントワークフロー

次のワークフローでは、クラウドインフラストラクチャー上のAdobe Commerceと PrivateLink 統合のイネーブルメントプロセスの概要を説明します。

1. **顧客** 件名行で PrivateLink を有効にするようにリクエストするサポートチケットを送信します `PrivateLink support for <company>`. 次を含める [イネーブルメントに必要なデータ](#prerequisites) チケットの中に。 Adobeは、サポートチケットを使用して、イネーブルメントプロセス中のコミュニケーションを調整します。

1. **Adobe** カスタマーアカウントに対して、AdobeVPC のエンドポイントサービスへのアクセスを有効にします。

   - Adobeエンドポイントサービス設定を更新して、カスタマーAWSまたは Azure アカウントから開始されたリクエストを受け入れます。
   - サポートチケットを更新して、接続先のAdobe VPC エンドポイントのサービス名（など）を指定します。 `com.amazonaws.vpce.<cloud-region>.vpce-svc-<service-id>`.

1. **顧客** は、Adobeエンドポイントサービスをクラウドアカウント（AWSまたは Azure）に追加します。これにより、Adobeへの接続リクエストがトリガーされます。 手順については、Cloud Platform のドキュメントを参照してください。

   - AWSについては、を参照してください。 [インターフェイスエンドポイント接続リクエストの承認と却下].
   - Azure については、を参照してください。 [接続リクエストの管理].

1. **Adobe** 接続リクエストを承認します。

1. 接続リクエストの承認後、 **顧客** [接続を検証します](#test-vpc-endpoint-service-connection) VPC とAdobeVPC の間。

1. 双方向接続を有効にするための追加手順：

   - **Adobe** は、Adobeアカウントプリンシパル（AWSまたは Azure アカウントのルートユーザー）を提供し、カスタマー VPC エンドポイントサービスへのアクセスをリクエストします。
   - **顧客** お客様の VPC のエンドポイントサービスへのAdobeアクセスを有効にします。 これは、Adobeアカウントプリンシパルがにアクセスできることを前提としています `arn:aws:iam::402592597372:root`（で前述） **Endpoint service アクセスが許可されました** 前提条件。

      - Adobeアカウントから開始されたリクエストを受け入れるように、カスタマーエンドポイントサービス設定を更新します。 手順については、Cloud Platform のドキュメントを参照してください。

         - AWSについては、を参照してください。 [エンドポイントサービスの権限の追加と削除].
         - Azure については、を参照してください。 [プライベートエンドポイント接続の管理]

      - Adobeに、お客様の VPC のエンドポイントサービス名を入力します。

   - **Adobe** カスタマーエンドポイントサービスをAdobeプラットフォームアカウント（AWSまたは Azure）に追加します。これにより、お客様の VPC への接続リクエストがトリガーされます。
   - **顧客** Adobeからの接続要求を承認して設定を完了します。
   - **顧客** [接続を検証します](#test-vpc-endpoint-service-connection) AdobeVPC から。

## VPC エンドポイントサービス接続のテスト

Telnet アプリケーションを使用して、VPC エンドポイント サービスへの接続をテストできます。

**VPC エンドポイントサービスへの接続をテストするには**:

1. プロジェクトのルートディレクトリから **チェックアウト** privateLink エンドポイントサービスにアクセスするように設定されたステージング環境または実稼動環境。

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

   成功した応答のサンプル：

   ```terminal
   * Rebuilt URL to: telnet://vpce-007ffnb9qkcnjgult-yfhmywqh.vpce-svc-083cqvm2ta3rxqat5v.us-east-1.vpce.amazonaws.com:80
   * Connected to vpce-0088d56482571241d-yfhmywqh.vpce-svc-083cqvm2ta3rxqat5v.us-east-1.vpce. amazonaws.com (191.210.82.246) port 80 (#0)
   ```

   失敗した応答のサンプル：

   ```terminal
   Failed to connect to vpce-007ffnb9qkcnjgult-yfhmywqh.vpce-svc-083cqvm2ta3rxqat5v.ap-southeast-1.vpce.amazonaws.com port 80: Connection timed out
   * Closing connection 0
   ```

1. サービスが VM をリッスンしていることを確認します。

   ```bash
   netstat -na | grep <port>
   ```

1. パッケージフローを確認します。

   ```bash
   tcpdump -i <ethernet-interface> -tt -nn port <destination-port> and host <source-host>
   ```

   次の内部設定をチェックして、設定が有効であることを確認します。

   - エンドポイントとエンドポイントサービスの設定
   - ネットワーク ロード バランサー（NLB）の設定
   - NLB 内のターゲット グループは正常であることを確認します
   - 各 VM の netcat/curl エンドポイント URL （上記を参照）

   接続に関する問題のトラブルシューティングについて詳しくは、次の記事を参照してください。

   - [AWS：エンドポイントサービス接続のトラブルシューティング]
   - [Amazon:Azure プライベートリンク接続の問題のトラブルシューティング]

   エラーを解決できない場合は、Adobe Commerce サポートチケットを更新して、接続の確立に関するヘルプをリクエストします。

## PrivateLink 設定の変更

[Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) 既存の PrivateLink 設定を変更するには、次の手順に従います。 例えば、次のような変更をリクエストできます。

- Cloud infrastructure Pro 実稼動環境またはステージング環境のAdobe Commerceから PrivateLink 接続を削除します。
- Adobeエンドポイントサービスにアクセスするための顧客の Cloud Platform アカウント番号を変更します。
- お客様の VPC 環境で利用可能な他のエンドポイントサービスへの PrivateLink 接続をAdobeVPC から追加または削除します。

## 双方向の PrivateLink 接続用の設定

双方向 PrivateLink 接続をサポートするには、お客様の VPC に次のリソースが必要です。

- ネットワーク ロード バランサー（NLB）
- 顧客 VPC からのアプリケーションまたはサービスへのアクセスを可能にするエンドポイントサービス設定
- An [インターフェイス エンドポイント] （AWS）または [プライベートエンドポイント] （Azure）:Adobeが VPC でホストされているエンドポイントサービスに接続できます

これらのリソースが顧客 VPC で使用できない場合は、Cloud Platform アカウントにサインインして設定を追加する必要があります。

- Amazon VPC コンソール –  `https://console.aws.amazon.com/vpc/`
- Azure portal- `https://portal.azure.com`

PrivateLink の設定手順については、Cloud Platform のドキュメントを参照してください。

- **AWS PrivateLink のドキュメント**
   - [ネットワークロードバランサーの作成]
   - [エンドポイントサービス設定の作成]
   - [インターフェイスエンドポイントの作成]
   - [インターフェイスエンドポイントのライフサイクル]

- **Azure PrivateLink のドキュメント**
   - [ロードバランサーの作成]
   - [Azure プライベートリンクのワークフロー]

<!--Link definitions-->

[インターフェイスエンドポイント接続リクエストの承認と却下]: https://docs.aws.amazon.com/vpc/latest/userguide/accept-reject-endpoint-requests.html
[エンドポイントサービスの権限の追加と削除]: https://docs.aws.amazon.com/vpc/latest/userguide/add-endpoint-service-permissions.html
[Amazon:Azure プライベートリンク接続の問題のトラブルシューティング]: https://docs.microsoft.com/en-us/azure/private-link/troubleshoot-private-link-connectivity
[AWS：エンドポイントサービス接続のトラブルシューティング]: https://aws.amazon.com/premiumsupport/knowledge-center/connect-endpoint-service-vpc/
[Azure プライベートリンクのワークフロー]: https://docs.microsoft.com/en-us/azure/private-link/private-link-service-overview#workflow
[ロードバランサーの作成]: https://docs.microsoft.com/en-us/azure/load-balancer/quickstart-load-balancer-standard-public-portal
[ネットワークロードバランサーの作成]: https://docs.aws.amazon.com/elasticloadbalancing/latest/network/create-network-load-balancer.html
[エンドポイントサービス設定の作成]: https://docs.aws.amazon.com/vpc/latest/userguide/create-endpoint-service.html
[インターフェイスエンドポイントの作成]: https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#create-interface-endpoint
[interface endpoint lifecycle]: https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#vpce-interface-lifecycle
[インターフェイス エンドポイント]: https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html
[プライベートエンドポイント接続の管理]: https://docs.microsoft.com/en-us/azure/private-link/manage-private-endpoint
[接続リクエストの管理]: https://docs.microsoft.com/en-us/azure/private-link/private-link-service-overview#manage-your-connection-requests
[プライベートエンドポイント]: https://docs.microsoft.com/en-us/azure/private-link/private-endpoint-overview
