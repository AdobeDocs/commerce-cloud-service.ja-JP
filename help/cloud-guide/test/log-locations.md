---
title: ログの表示と管理
description: クラウドインフラストラクチャで使用可能なログファイルのタイプと場所を理解します。
last-substantial-update: 2023-05-23T00:00:00Z
exl-id: d7f63dab-23bf-4b95-b58c-3ef9b46979d4
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '1024'
ht-degree: 0%

---

# ログの表示と管理

クラウドインフラストラクチャプロジェクト上のAdobe Commerceのログは、 [フックの作成とデプロイ](../application/hooks-property.md)、クラウドサービス、Adobe Commerceアプリケーションの 3 つのソリューションが含まれます。

ログは、ファイルシステムの [!DNL Cloud Console]、および `magento-cloud` CLI。

- **ファイルシステム**- `/var/log` システムディレクトリには、すべての環境のログが含まれます。 The `var/log/` ディレクトリには、特定の環境に固有のアプリ固有のログが含まれます。 これらのディレクトリは、クラスター内のノード間では共有されません。 Pro 実稼動環境およびステージング環境では、各ノードのログを確認する必要があります。

- **[!DNL Cloud Console]** — 環境内で、ビルド、デプロイ、デプロイ後のログ情報を確認できます。 _メッセージ_ リスト。

- **Cloud CLI** — ローカル環境ログを表示するには、 `magento-cloud log` を使用したコマンドまたはリモート環境ログ `magento-cloud ssh` コマンドを使用します。

## ログの場所

システムログは次の場所に保存されます。

- 統合： `/var/log/<log-name>.log`
- Pro Staging: `/var/log/platform/<project-ID>_stg/<log-name>.log`
- プロプロダクション： `/var/log/platform/<project-ID>/<log-name>.log`

の値 `<project-ID>` は、プロジェクトと、環境が「ステージング」か「実稼動」かによって異なります。 例えば、のプロジェクト ID がの場合、 `yw1unoukjcawe`、ステージング環境ユーザーは `yw1unoukjcawe_stg` 実稼動環境のユーザーは、 `yw1unoukjcawe`.

この例では、デプロイログは次のようになります。 `/var/log/platform/yw1unoukjcawe_stg/deploy.log`

### リモート環境ログを表示

ほとんどのログには、リモート環境で発生するイベントが含まれます。 Pro の場合、複数のノードがあり、各ノードに一意のログがあります。 すべてのホストのリストを表示するには、次の手順を実行します。

```bash
magento-cloud ssh -p <project-ID> -e <environment-ID> --all
```

レスポンスのサンプル：

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

**リモートログを表示するには**:

```bash
magento-cloud ssh -e <environment-ID> "cat var/log/cron.log"
```

Pro の例：

```bash
ssh 1.ent-project-environment-id@ssh.region.magento.cloud "cat var/log/cron.log"
```

>[!TIP]
>
>Pro 環境では、固定ファイル名を持つログファイルに対して、自動ログローテーション、圧縮、削除が有効になります。 各ログファイルタイプには、回転パターンと有効期間があります。 スターター環境にはローテーションがログに記録されません。 環境のログのローテーションと圧縮ログの存続期間について詳しくは、以下を参照してください。 `/etc/logrotate.conf` および `/etc/logrotate.d/<various>`

## ログの作成とデプロイ

環境に変更をプッシュした後、 `var/log/cloud.log` ファイル。 ログには、各フックの開始メッセージと停止メッセージが含まれます。 次の例では、メッセージは「`Starting post-deploy.`&quot;および&quot;`Post-deploy is complete.`&quot;

ログエントリのタイムスタンプを確認し、特定のデプロイメントのログを確認して探します。 次に、トラブルシューティングに使用できるログ出力の例をまとめます。

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
>クラウド環境を設定する際に、 [ログベースのSlackと電子メールの通知](../environment/set-up-notifications.md) ビルドおよびデプロイアクション用。

次のログには、すべてのクラウドプロジェクトで共通の場所があります。

- **デプロイメントログ**: `var/log/cloud.log`
- **前回のデプロイメントエラーログ**: `var/log/cloud.error.log`
- **デバッグログ**: `var/log/debug.log`
- **例外ログ**: `var/log/exception.log`
- **システムログ**: `var/log/system.log`
- **サポートログ**: `var/log/support_report.log`
- **レポート**: `var/report/`

ただし、 `cloud.log` ファイルには、デプロイメントプロセスの各段階からのフィードバックが含まれ、デプロイフックによって作成されたログは各環境に固有です。 環境固有のデプロイログは、次のディレクトリにあります。

- **スターターと Pro の統合**: `/var/log/deploy.log`
- **Pro Staging**: `/var/log/platform/<project-ID>_stg/deploy.log`
- **Pro Production**: `/var/log/platform/<project-ID>/deploy.log`

### ログをデプロイ

各デプロイメントのログは、特定の `deploy.log` ファイル。 次の例では、ターミナルに現在の環境のデプロイログを出力します。

```bash
magento-cloud log -e <environment-ID> deploy
```

レスポンスのサンプル：

```terminal
Reading log file projectID-branchname-ID--mymagento@ssh.zone.magento.cloud:/var/log/'deploy.log'

[2023-04-24 18:58:03.080678] Launching command 'b'php ./vendor/bin/ece-tools run scenario/deploy.xml\n''.

[2023-04-24T18:58:04.129888+00:00] INFO: Starting scenario(s): scenario/deploy.xml (magento/ece-tools version: 2002.1.14, magento/magento2-base version: 2.4.6)
[2023-04-24T18:58:04.364714+00:00] NOTICE: Starting pre-deploy.
...
```

{{scd-timing-warning}}

### エラーログ

デプロイメントプロセス中に生成されたエラーメッセージと警告メッセージは、 `var/log/cloud.log` そして `var/log/cloud.error.log` ファイル。 クラウドエラーログファイルには、最新のデプロイメントからのエラーと警告のみが含まれています。 空のファイルは、エラーなしでデプロイメントが成功したことを示します。

ログファイルを表示するには、 [Cloud CLI SSH](#view-remote-environment-logs)または、 ECE-Tools を使用して、エラーと修正候補を表示できます。

```bash
magento-cloud ssh -e <environment-ID> "./vendor/bin/ece-tools error:show"
```

レスポンスのサンプル：

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

ほとんどのエラーメッセージには、説明と推奨アクションが含まれています。 以下を使用します。 [ECE-Tools のエラーメッセージリファレンス](../dev-tools/error-reference.md) をクリックしてエラーコードを調べ、詳細なガイダンスを確認します。 その他のガイダンスについては、 [Adobe Commerceデプロイメントトラブルシューター](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/deployment/magento-deployment-troubleshooter.html).

## アプリケーションログ

デプロイログと同様に、アプリケーションログは環境ごとに一意です。

| ログファイル | スターターと Pro の統合 | 説明 |
| ------------------- | --------------------------- | ------------------------------------------------- |
| **ログをデプロイ** | `/var/log/deploy.log` | からのアクティビティ [デプロイフック](../application/hooks-property.md). |
| **デプロイ後のログ** | `/var/log/post_deploy.log` | からのアクティビティ [展開後のフック](../application/hooks-property.md). |
| **Cron ログ** | `/var/log/cron.log` | cron ジョブからの出力。 |
| **Nginx アクセスログ** | `/var/log/access.log` | Nginx の起動時に、見つからないディレクトリと除外されたファイルタイプに対する HTTP エラーが発生します。 |
| **Nginx エラーログ** | `/var/log/error.log` | Nginx に関連する設定エラーをデバッグする際に役立つ起動メッセージ。 |
| **PHP アクセスログ** | `/var/log/php.access.log` | PHP サービスに対する要求。 |
| **PHP FPM ログ** | `/var/log/app.log` | |

Pro ステージング環境および実稼動環境の場合、デプロイログ、デプロイ後ログ、Cron ログは、クラスターの最初のノードでのみ使用できます。

| ログファイル | Pro Staging | Pro Production |
| ------------------- | --------------------------------------------------- | ----------------------------------------------- |
| **ログをデプロイ** | 最初のノードのみ：<br>`/var/log/platform/<project-ID>_stg/deploy.log` | 最初のノードのみ：<br>`/var/log/platform/<project-ID>/deploy.log` |
| **デプロイ後のログ** | 最初のノードのみ：<br>`/var/log/platform/<project-ID>_stg/post_deploy.log` | 最初のノードのみ：<br>`/var/log/platform/<project-ID>/post_deploy.log` |
| **Cron ログ** | 最初のノードのみ：<br>`/var/log/platform/<project-ID>_stg/cron.log` | 最初のノードのみ：<br>`/var/log/platform/<project-ID>/cron.log` |
| **Nginx アクセスログ** | `/var/log/platform/<project-ID>_stg/access.log` | `/var/log/platform/<project-ID>/access.log` |
| **Nginx エラーログ** | `/var/log/platform/<project-ID>_stg/error.log` | `/var/log/platform/<project-ID>/error.log` |
| **PHP アクセスログ** | `/var/log/platform/<project-ID>_stg/php.access.log` | `/var/log/platform/<project-ID>/php.access.log` |
| **PHP FPM ログ** | `/var/log/platform/<project-ID>_stg/php5-fpm.log` | `/var/log/platform/<project-ID>/php5-fpm.log` |

### アーカイブされたログファイル

アプリケーションログは、1 日に 1 回圧縮およびアーカイブされ、1 年間保持されます。 圧縮ログの名前は、 `Number of Days Ago + 1`. 例えば、Pro 実稼動環境では、過去 21 日間の PHP アクセスログが次のように保存され、名前が付けられます。

```terminal
/var/log/platform/<project-ID>/php.access.log.22.gz
```

アーカイブされたログファイルは、常に、圧縮前に元のファイルが配置されたディレクトリに保存されます。

>[!NOTE]
>
>**デプロイ** および **デプロイ後** ログファイルは回転およびアーカイブされません。 デプロイメント履歴全体がこれらのログファイル内に書き込まれます。

## サービスログ

各サービスは別々のコンテナで実行されるので、統合環境ではサービスログを使用できません。 Adobe Commerce on cloud infrastructure は、統合環境でのみ web サーバーコンテナにアクセスできます。 次のサービスログの場所は、Pro 実稼動環境とステージング環境用です。

- **Redis ログ**: `/var/log/platform/<project-ID>_stg/redis-server-<project-ID>_stg.log`
- **Elasticsearchログ**: `/var/log/elasticsearch/elasticsearch.log`
- **Java ガベージコレクションログ**: `/var/log/elasticsearch/gc.log`
- **メールログ**: `/var/log/mail.log`
- **MySQL エラーログ**: `/var/log/mysql/mysql-error.log`
- **MySQL の低速ログ**: `/var/log/mysql/mysql-slow.log`
- **RabbitMQログ**: `/var/log/rabbitmq/rabbit@host1.log`

サービスログは、ログのタイプに応じて、様々な期間アーカイブおよび保存されます。 例えば、MySQL ログの有効期間が最も短く、7 日後に削除されます。

>[!TIP]
>
>スケールアーキテクチャ内のログファイルの場所は、ノードタイプによって異なります。 詳しくは、 [スケールアーキテクチャでのログの場所](../architecture/scaled-architecture.md#log-locations) トピック。

## プロプロダクションおよびステージング用のデータのログ

Pro 実稼動環境とステージング環境では、 [New Relic Log Management](../monitor/log-management.md) をプロジェクトと統合して、Adobe Commerce on cloud infrastructure プロジェクトに関連付けられたすべてのログの集計ログデータを管理します。

New Relic Logs アプリケーションは、クラウドインフラストラクチャの実稼動環境およびステージング環境でAdobe Commerceをトラブルシューティングおよび監視するための、一元化されたログ管理ダッシュボードを提供します。 また、このダッシュボードから、Fastly CDN、画像の最適化、Web アプリケーションファイアウォール (WAF) サービスのログデータにアクセスできます。 詳しくは、 [New Relic services](../monitor/new-relic-service.md).
