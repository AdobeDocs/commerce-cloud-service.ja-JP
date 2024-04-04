---
title: データベースのバックアップ
description: ECE-tools を使用して、クラウドインフラストラクチャプロジェクト上のAdobe Commerceのデータベースのバックアップを作成する方法を説明します。
feature: Cloud, Iaas, Storage
exl-id: 8a96effe-a587-4edf-b0c7-e73ca8d3b56c
source-git-commit: 4d790bff2ba5d02ef10de5c36a2f0d140e31a407
workflow-type: tm+mt
source-wordcount: '339'
ht-degree: 0%

---

# データベースのバックアップ

データベースのコピーを作成するには、 `ece-tools db-dump` コマンドを使用すると、すべての環境データがサービスとマウントからキャプチャされずに実行できます。 デフォルトでは、このコマンドは、 `/app/var/dump-main` 環境設定で指定されたすべてのデータベース接続のディレクトリ。 DB ダンプ操作は、アプリケーションをメンテナンスモードに切り替え、コンシューマーキュープロセスを停止し、ダンプが開始する前に cron ジョブを無効にします。

DB ダンプに関しては、次のガイドラインを考慮してください。

- 実稼動環境では、Adobeは、サイトがメンテナンスモードの場合に発生するサービスの中断を最小限に抑えるために、オフピーク時間中にデータベースダンプ操作を完了することを推奨します。
- ダンプ操作中にエラーが発生した場合は、ディスク領域を節約するためにダンプファイルが削除されます。 詳細はログを確認してください (`var/log/cloud.log`) をクリックします。
- Pro 実稼動環境の場合、このコマンドは _1 つ_ 3 つの高可用性ノードのうち、ダンプ中に別のノードに書き込まれた実稼動データがコピーされない可能性がある。 このコマンドにより、 `var/dbdump.lock` ファイルを使用して、複数のノードでコマンドが実行されないようにします。
- すべての環境サービスのバックアップに関して、Adobeは、 [バックアップ](snapshots.md).

コマンドにデータベース名を追加することで、複数のデータベースをバックアップすることを選択できます。 次の例では、2 つのデータベースをバックアップします。 `main` および `sales`:

```bash
php vendor/bin/ece-tools db-dump main sales
```

以下を使用します。 `php vendor/bin/ece-tools db-dump --help` コマンドを使用して、さらにオプションを指定します。

- `--dump-directory=<dir>` — データベースダンプのターゲットディレクトリを選択します。
- `--remove-definers` — データベースダンプから DEFINER 文を削除します。

**ステージング環境または実稼動環境でデータベースダンプを作成するには**:

1. [SSH を使用して、リモート環境にログインするか、トンネルを作成して接続します](../development/secure-connections.md) コピーするデータベースを含む

1. 環境の関係をリストし、データベースのログイン情報をメモします。

   ```bash
   echo $MAGENTO_CLOUD_RELATIONSHIPS | base64 -d | json_pp
   ```

   または

   ```bash
   php -r 'print_r(json_decode(base64_decode($_ENV["MAGENTO_CLOUD_RELATIONSHIPS"]))->database);'
   ```

1. データベースのバックアップを作成します。 DB ダンプのターゲットディレクトリを選択するには、 `--dump-directory` オプション。

   ```bash
   php vendor/bin/ece-tools db-dump -- main
   ```

   レスポンスのサンプル：

   ```terminal
   The db-dump operation switches the site to maintenance mode, stops all active cron jobs and consumer queue processes, and disables cron jobs before starting the dump process.
   Your site will not receive any traffic until the operation completes.
   Do you wish to proceed with this process? (y/N)? y
   2020-01-28 16:38:08] INFO: Starting backup.
   [2020-01-28 16:38:08] NOTICE: Enabling Maintenance mode
   [2020-01-28 16:38:10] INFO: Trying to kill running cron jobs and consumers processes
   [2020-01-28 16:38:10] INFO: Running Magento cron and consumers processes were not found.
   [2020-01-28 16:38:10] INFO: Waiting for lock on db dump.
   [2020-01-28 16:38:10] INFO: Start creation DB dump for main database...
   [2020-01-28 16:38:10] INFO: Finished DB dump for main database, it can be found here: /tmp/qxmtlseakof6y/dump-main-1580229490.sql.gz
   [2020-01-28 16:38:10] INFO: Backup completed.
   [2020-01-28 16:38:11] NOTICE: Maintenance mode is disabled.
   ```

1. The `db-dump` コマンドは、 `dump-<timestamp>.sql.gz` リモートプロジェクトディレクトリのアーカイブファイル。

>[!TIP]
>
>このデータを特定の環境にプッシュする場合は、 [データと静的ファイルの移行](../deploy/staging-production.md#migrate-static-files).
