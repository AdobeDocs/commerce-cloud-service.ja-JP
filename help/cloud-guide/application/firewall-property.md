---
title: Firewall プロパティ
description: コマースアプリケーションの構成ファイルでファイアウォールプロパティを構成する方法の例を参照してください。
feature: Cloud, Configuration, Security
exl-id: f169c008-c62a-41b7-a98d-cccd81c7291a
source-git-commit: 74d88560db3b65294673a1e1827f9cea098d707a
workflow-type: tm+mt
source-wordcount: '844'
ht-degree: 0%

---

# Firewall プロパティ

>[!IMPORTANT]
>
>スタータープロジェクトのみ

スタータープロジェクトの場合、 `firewall` プロパティは、 _送信_ ファイアウォールをアプリケーションに追加します。 このファイアウォールは、受信要求には影響しません。 どれを定義するか `tcp` 送信要求に対して _leave_ Adobe Commerceサイト。 これは、エグレスフィルタリングと呼ばれます。 アウトバウンドファイアウォールは、サイトを出口またはエスケープする機能をフィルタリングします。 何をエスケープできるかを制限すると、サーバーに強力なセキュリティツールが追加されます。

## デフォルトの制限ポリシー

ファイアウォールには、送信トラフィックを制御する 2 つのデフォルトポリシーが用意されています。 `allow` および `deny`. The `allow` ポリシー _許可_ デフォルトでは、すべてのアウトバウンドトラフィック。 また、 `deny` ポリシー _拒否_ デフォルトでは、すべてのアウトバウンドトラフィック。 ただし、ルールを追加すると、デフォルトのポリシーが上書きされ、ファイアウォールがブロックされます **すべて** ルールで許可されていない送信トラフィックです。

スタータープランの場合、デフォルトのポリシーは次のように設定されます。 `allow`. この設定により、エグレスフィルタールールを追加するまで、現在のすべての送信トラフィックのブロックが解除されたままになります。 デフォルトのポリシーは次のように設定できます。 `deny` リクエストに応じて

**デフォルトのポリシーを確認するには**:

```bash
magento-cloud p:curl --project PROJECT_ID /settings | grep -i outbound
```

ご要望に応じて `deny` ポリシーの場合、コマンドは、ポリシーを次のように設定して表示する必要があります。 `allow`:

```terminal
"outbound_restrictions_default_policy": "allow"
```

>[!NOTE]
>
>**キーの取り出し**：アウトバウンドルールを追加すると、そのルールに追加するドメイン、IP アドレス、またはポートを除くすべてのアウトバウンドトラフィックがブロックされます。 そのため、実稼動サイトにアウトバウンドリストを追加する前に、完全なアウトバウンドリストを定義し、テストしておくことが重要です。

## ファイアウォールオプション

次の設定例は、 `.magento.app.yaml` ファイルに `firewall` エグレスフィルターのルールを追加する際に使用できるオプションです。

```yaml
firewall:
    outbound:
        - # Common accessed domains
            domains:
                - newrelic.com
                - fastly.com
                - magento.com
                - magentocommerce.com
                - google.com
            ports:
                - 80
                - 443
            protocol: tcp # Can be omitted from rules.

        - # Adobe Stock integration
            domains:
                - account.adobe.com
                - stock.adobe.com
                - console.adobe.io
            ports:
                - 80
                - 443

        - # Payment services
            domains:
                - braintreepayments.com
                - paypal.com
            ports:
                - 80
                - 443

        - # Shipping services
            domains:
                - ups.com
                - usps.com
                - fedex.com
                - dhl.com
            ports:
                - 80
                - 443

        - # Vertex Integrated Address Cleansing
            domains:
                - mgcsconnect.vertexsmb.com
            ports:
                - 80
                - 443

        - # New Relic networks
            ips:
                - 162.247.240.0/22 # US region accounts
                - 185.221.84.0/22 # EU region accounts
            ports:
                - 443

        - # New Relic endpoints
            domains:
                - collector.newrelic.com, # US region accounts
                - collector.eu01.nr-data.net # EU region accounts
            ports:
                - 443

        - # Fastly IP ranges
            ips:
                - 23.235.32.0/20
                - 43.249.72.0/22
                - 103.244.50.0/24
                - 103.245.222.0/23
                - 103.245.224.0/24
                - 104.156.80.0/20
                - 146.75.0.0/16
                - 151.101.0.0/16
                - 157.52.64.0/18
                - 167.82.0.0/17
                - 167.82.128.0/20
                - 167.82.160.0/20
                - 167.82.224.0/20
                - 172.111.64.0/18
                - 185.31.16.0/22
                - 199.27.72.0/21
                - 199.232.0.0/16
            ports:
                - 80
                - 443
```

## エグレスフィルタールール

アウトバウンドファイアウォールの設定は、複数のルールで構成されています。 ルールは必要な数だけ定義できます。 ルールの要件は次のとおりです。

**各ルール：**

- ハイフン (`-`) をクリックします。 同じ行にコメントを追加すると、1 つのルールを次のルールと明確に区切るのに役立ちます。
- 次のオプションを少なくとも 1 つ定義する必要があります。 `domains`, `ips`または `ports`.
- を使用する必要があります `tcp` プロトコルを使用します。 これはすべてのルールのデフォルトのプロトコルなので、ルールから省略できます。
- 定義可能 `domains` または `ips`ですが、両方とも同じルールに含めることはできません。
- 次を含めることができます。 `yaml` コメント (`#`) や改行を使用して、許可されているドメイン、IP アドレス、ポートを整理します。

各ルールは、次のプロパティを使用します。

### `domains`

The `domains` オプションは、完全修飾ドメイン名 (FQDN) のリストを許可します。

ルールが `domains` しかし、 `ports`の場合、ファイアウォールは任意のポートでのドメイン要求を許可します。

### `ips`

The `ips` オプションを使用すると、CIDR 表記での IP アドレスのリストを許可します。 単一の IP アドレスまたは IP アドレスの範囲を指定できます。

単一の IP アドレスを指定するには、 `/32` IP アドレスの末尾に対する CIDR プレフィックス：

```terminal
172.217.11.174/32  # google.com
```

IP アドレスの範囲を指定するには、 [IP 範囲から CIDR](https://ipaddressguide.com/cidr) 計算ツール

ルールが `ips` しかし、 `ports`に設定すると、ファイアウォールは任意のポートで IP リクエストを許可します。

### `ports`

The `ports` オプションは、1 ～ 65535のポートのリストを許可します。 この例のほとんどのルールでは、ポート `80` および `443` は、HTTP リクエストと HTTPS リクエストの両方を許可します。 ただし、New Relicの場合、このルールはポート上のドメインと IP アドレスへのアクセスのみを許可します `443`( に関するNew Relicドキュメントで推奨 ) [ネットワークトラフィック](https://docs.newrelic.com/docs/new-relic-solutions/get-started/networks/#agents).

ルールが `ports`を使用すると、ファイアウォールは定義されたポートのすべてのドメインと IP アドレスにアクセスできます。

>[!NOTE]
>
>ポート `25`:E メールを送信する SMTP ポートは、例外なく常にブロックされます。

### `protocol`

既に述べたように、TCP はデフォルトであり、ルールで使用できる唯一のプロトコルです。 UDP とそのポートは許可されていません。 このため、 `protocol` 」オプションを使用します。 それでも含めたい場合は、値をに設定する必要があります。 `tcp`の最初のルールに示すように、です。

## 許可するドメイン名の検索

エグレスフィルタリングルールに含めるドメインを識別するには、次のコマンドを使用してサーバーの `dns.log` ファイルを開き、サイトがログに記録したすべての DNS 要求のリストを表示します。

```shell
awk '($5 ~/query/)' /var/log/dns.log | awk '{print $6}' | sort | uniq -c | sort -rn
```

また、このコマンドは、エグレスフィルタリングルールによってブロックされた DNS 要求も表示します。 出力には、どのドメインがブロックされたかは表示されず、リクエストのみがおこなわれました。 この出力には、IP アドレスを使用して行われたリクエストは表示されません。

```terminal
Example output:

97 magento.com
93 magentocommerce.com
88 google.com
70 metadata.google.internal.0
70 metadata.google.internal
65 newrelic.com
56 fastly.com
17 mcprod-0vunku5xn24ip.ap-4.magentosite.cloud
6 advancedreporting.rjmetrics.com
```

IP アドレスとは異なり、ドメインは、通常、エグレスフィルタリングでより具体的で安全です。 例えば、CDN を使用するサービスの IP アドレスを追加すると、CDN の IP アドレスを許可することになります。CDN は、数百や数千の他のドメインで使用できます。 1 つの IP アドレスを使用すると、数千台の他のサーバーへのアウトバウンドアクセスを許可できます。

## エグレスフィルタールールのテスト

サイトで必要なドメインおよび IP アドレスのアクセス規則を収集および設定したら、プッシュしてテストする番です。

エグレスフィルタールールをテストするには、次の手順に従います。

1. のシェルスクリプトを作成する `curl` コマンドを使用して、ルール内のドメインと IP アドレスにアクセスできます。 ブロックする必要のあるドメインおよび IP アドレスへのアクセスをテストするコマンドを含めます。

1. の設定 `post_deploy` フックで `.magento.app.yaml` ファイルを使用してスクリプトを実行します。

1. プッシュする `firewall` 設定およびテストスクリプトを `integration` 分岐。

1. を調べます。 `post_deploy` の出力 `curl` コマンド。

1. を調整する `firewall` ルール、更新 `curl` スクリプト、コミット、プッシュ、繰り返し。

### `curl` スクリプトの例

```shell
# curl-tests-for-egress-filtering.sh

# Use the -v option to display connection details

# Check domain access
curl -v newrelic.com
curl -v fastly.com
curl -v magento.com
curl -v magentocommerce.com
curl -v google.com
curl -v account.adobe.com
curl -v stock.adobe.com
curl -v console.adobe.io
curl -v braintreepayments.com
curl -v paypal.com
curl -v ups.com
curl -v usps.com
curl -v fedex.com
curl -v dhl.com
curl -v devdocs.magento.com

# Check domain denials
curl -v amazon.com
curl -v facebook.com
curl -v twitter.com

# IP address access
...

# IP address denials
...
```

### `post_deploy` 例

```yaml
hooks:
    build: |
        set -e
        php ./vendor/bin/ece-tools run scenario/build/generate.xml
        php ./vendor/bin/ece-tools run scenario/build/transfer.xml
    deploy: "php ./vendor/bin/ece-tools run scenario/deploy.xml\n"
    post_deploy: |
        set -e
        php ./vendor/bin/ece-tools run scenario/post-deploy.xml
        echo "[$(date)] post-deploy hook end"
        ./curl-tests-for-egress-filtering.sh
        echo "[$(date)] curl finished"
```
