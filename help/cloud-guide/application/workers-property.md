---
title: 作業者
description: でワーカープロパティを設定する方法を説明します [!DNL Commerce] アプリケーション設定ファイル。
feature: Cloud, Configuration
exl-id: d6816925-5912-45ca-8255-6c307e58542d
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '339'
ht-degree: 0%

---

# 労働者財産

ワーカーは、実行中の Nginx インスタンスを使用せずに、web インスタンスとは別に実行するように定義できます。ただし、ワーカーは、で使用されるのと同じネットワークストレージを使用します。 [!DNL Commerce] アプリケーション。 ルーターがワーカーに公開リクエストを送信できないので、ワーカーインスタンスに（Node.js または Go を使用して） web サーバーを設定する必要はありません。 これにより、ワーカーインスタンスがバックグラウンドタスクや、デプロイメントをブロックするリスクのあるタスクを継続的に実行する場合に最適になります。

## ワーカーの設定

ワーカーは、Pro ステージング環境および実稼動環境でのみ使用できます。 Pro 統合およびスターター環境は、 [CRON_CONSUMERS_RUNNER](../environment/variables-deploy.md#cron_consumers_runner) 変数。

ステージング環境または実稼動環境でワーカーを設定するには、 [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) また、次の情報も含めます。

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

この例では、という名前の単一のワーカーを定義します。 `queue`を実行します。リソース割り当てのレベルが小さい（サイズ S）場合は、 `php ./bin/magento` 起動時のコマンド。 作業者 `queue` 次に、各ノードでワーカープロセスとして実行されます。 コマンドが終了すると、自動的に再起動されます。

## コマンドと上書き

この `commands.start` キーは、ワーカーアプリケーションでコマンドを起動するために必要です。 任意の有効なシェルコマンドを使用できますが、アプリケーションの言語を使用するのが理想的です。 コマンドが `start` キーが終了すると、自動的に再起動します。

>[!IMPORTANT]
>
>この `deploy` および `post_deploy` フックと `crons` コマンドは web コンテナでのみ実行され、ワーカーインスタンスでは実行されません。

### 継承

の定義 `size`, `relationships`, `access`, `disk` および `mount`、および `variables` プロパティは、明示的に上書きされない限り、ワーカーに継承されます。

次のプロパティは、オーバーライドに最もよく使用されます [トップレベルの設定](properties.md):

- `size` – 単一のバックグラウンド・プロセスに割り当てるリソースを削減する
- `variables` – 異なる方法で実行するようアプリケーションに指示する

### タイミングとキュー

各ワーカーは別のワーカーの後ろにキューしますが、次の設定では、タイムスタンプで一貫した 2 秒の分離が生成されます。 `var/time.txt` ファイル。PHP コード内の 8 秒間のスリープに関係なく実行されます。

```yaml
workers:
    time1:
        commands:
            start: 'php -r "sleep(8); echo time() . PHP_EOL;" >> var/time.txt& sleep 2'
```
