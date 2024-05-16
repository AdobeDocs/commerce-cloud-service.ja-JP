---
title: ファイアウォールプロパティ
description: Commerce アプリケーション設定ファイルでファイアウォールプロパティを設定する方法の例を参照してください。
feature: Cloud, Configuration, Security
exl-id: f169c008-c62a-41b7-a98d-cccd81c7291a
source-git-commit: 74d88560db3b65294673a1e1827f9cea098d707a
workflow-type: tm+mt
source-wordcount: '844'
ht-degree: 0%

---

# ファイアウォールプロパティ

>[!IMPORTANT]
>
>スタータープロジェクトのみ

スタータープロジェクトの場合、 `firewall` プロパティはを追加します _アウトバウンド_ アプリケーションに対するファイアウォール。 このファイアウォールは、受信リクエストには影響しません。 次のいずれかを定義します `tcp` アウトバウンドリクエストで実行できる操作 _移動_ Adobe Commerce サイト。 これは、エグレスフィルタリングと呼ばれます。 アウトバウンドファイアウォールは、サイトから出たり、サイトから出たりできるものをフィルタリングします。 エスケープできるものを制限すると、サーバーに強力なセキュリティツールが追加されます。

## デフォルトの制限ポリシー

ファイアウォールには、送信トラフィックを制御するための 2 つのデフォルトのポリシーが用意されています。 `allow` および `deny`. この `allow` 保険証書 _を許可_ デフォルトでは、すべての送信トラフィックです。 および `deny` 保険証書 _拒否_ デフォルトでは、すべての送信トラフィックです。 ただし、ルールを追加すると、デフォルトポリシーが上書きされ、ファイアウォールがブロックされます **all** ルールによって許可されていない送信トラフィック。

スタータープランの場合、デフォルトポリシーはに設定されます `allow`. この設定により、出力フィルタリングルールを追加するまで、現在のすべての送信トラフィックがブロック解除されたままになります。 デフォルトポリシーはに設定できます。 `deny` ご要望により。

**デフォルトポリシーを確認するには：**:

```bash
magento-cloud p:curl --project PROJECT_ID /settings | grep -i outbound
```

ご要望がない限り `deny` ポリシーに関しては、コマンドはポリシーがに設定されていることを表示します。 `allow`:

```terminal
"outbound_restrictions_default_policy": "allow"
```

>[!NOTE]
>
>**重要な留意点**：送信規則を追加すると、その規則に追加したドメイン、IP アドレス、ポートを除くすべての送信トラフィックがブロックされます。 そのため、実稼動サイトに追加する前に、完全なアウトバウンドリストを定義しテストしておくことが重要です。

## ファイアウォールオプション

での設定例は次のようになります。 `.magento.app.yaml` ファイルには、すべての `firewall` エグレスフィルタリングのルールを追加するために使用できるオプションです。

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

## 出力フィルタールール

送信ファイアウォール設定は、ルールで構成されています。 必要な数のルールを定義できます。 ルールの要件は次のとおりです。

**各ルール：**

- 先頭をハイフン （`-`）に設定します。 同じ行にコメントを追加すると、ドキュメントを作成して、ルールを次のルールと視覚的に区別するのに役立ちます。
- 次のオプションの少なくとも 1 つを定義する必要があります。 `domains`, `ips`、または `ports`.
- を使用する必要があります。 `tcp` プロトコル。 これは、すべてのルールのデフォルトのプロトコルなので、ルールから省略できます。
- 定義可能 `domains` または `ips`が、両方とも同じルールに含まれるわけではありません。
- 次を含めることができます `yaml` コメント （`#`）と改行を使用して、許可されるドメイン、IP アドレス、ポートを整理します。

各ルールでは、次のプロパティを使用します。

### `domains`

この `domains` オプションを使用すると、完全修飾ドメイン名（FQDN）の一覧が許可されます。

ルールに次の定義がある場合： `domains` ただし、そうではありません `ports`の場合、ファイアウォールでは、任意のポートでのドメインリクエストが許可されます。

### `ips`

この `ips` オプションを使用すると、CIDR 表記で IP アドレスのリストが許可されます。 単一の IP アドレスまたは IP アドレスの範囲を指定できます。

単一の IP アドレスを指定するには、 `/32` IP アドレスの末尾に付加する CIDR プレフィックス：

```terminal
172.217.11.174/32  # google.com
```

IP アドレスの範囲を指定するには、 [CIDR への IP 範囲](https://ipaddressguide.com/cidr) 計算ツール。

ルールに次の定義がある場合： `ips` ただし、そうではありません `ports`の場合、ファイアウォールでは、任意のポートでの IP 要求が許可されます。

### `ports`

この `ports` オプションを指定すると、1 から 65535 までのポートのリストが表示されます。 この例のほとんどのルールでは、ports `80` および `443` http リクエストと HTTPS リクエストの両方を許可します。 ただし、New Relicの場合、ルールでは、ポート上のドメインと IP アドレスへのアクセスのみが許可されます `443`のNew Relic ドキュメントの推奨に従って [ネットワークトラフィック](https://docs.newrelic.com/docs/new-relic-solutions/get-started/networks/#agents).

ルールが定義しているだけの場合 `ports`ファイアウォールでは、定義されたポートのすべてのドメインと IP アドレスへのアクセスが許可されます。

>[!NOTE]
>
>ポート `25`の場合、メールを送信する SMTP ポートは常にブロックされます（例外はありません）。

### `protocol`

前述のように、TCP はデフォルトであり、ルールに使用できる唯一のプロトコルです。 UDP とそのポートは許可されていません。 この理由から、は省略できます `protocol` すべてのルールからのオプション。 それでも含める場合は、値をに設定する必要があります。 `tcp`例の最初のルールで示されているように。

## 許可するドメイン名の検索

出力フィルタリングルールに含めるドメインを特定しやすくするには、次のコマンドを使用してサーバーのを解析します。 `dns.log` をファイルに保存し、サイトがログに記録したすべての DNS リクエストのリストを表示します。

```shell
awk '($5 ~/query/)' /var/log/dns.log | awk '{print $6}' | sort | uniq -c | sort -rn
```

このコマンドは、送信フィルタリングルールによってブロックされた DNS リクエストも表示します。 出力には、ブロックされたドメインは表示されず、リクエストが行われただけです。 IP アドレスを使用して行われたリクエストは出力に表示されません。

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

IP アドレスとは異なり、ドメインは通常、エグレスフィルタリングに対してより具体的で安全です。 例えば、CDN を使用するサービスの IP アドレスを追加する場合、CDN の IP アドレスを許可します。これは、数百または数千の他のドメインで使用できます。 1 つの IP アドレスで、他の何千ものサーバーへのアウトバウンドアクセスを許可できます。

## 出力フィルタールールのテスト

サイトで必要なドメインおよび IP アドレスのアクセスルールを収集して設定したら、プッシュしてテストします。

出力フィルタリングルールをテストするには：

1. のシェルスクリプトを作成します。 `curl` ルールのドメインと IP アドレスにアクセスするためのコマンド。 ブロックする必要があるドメインおよび IP アドレスへのアクセスをテストするコマンドを含めます。

1. の設定 `post_deploy` をフックする `.magento.app.yaml` スクリプトを実行するファイル。

1. をプッシュ `firewall` 設定およびテストスクリプト `integration` 分岐。

1. を調べる `post_deploy` の出力 `curl` コマンド。

1. を絞り込む `firewall` ルール、を更新する `curl` スクリプト、コミット、プッシュおよび繰り返し。

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
