---
title: ステージング環境および実稼動環境へのデプロイ
description: さらなるテストをおこなうために、クラウドインフラストラクチャコード上のAdobe Commerceをステージング環境と実稼動環境にデプロイする方法を説明します。
feature: Cloud, Console, Deploy, SCD, Storage
exl-id: 4b82289f-ee04-4b14-a0ed-7a8a19fc6a6a
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '1289'
ht-degree: 0%

---

# ステージング環境および実稼動環境へのデプロイ

デプロイおよびライブに移行するプロセスは、開発から始まり、ステージングに進み、実稼動に移行します。 Adobeは、一貫した構成を確保するエンドツーエンドの環境ソリューションを提供します。 各環境では、CLI コマンド用のストアフロントおよび Admin および SSH アクセスへの直接 URL アクセスがサポートされています。

ストアをデプロイする準備が整ったら、実稼動環境にデプロイする前に、ステージング環境でデプロイメントとテストを完了する必要があります。 この節では、ビルドおよびデプロイプロセス、データおよびコンテンツの移行、テストに関する詳細な手順と情報を説明します。

>[!TIP]
>
>Adobeは、 [バックアップ](../storage/snapshots.md) をデプロイします。

また、 [New Relicを使用したデプロイメントの追跡](../monitor/track-deployments.md) デプロイメントイベントを監視し、デプロイメント間のパフォーマンスを分析するのに役立ちます。

## スターターデプロイメントフロー

Adobeは、 `staging` ～から分岐する `master` 分岐を使用して、スタータープランの開発と導入を最も効果的にサポートします。 次に、4 つのアクティブな環境のうち 2 つを準備します。 `master` （実稼動用）および `staging` ステージング用。

このプロセスについて詳しくは、 [スターター開発とデプロイワークフロー](../architecture/starter-develop-deploy-workflow.md).

## Pro デプロイメントフロー

Pro には、2 つのアクティブなブランチ（グローバル）を持つ大規模な統合環境が備わっています。 `master` ブランチ、ステージングブランチ、実稼動ブランチ。 プロジェクトを作成すると、コードは、サイトの構築とデプロイのために、ブランチ、開発およびプッシュの準備が整います。 統合環境には多数のブランチを持つことができますが、ステージングと実稼動環境には、各環境に対して 1 つのブランチしかありません。

このプロセスについて詳しくは、 [Pro Develop and Deploy Workflow](../architecture/pro-develop-deploy-workflow.md).

## コードをステージング環境にデプロイする

ステージング環境は、データベース、Web サーバー、および Fastly やNew Relicを含むすべてのサービスを含む、実稼動近くの環境を提供します。 を使用して、完全にプッシュ、結合およびデプロイできます。 [[!DNL Cloud Console]](../project/overview.md) または [Cloud CLI コマンド](../dev-tools/cloud-cli-overview.md) ターミナルアプリケーションを通じて。

### を使用してコードをデプロイする [!DNL Cloud Console]

The [!DNL Cloud Console] は、Starter プランと Pro プランの統合環境、ステージング環境および実稼動環境でコードを作成、管理、デプロイする機能を提供します。

**Pro プロジェクトの場合は、統合ブランチをステージングにデプロイします。**:

1. [ログイン](https://accounts.magento.cloud) をプロジェクトに追加します。
1. を選択します。 `integration` 分岐。
1. を選択します。 **結合** ステージングにデプロイするオプション。

   ![結合](../../assets/button-merge.png){width="150"}

1. すべてを完了 [テスト](../test/staging-and-production.md) （ステージング環境）を参照してください。
1. ステージングの準備が整ったら、 **結合** オプションを使用して実稼動環境にデプロイできます。

**スターターの場合は、開発ブランチをステージングにデプロイします。**:

1. [ログイン](https://accounts.magento.cloud) をプロジェクトに追加します。
1. 準備済みコードブランチを選択します。
1. を選択します。 **結合** ステージングにデプロイするオプション。

   ![結合](../../assets/button-merge.png){width="150"}

1. すべてを完了 [テスト](../test/staging-and-production.md) （ステージング環境）を参照してください。
1. ステージングの準備が整ったら、 **結合** 実稼動環境にデプロイするオプション (`master`) をクリックします。

### コマンドラインを使用してコードをデプロイする

Cloud CLI には、コードをデプロイするコマンドが用意されています。 プロジェクトに SSH および Git でアクセスできる必要があります。

#### 手順 1：統合環境のデプロイとテスト

1. プロジェクトにログインした後、統合環境を確認します。

   ```bash
   magento-cloud environment:checkout <environment-ID>
   ```

1. ローカル統合環境をリモート環境と同期します。

   ```bash
   magento-cloud environment:synchronize <environment-ID>
   ```

1. バックアップとして環境のスナップショットを作成します。

   ```bash
   magento-cloud snapshot: create -e <environment-ID>
   ```

1. 必要に応じて、ローカルブランチのコードを更新します。

1. 変更を環境に追加、コミット、プッシュします。

   ```bash
   git add -A && git commit -m "Commit message" && git push origin <environment-ID>
   ```

1. サイトテストを完了します。

#### 手順 2：ステージングおよびデプロイの変更を結合する

1. ステージング環境を確認します。

   ```bash
   magento-cloud environment:checkout <environment-ID>
   ```

1. ローカルのステージング環境をリモート環境と同期します。

   ```bash
   magento-cloud environment:synchronize <environment-ID>
   ```

1. バックアップとして環境のスナップショットを作成します。

   ```bash
   magento-cloud snapshot: create -e <environment-ID>
   ```

1. 統合環境をステージングに統合して、以下をデプロイします。

   ```bash
   magento-cloud environment:merge <integration-ID>
   ```

1. サイトテストを完了します。

#### 手順 3：実稼動環境へのデプロイ

1. ローカルの実稼動環境のスナップショットを確認、同期、作成します。

1. ステージング環境を実稼動環境に結合してデプロイ：

   ```bash
   magento-cloud environment:merge <staging-ID>
   ```

1. サイトテストを完了します。

## 静的ファイルを移行

[静的ファイル](https://experienceleague.adobe.com/docs/commerce-operations/operational-playbook/glossary.html) が `mounts`. ローカル環境などのソース・マウント・ロケーションからデスティネーション・マウント・ロケーションにファイルを移行する方法は 2 つあります。 どちらの方法でも `rsync` ユーティリティの場合は、Adobeは `magento-cloud` ローカル環境とリモート環境の間でファイルを移動するための CLI。 また、Adobeは、 `rsync` メソッドを使用して、ファイルをリモートソースから別のリモートの場所に移動する場合に使用します。

### CLI を使用したファイルの移行

以下を使用すると、 `mount:upload` および `mount:download` ローカル環境とリモート環境の間でファイルを移行する CLI コマンド。 どちらのコマンドでも、 `rsync` ユーティリティですが、CLI コマンドは、クラウドインフラストラクチャ環境上のAdobe Commerceに合わせてカスタマイズされたオプションとプロンプトを提供します。 たとえば、オプションを指定せずに単純なコマンドを使用する場合、CLI は、アップロードまたはダウンロードするマウントまたはマウントを選択するように求めます。

```bash
magento-cloud mount:download
```

レスポンスのサンプル：

```terminal
Enter a number to choose a mount to download from:
  [0] app/etc
  [1] pub/static
  [2] var
  [3] pub/media
  [4] All mounts
 > 3

Target directory: ~/pub/media/

Downloading files from the remote mount pub/media to pub/media

Are you sure you want to continue? [Y/n] Y
```

**ローカルからファイルをアップロードするには `pub/media/` リモートフォルダーへ `pub/media/` 現在の環境のフォルダー**:

```bash
magento-cloud mount:upload --source /path/to/project/pub/media/ --mount pub/media/
```

レスポンスのサンプル：

```terminal
Uploading files from pub/media to the remote mount pub/media

Are you sure you want to continue? [Y/n] Y

  building file list ...   done
  ./
  sample-file.jpeg

  sent 8.43K bytes  received 48 bytes  3.39K bytes/sec
  total size is 154.57K  speedup is 18.23
```

以下を使用します。 `--help` オプション `mount:upload` および `mount:download` コマンドを使用して、さらに多くのオプションを表示できます。 例えば、 `--delete` 移行中に不要なファイルを削除するオプション。

### rsync を使用してファイルを移行する

または、 `rsync` ファイルを移行するユーティリティ。

```bash
rsync -azvP <source> <destination>
```

このコマンドは、次のオプションを使用します。

- `a`-archive
- `z`-compress ファイルを移行中に
- `v`-verbose
- `P` — 部分的な進行

詳しくは、 [rsync](https://linux.die.net/man/1/rsync) ヘルプ。

>[!NOTE]
>
>リモートからリモート環境に直接メディアを転送するには、SSH エージェント転送を有効にする必要があります。詳しくは、 [GitHub ガイダンス](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/using-ssh-agent-forwarding).

**静的ファイルをリモートからリモート環境に直接移行する（高速アプローチ）**:

1. SSH を使用して、ソース環境にログインします。 次を使用しない `magento-cloud` CLI。 の使用 `-A` オプションは、認証エージェント接続の転送を有効にするので、重要です。

   >[!TIP]
   >
   >次を検索： **SSH アクセス** リンクを [!DNL Cloud Console]」をクリックし、環境を選択して、 **サイトにアクセス**.

   ```bash
   ssh -A <environment_ssh_link@ssh.region.magento.cloud>
   ```

1. 以下を使用します。 `rsync` コマンドを使用して `pub/media` ソース環境から別のリモート環境にディレクトリを作成します。

   ```bash
   rsync -azvP pub/media/ <destination_environment_ssh_link@ssh.region.magento.cloud>:pub/media/
   ```

1. 他のリモート環境にログインして、ファイルが正常に移行されたことを確認します。

## データベースを移行

>[!WARNING]
>
>データベースのインポートおよびエクスポート操作には時間がかかる場合があり、サイトのパフォーマンスと可用性に影響を与える可能性があります。 本番サイトでのパフォーマンスの低下や停止を防ぐために、オフピーク時にインポートおよびエクスポート操作をスケジュールします。

>[!BEGINSHADEBOX]

**前提条件：** データベースダンプ（手順 3 を参照）には、データベーストリガーが含まれます。 ダンプする場合は、 [トリガー権限](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_trigger).

>[!IMPORTANT]
>
>統合環境データベースは開発テスト用に厳密に定義されており、ステージングおよび実稼動環境に移行しないデータを含めることができます。

継続的な統合のデプロイメントの場合、Adobe **次を推奨しない** 統合からステージングおよび実稼動環境へのデータの移行。 テストデータに合格したり、重要なデータを上書きしたりできます。 重要な設定は、 [設定ファイル](../store/store-settings.md) および `setup:upgrade` コマンドを使用して、ビルドとデプロイを実行できます。

>[!ENDSHADEBOX]

Adobe **を推奨** 実稼動環境からステージング環境へデータを移行してサイトを完全にテストし、すべてのサービスと設定を含む実稼動近くの環境に保存する。

>[!NOTE]
>
>リモートからリモート環境にメディアを直接転送するには、ssh エージェントの転送を有効にする必要があります。詳しくは、 [GitHub ガイダンス](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/using-ssh-agent-forwarding).

### データベースのバックアップ

データベースのバックアップを作成することをお勧めします。 以下の手順では、 [データベースのバックアップ](../storage/database-dump.md).

**データベースをダンプするには**:

1. [SSH を使用してリモート環境にログイン](../development/secure-connections.md#use-an-ssh-command) コピーするデータベースを含む

1. 環境の関係をリストし、データベースのログイン情報をメモします。

   ```bash
   php -r 'print_r(json_decode(base64_decode($_ENV["MAGENTO_CLOUD_RELATIONSHIPS"]))->database);'
   ```

   Pro Staging および Production の場合、データベースの名前は `MAGENTO_CLOUD_RELATIONSHIPS` 変数（通常、アプリケーション名およびユーザー名と同じ）。

1. データベースのバックアップを作成します。 DB ダンプのターゲットディレクトリを選択するには、 `--dump-directory` オプション。

   スターター環境と Pro 統合環境の場合は、 `main` データベースの名前：

   ```bash
   php vendor/bin/ece-tools db-dump main
   ```

   ダンプオプション：
   - `--dump-directory=<dir>` — データベースダンプのターゲットディレクトリを選択します。
   - `--remove-definers` — データベースダンプから DEFINER 文を削除します。

1. ECE-Tools メソッドをお勧めしますが、GZIP 形式のネイティブ MySQL を使用してデータベースダンプファイルを作成する方法もあります。

   ```bash
   mysqldump -h <database-host> --user=<database-username> --password=<password> --single-transaction --triggers <database-name> | gzip - > /tmp/database.sql.gz
   ```

   ターゲット環境で 2 要素認証を設定した場合は、関連する 2FA テーブルを除外して、データベース移行後に再設定しないようにする方が効果的です。

   ```bash
   mysqldump -h <database-host> --user=<database-username> --password=<password> --single-transaction --triggers --ignore-table=<database-name>.tfa_user_config --ignore-table=<database-name>.tfa_country_codes <database-name> | gzip - > /tmp/database.sql.gz
   ```

1. タイプ `logout` をクリックして SSH 接続を終了します。

### データベースを削除して再作成する

データをインポートする場合は、データベースを削除して作成する必要があります。

**データベースを削除して再作成するには**:

1. の確立 [SSH トンネル](../development/secure-connections.md#ssh-tunneling) をリモート環境に追加します。

1. データベースサービスに接続します。

   ```bash
   mysql --host=127.0.0.1 --user='<database-username>' --pass='<user-password>' --database='<name>' --port='<port>'
   ```

1. 次の場合： `MariaDB [main]>` プロンプトが表示されたら、データベースを削除します。

   Starter と Pro の統合の場合：

   ```shell
   drop database main;
   ```

   実稼動環境の場合：

   ```shell
   drop database <cluster-id>;
   ```

   ステージングの場合：

   ```shell
   drop database <cluster-ID_stg>;
   ```

1. データベースを再作成します。

   Starter と Pro の統合の場合：

   ```shell
   create database main;
   ```

   実稼動用にインポート：

   ```shell
   zcat <cluster-ID>.sql.gz | sed -e 's/DEFINER[ ]*=[ ]*[^*]*\*/\*/' | mysql -h 127.0.0.1 -p -u <database-username> <database-name>;
   ```

   ステージング用にインポート：

   ```shell
   zcat <cluster-ID_stg>.sql.gz | sed -e 's/DEFINER[ ]*=[ ]*[^*]*\*/\*/' | mysql -h 127.0.0.1 -p -u <database-username> <database-name>;
   ```
