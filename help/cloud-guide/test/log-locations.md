---
title: ログの表示と管理
description: クラウドインフラストラクチャで使用できるログファイルのタイプと、それらのログファイルの場所について説明します。
last-substantial-update: 2023-05-23T00:00:00Z
exl-id: d7f63dab-23bf-4b95-b58c-3ef9b46979d4
source-git-commit: 86af69eed16e8fe464de93bd0f33cfbfd4ed8f49
workflow-type: tm+mt
source-wordcount: '1056'
ht-degree: 0%

---

# ログの表示と管理

クラウドインフラストラクチャプロジェクト上のAdobe Commerceのログは、に関連する問題のトラブルシューティングに役立ちます [フックの作成とデプロイ](../application/hooks-property.md)、クラウドサービスおよびAdobe Commerce アプリケーションです。

ログはファイルシステムから表示できます。 [!DNL Cloud Console]、および `magento-cloud` CLI。

- **ファイルシステム** – この `/var/log` システムディレクトリには、すべての環境のログが含まれています。 この `var/log/` ディレクトリには、特定の環境に固有のアプリ固有のログが含まれています。 これらのディレクトリは、クラスター内のノード間で共有されません。 実稼動環境およびステージング環境では、各ノードのログを確認する必要があります。

- **[!DNL Cloud Console]** – ビルド、デプロイ、デプロイ後のログ情報は、環境で確認できます _メッセージ_ リスト。

- **クラウド CLI**— ローカル環境のログを表示するには、 `magento-cloud log` を使用したコマンドまたはリモート環境のログ `magento-cloud ssh` コマンド。

## ログの場所

システムログは、次の場所に保存されています。

- 統合： `/var/log/<log-name>.log`
- Pro ステージング： `/var/log/platform/<project-ID>_stg/<log-name>.log`
- 試作品： `/var/log/platform/<project-ID>/<log-name>.log`

次の値 `<project-ID>` プロジェクトと、環境がステージング環境か実稼動環境かによって異なります。 例えば、プロジェクト ID がの場合 `yw1unoukjcawe`の場合、ステージング環境ユーザーはです `yw1unoukjcawe_stg` 実稼動環境のユーザーは `yw1unoukjcawe`.

この例では、デプロイログは次のようになります。 `/var/log/platform/yw1unoukjcawe_stg/deploy.log`

### リモート環境ログの表示

ほとんどのログには、リモート環境で発生するイベントが含まれます。 Pro の場合、複数のノードがあり、各ノードには一意のログがあります。 すべてのホストのリストを表示するには、以下を使用します。

```bash
magento-cloud ssh -p <project-ID> -e <environment-ID> --all
```

応答の例：

```terminal
1.ent-project-environment-id@ssh.region.magento.cloud
2.ent-project-environment-id@ssh.region.magento.cloud
3.ent-project-environment-id@ssh.region.magento.cloud
```

**リモート環境ログの一覧を表示するには**:

```bash
magento-cloud ssh -e <environment-ID> "ls var/log"
```

Pro の例：

```bash
ssh 1.ent-project-environment-id@ssh.region.magento.cloud "ls var/log | grep error"
```

**リモート ログを表示するには**:

```bash
magento-cloud ssh -e <environment-ID> "cat var/log/cron.log"
```

Pro の例：

```bash
ssh 1.ent-project-environment-id@ssh.region.magento.cloud "cat var/log/cron.log"
```

>[!TIP]
>
>ステージング環境および実稼動環境では、固定ファイル名のログファイルに対して、自動ログローテーション、圧縮、削除が有効になります。 各ログ ファイル タイプには、回転パターンと有効期間があります。 スターター環境にはログローテーションがありません。 環境のログのローテーションと圧縮ログの寿命の詳細については、次を参照してください。 `/etc/logrotate.conf` および `/etc/logrotate.d/<various>`. ログのローテーションは、Pro 統合環境では設定できません。 Pro 統合の場合、カスタムソリューション/スクリプトを実装し、 [cron の設定](../application/crons-property.md) 必要に応じてスクリプトを実行します。

## ログの作成とデプロイ

環境に変更をプッシュした後、の各フックからログを確認できます。 `var/log/cloud.log` ファイル。 ログには、各フックの開始メッセージと停止メッセージが含まれます。 次の例では、メッセージは「」です`Starting post-deploy.`「」と「」に対して検査する値`Post-deploy is complete.`“

特定のデプロイメントのログエントリのタイムスタンプを確認し、ログを確認および特定します。 次に、トラブルシューティングに使用できるログ出力の圧縮例を示します。

```terminal
Re-deploying environment project-integration-ID
  Executing post deploy hook for service `mymagento`
    [2019-01-03 19:44:11] NOTICE: Starting post-deploy.
    [2019-01-03 19:44:11] INFO: Validating configuration
    [2019-01-03 19:44:11] INFO: End of validation
    [2019-01-03 19:44:11] INFO: Enable cron
    [2019-01-03 19:44:11] INFO: Create backup of important files.
    [2019-01-03 19:44:11] INFO: Backup /app/app/etc/env.php.bak for /app/app/etc/env.php was created.
    [2019-01-03 19:44:11] INFO: Backup /app/app/etc/config.php.bak for /app/app/etc/config.php was created.
    [2019-01-03 19:44:11] INFO: php ./bin/magento cache:flush --ansi --no-interaction
    [2019-01-03 19:44:32] INFO: Warming up failed: http://integration-id-project.us.magentosite.cloud/
    [2019-01-03 19:44:32] NOTICE: Post-deploy is complete.
```

>[!TIP]
>
>クラウド環境を設定する際に、次を設定できます [ログベースのSlackとメール通知](../environment/set-up-notifications.md) ビルドおよびデプロイアクションの場合。

次のログは、すべてのクラウドプロジェクトで共通の場所を持ちます。

- **デプロイメントログ**: `var/log/cloud.log`
- **前回のデプロイメントエラーログ**: `var/log/cloud.error.log`
- **デバッグログ**: `var/log/debug.log`
- **例外ログ**: `var/log/exception.log`
- **システムログ**: `var/log/system.log`
- **サポートログ**: `var/log/support_report.log`
- **報告書**: `var/report/`

ただし、 `cloud.log` ファイルには、デプロイメントプロセスの各ステージからのフィードバックが含まれています。デプロイメントフックで作成されたログは、各環境に固有のログです。 環境固有のデプロイログは、次のディレクトリにあります。

- **スターターと Pro の統合**: `/var/log/deploy.log`
- **Pro ステージング**: `/var/log/platform/<project-ID>_stg/deploy.log`
- **実稼動環境に対応**: `/var/log/platform/<project-ID>/deploy.log`

### ログをデプロイ

各デプロイメントのログは、特定のに連結されます `deploy.log` ファイル。 次の例では、ターミナル内の現在の環境のデプロイログを出力します。

```bash
magento-cloud log -e <environment-ID> deploy
```

応答の例：

```terminal
Reading log file projectID-branchname-ID--mymagento@ssh.zone.magento.cloud:/var/log/'deploy.log'

[2023-04-24 18:58:03.080678] Launching command 'b'php ./vendor/bin/ece-tools run scenario/deploy.xml\n''.

[2023-04-24T18:58:04.129888+00:00] INFO: Starting scenario(s): scenario/deploy.xml (magento/ece-tools version: 2002.1.14, magento/magento2-base version: 2.4.6)
[2023-04-24T18:58:04.364714+00:00] NOTICE: Starting pre-deploy.
...
```

{{scd-timing-warning}}

### エラーログ

デプロイメントプロセス中に生成されたエラーメッセージと警告メッセージは、両方に書き込まれます `var/log/cloud.log` および `var/log/cloud.error.log` ファイル。 Cloud エラーログファイルには、最新のデプロイメントのエラーと警告のみが含まれます。 空のファイルは、エラーのないデプロイメントが成功したことを示します。

ログファイルを表示するには、 [Cloud CLI SSH](#view-remote-environment-logs)または、ECE-Tools を使用して、エラーと提案を表示できます。

```bash
magento-cloud ssh -e <environment-ID> "./vendor/bin/ece-tools error:show"
```

応答の例：

```terminal
errorCode: 1001
stage: build
step: validate-config
suggestion: Please run the following commands:
1. bin/magento module:enable --all
2. git add -f app/etc/config.php
3. git commit -m 'Adding config.php'
4. git push
title: File app/etc/config.php does not exist
type: warning
---------------

errorCode: 1006
stage: build
step: validate-config
suggestion: Your application does not have the "post_deploy" hook enabled.
  In order to minimize downtime, add the following to ".magento.app.yaml":
  hooks:
      post_deploy: |
          php ./vendor/bin/ece-tools run scenario/post-deploy.xml
title: The configured state is not ideal
type: warning
```

ほとんどのエラーメッセージには、説明と推奨されるアクションが含まれています。 の使用 [ECE ツールのエラーメッセージ参照](../dev-tools/error-reference.md) 詳しいガイダンスを得るためにエラーコードを検索します。 詳しいガイダンスについては、 [Adobe Commerce導入のトラブルシューティング](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/deployment/magento-deployment-troubleshooter.html).

## アプリケーションログ

デプロイログと同様、アプリケーションログは環境ごとに一意です。

| ログファイル | スターターと Pro の統合 | 説明 |
| ------------------- | --------------------------- | ------------------------------------------------- |
| **ログをデプロイ** | `/var/log/deploy.log` | からのアクティビティ [デプロイフック](../application/hooks-property.md). |
| **デプロイ後のログ** | `/var/log/post_deploy.log` | からのアクティビティ [デプロイ後フック](../application/hooks-property.md). |
| **Cron ログ** | `/var/log/cron.log` | Cron ジョブからの出力。 |
| **Nginx アクセス ログ** | `/var/log/access.log` | Nginx の起動時に、ディレクトリの欠落や除外されたファイル タイプの HTTP エラーが発生します。 |
| **Nginx エラーログ** | `/var/log/error.log` | Nginx に関連する構成エラーのデバッグに役立つスタートアップ メッセージ。 |
| **PHP アクセスログ** | `/var/log/php.access.log` | PHP サービスへのリクエスト。 |
| **PHP FPM ログ** | `/var/log/app.log` | |

ステージング環境および実稼動環境の場合、デプロイ、ポストデプロイ、Cron ログは、クラスターの最初のノードでのみ使用できます。

| ログファイル | Pro ステージング | 実稼動環境に対応 |
| ------------------- | --------------------------------------------------- | ----------------------------------------------- |
| **ログをデプロイ** | 最初のノードのみ：<br>`/var/log/platform/<project-ID>_stg/deploy.log` | 最初のノードのみ：<br>`/var/log/platform/<project-ID>/deploy.log` |
| **デプロイ後のログ** | 最初のノードのみ：<br>`/var/log/platform/<project-ID>_stg/post_deploy.log` | 最初のノードのみ：<br>`/var/log/platform/<project-ID>/post_deploy.log` |
| **Cron ログ** | 最初のノードのみ：<br>`/var/log/platform/<project-ID>_stg/cron.log` | 最初のノードのみ：<br>`/var/log/platform/<project-ID>/cron.log` |
| **Nginx アクセス ログ** | `/var/log/platform/<project-ID>_stg/access.log` | `/var/log/platform/<project-ID>/access.log` |
| **Nginx エラーログ** | `/var/log/platform/<project-ID>_stg/error.log` | `/var/log/platform/<project-ID>/error.log` |
| **PHP アクセスログ** | `/var/log/platform/<project-ID>_stg/php.access.log` | `/var/log/platform/<project-ID>/php.access.log` |
| **PHP FPM ログ** | `/var/log/platform/<project-ID>_stg/php5-fpm.log` | `/var/log/platform/<project-ID>/php5-fpm.log` |

### アーカイブしたログファイル

アプリケーションログは 1 日に 1 回の頻度で圧縮およびアーカイブされ、1 年間保持されます。 圧縮ログには、に対応する一意の ID を使用して名前が付けられます `Number of Days Ago + 1`. 例えば、Pro 実稼動環境では、過去 21 日間の PHP アクセスログが次のように保存され、名前が付けられます。

```terminal
/var/log/platform/<project-ID>/php.access.log.22.gz
```

アーカイブされたログ・ファイルは、圧縮前に元のファイルがあったディレクトリに常に保存されます。

>[!NOTE]
>
>**デプロイ** および **デプロイ後** ログファイルはローテーションおよびアーカイブされません。 デプロイメント履歴全体がこれらのログファイルに書き込まれます。

## サービスログ

各サービスは個別のコンテナで実行されるので、サービスログは統合環境では使用できません。 クラウドインフラストラクチャー上のAdobe Commerceを使用すると、統合環境内の web サーバーコンテナにのみアクセスできます。 次のサービスログの場所は、実稼動環境およびステージング環境用です。

- **Redis ログ**: `/var/log/platform/<project-ID>_stg/redis-server-<project-ID>_stg.log`
- **Elasticsearchログ**: `/var/log/elasticsearch/elasticsearch.log`
- **Java ガベージコレクションログ**: `/var/log/elasticsearch/gc.log`
- **メールログ**: `/var/log/mail.log`
- **MySQL エラーログ**: `/var/log/mysql/mysql-error.log`
- **MySQL の低速ログ**: `/var/log/mysql/mysql-slow.log`
- **RabbitMQ ログ**: `/var/log/rabbitmq/rabbit@host1.log`

サービス・ログは、ログ・タイプに応じて異なる期間にわたってアーカイブおよび保存されます。 例えば、MySQL ログの有効期間が最短で、7 日後に削除されます。

>[!TIP]
>
>スケールされたアーキテクチャにおけるログファイルの場所は、ノードタイプによって異なります。 参照： [スケールされたアーキテクチャにおけるログの場所](../architecture/scaled-architecture.md#log-locations) トピック。

## 実稼動およびステージング用のログデータ

実稼動環境とステージング環境では、を使用します。 [New Relic ログ管理](../monitor/log-management.md) プロジェクトと統合して、クラウドインフラストラクチャプロジェクト上のAdobe Commerceに関連付けられたすべてのログから集計ログデータを管理します。

New Relic ログアプリケーションは、クラウドインフラストラクチャの実稼動環境とステージング環境でAdobe Commerceをトラブルシューティングおよび監視するための一元的なログ管理ダッシュボードを提供します。 また、このダッシュボードからは、Fastly CDN、画像の最適化、web アプリケーションファイアウォール（WAF）の各サービスのログデータにもアクセスできます。 参照： [New Relic サービス](../monitor/new-relic-service.md).
