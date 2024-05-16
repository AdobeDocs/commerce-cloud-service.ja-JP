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

データベースのコピーを作成するには、 `ece-tools db-dump` サービスおよびマウントからすべての環境データを取得せずにコマンドを実行します。 デフォルトでは、このコマンドは `/app/var/dump-main` 環境設定で指定されたすべてのデータベース接続のディレクトリ。 DB ダンプ操作は、アプリケーションをメンテナンスモードに切り替え、コンシューマーキュープロセスを停止し、ダンプを開始する前に cron ジョブを無効にします。

DB ダンプに関する次のガイドラインを考慮してください。

- 実稼動環境の場合、Adobeでは、サイトがメンテナンスモードの場合にサービスの中断を最小限に抑えるために、ピーク外の時間にデータベースダンプ処理を完了することをお勧めします。
- ダンプ処理中にエラーが発生した場合は、ディスク容量を節約するために、ダンプ・ファイルが削除されます。 詳細についてはログを確認してください（`var/log/cloud.log`）に設定します。
- Pro 実稼動環境の場合、このコマンドは次の場所からのみダンプします。 _1_ 3 つの高可用性ノードのうち、ダンプ中に別のノードに書き込まれた実稼動データはコピーされない可能性があります。 このコマンドは、 `var/dbdump.lock` ファイル：コマンドが複数のノードで実行されないようにします。
- すべての環境サービスのバックアップの場合、Adobeでは次を作成することをお勧めします [バックアップ](snapshots.md).

コマンドにデータベース名を追加することで、複数のデータベースをバックアップするように選択できます。 次の例では、2 つのデータベースをバックアップします。 `main` および `sales`:

```bash
php vendor/bin/ece-tools db-dump main sales
```

の使用 `php vendor/bin/ece-tools db-dump --help` その他のオプションを表示するには、次のコマンドを使用します。

- `--dump-directory=<dir>`— データベース ダンプのターゲット ディレクトリを選択します。
- `--remove-definers`— データベース ダンプから DEFINER ステートメントを削除します。

**ステージング環境または実稼動環境でデータベースダンプを作成するには、次の手順に従います**:

1. [SSH を使用してログインするか、リモート環境に接続するトンネルを作成します。](../development/secure-connections.md) コピーするデータベースを含みます。

1. 環境の関係を一覧表示し、データベースのログイン情報をメモします。

   ```bash
   echo $MAGENTO_CLOUD_RELATIONSHIPS | base64 -d | json_pp
   ```

   または

   ```bash
   php -r 'print_r(json_decode(base64_decode($_ENV["MAGENTO_CLOUD_RELATIONSHIPS"]))->database);'
   ```

1. データベースのバックアップを作成します。 DB ダンプのターゲット・ディレクトリを選択するには、 `--dump-directory` オプション。

   ```bash
   php vendor/bin/ece-tools db-dump -- main
   ```

   応答の例：

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

1. この `db-dump` コマンドは、 `dump-<timestamp>.sql.gz` リモートプロジェクトディレクトリのアーカイブファイル。

>[!TIP]
>
>このデータを特定の環境にプッシュする場合は、 [データおよび静的ファイルの移行](../deploy/staging-production.md#migrate-static-files).
