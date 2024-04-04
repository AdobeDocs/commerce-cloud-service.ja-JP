---
title: 作業者
description: ワーカーのプロパティを設定する方法については、 [!DNL Commerce] アプリケーション設定ファイル。
feature: Cloud, Configuration
exl-id: d6816925-5912-45ca-8255-6c307e58542d
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '339'
ht-degree: 0%

---

# 作業者プロパティ

実行中の Nginx インスタンスを使用せずに、Web インスタンスとは独立して実行するワーカーを定義できます。ただし、ワーカーは、 [!DNL Commerce] アプリケーション。 ルータはワーカーに対してパブリック要求を送信できないので、（Node.js または Go を使用して）ワーカーインスタンス上に Web サーバーを設定する必要はありません。 これにより、ワーカーインスタンスは、バックグラウンドタスクに最適になるか、デプロイメントをブロックするリスクのあるタスクを継続的に実行します。

## ワーカーの設定

ワーカーは、Pro Staging 環境と実稼動環境でのみ使用できます。 Pro 統合環境とスターター環境では、 [CRON_CONSUMERS_RUNNER](../environment/variables-deploy.md#cron_consumers_runner) 変数を使用します。

Pro Staging または Production でワーカーを設定するには、次の手順に従います。 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) およびには、次の情報を含めます。

- プロジェクト ID
- 環境 ID
- 作業者名
- 開始コマンド

ワーカーごとに 1 つのプロセスを設定できます。 の基本的で一般的なワーカー設定 `.magento.app.yaml` ファイルは次のようになります。

```yaml
workers:
    queue:
        commands:
            start: |
                php ./bin/magento queue:consumers:start commerce.eventing.event.publish
```

この例では、という名前の 1 人の作業者を定義します。 `queue`（サイズ S）レベルのリソース割り当てが小さい場合に、 `php ./bin/magento` コマンドを使用して起動します。 労働者 `queue` 次に、各ノードでワーカープロセスとして実行します。 コマンドが終了すると、自動的に再起動されます。

## コマンドと上書き

The `commands.start` キーは、worker アプリケーションでコマンドを起動するために必要です。 任意の有効なシェルコマンドを使用できますが、アプリケーションの言語を使用するのが理想的です。 コマンドが `start` キーが終了すると、自動的に再起動します。

>[!IMPORTANT]
>
>The `deploy` および `post_deploy` フックと `crons` コマンドは、web コンテナ上でのみ実行され、ワーカーインスタンス内では実行されません。

### 継承

の定義 `size`, `relationships`, `access`, `disk` および `mount`、および `variables` プロパティは、明示的に上書きされない限り、ワーカーによって継承されます。

次のプロパティは、 [トップレベル設定](properties.md):

- `size` — 単一のバックグラウンドプロセスに割り当てるリソースの数を減らします。
- `variables` — アプリケーションが異なる方法で実行されるように指示します。

### タイミングとキュー

各ワーカーは別のワーカーの後ろにキューを置きますが、次の設定では、タイムスタンプの間隔が 2 秒間で一貫して `var/time.txt` ファイルは、PHP コード内の 8 秒のスリープに関係なく、次のようになります。

```yaml
workers:
    time1:
        commands:
            start: 'php -r "sleep(8); echo time() . PHP_EOL;" >> var/time.txt& sleep 2'
```
