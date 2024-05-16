---
title: ステージング環境および実稼動環境にデプロイ
description: さらなるテストのために、クラウドインフラストラクチャコード上でAdobe Commerceをステージング環境および実稼動環境にデプロイする方法について説明します。
feature: Cloud, Console, Deploy, SCD, Storage
exl-id: 4b82289f-ee04-4b14-a0ed-7a8a19fc6a6a
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '1289'
ht-degree: 0%

---

# ステージング環境および実稼動環境にデプロイ

デプロイと運用開始のプロセスは、開発から始まり、ステージングに続き、実稼動での運用開始で終わります。 Adobeは、一貫性のある設定を確実に行うためのエンドツーエンドの環境ソリューションを提供します。 すべての環境で、ストアフロントへの直接 URL アクセス、および CLI コマンドへの管理者および SSH アクセスがサポートされています。

ストアをデプロイする準備が整ったら、実稼動環境にデプロイする前に、ステージング環境でのデプロイメントとテストを完了する必要があります。 この節では、ビルドおよびデプロイプロセス、データとコンテンツの移行およびテストに関する詳細な手順と情報を示します。

>[!TIP]
>
>Adobeでは、以下を作成することをお勧めします [バックアップ](../storage/snapshots.md) デプロイメント前の環境の

また、を有効にすることもできます [New Relicを使用したデプロイメントの追跡](../monitor/track-deployments.md) デプロイメントのイベントを監視し、デプロイメント間のパフォーマンスを分析するのに役立ちます。

## スターターデプロイメントフロー

Adobeでは、以下を作成することをお勧めします `staging` 分岐の `master` スタータープランの開発とデプロイメントを最適にサポートするブランチ。 次に、4 つのアクティブな環境のうち 2 つが準備されています。 `master` 実稼動用および `staging` ステージングの場合。

このプロセスの詳細については、を参照してください [スターターの開発とデプロイのワークフロー](../architecture/starter-develop-deploy-workflow.md).

## プロデプロイメントフロー

Pro には、2 つのアクティブなブランチを持つ大規模な統合環境（グローバル）が付属しています `master` ブランチ、ステージングおよび実稼動のブランチ。 プロジェクトを作成すると、サイトの構築とデプロイのために、コードを分岐、開発、プッシュする準備が整います。 統合環境には多数のブランチを含めることができますが、ステージング環境と実稼動環境のブランチは各環境に 1 つだけです。

このプロセスの詳細については、を参照してください [プロ開発およびデプロイワークフロー](../architecture/pro-develop-deploy-workflow.md).

## コードをステージングにデプロイ

このステージング環境は、データベース、web サーバー、Fastly やNew Relicなどのすべてのサービスを含む、実稼動環境に近い環境を提供します。 を介して完全にプッシュ、結合、デプロイできます [[!DNL Cloud Console]](../project/overview.md) または [クラウド CLI コマンド](../dev-tools/cloud-cli-overview.md) ターミナルアプリケーションを使用します。

### を使用したコードのデプロイ [!DNL Cloud Console]

この [!DNL Cloud Console] は、スタータープランと Pro プランの統合、ステージングおよび実稼動環境でコードを作成、管理、デプロイする機能を提供します。

**Pro プロジェクトの場合は、統合ブランチをステージングにデプロイします**:

1. [ログイン](https://accounts.magento.cloud) をプロジェクトに追加します。
1. 「」を選択します `integration` 分岐。
1. 「」を選択します **結合** ステージングにデプロイするオプション。

   ![結合](../../assets/button-merge.png){width="150"}

1. すべて完了 [テスト](../test/staging-and-production.md) ステージング環境で行います。
1. ステージングの準備が整ったら、 **結合** 実稼動環境にデプロイするオプション。

**スターターの場合、開発ブランチをステージングにデプロイします**:

1. [ログイン](https://accounts.magento.cloud) をプロジェクトに追加します。
1. 準備済みコードブランチを選択します。
1. 「」を選択します **結合** ステージングにデプロイするオプション。

   ![結合](../../assets/button-merge.png){width="150"}

1. すべて完了 [テスト](../test/staging-and-production.md) ステージング環境で行います。
1. ステージングの準備が整ったら、 **結合** 実稼動にデプロイするオプション （`master`）に設定します。

### コマンドラインでコードをデプロイします。

Cloud CLI には、コードをデプロイするコマンドが用意されています。 プロジェクトに SSH および Git アクセス権が必要です。

#### 手順 1：統合環境のデプロイとテスト

1. プロジェクトにログインしたら、統合環境をチェックアウトします。

   ```bash
   magento-cloud environment:checkout <environment-ID>
   ```

1. ローカル統合環境をリモート環境と同期します。

   ```bash
   magento-cloud environment:synchronize <environment-ID>
   ```

1. 環境のスナップショットをバックアップとして作成します。

   ```bash
   magento-cloud snapshot: create -e <environment-ID>
   ```

1. 必要に応じて、ローカルブランチのコードを更新します。

1. 環境に対する変更を追加、コミット、プッシュします。

   ```bash
   git add -A && git commit -m "Commit message" && git push origin <environment-ID>
   ```

1. 完全なサイトテスト。

#### 手順 2：変更をステージングとデプロイに結合する

1. ステージング環境をチェックアウトします。

   ```bash
   magento-cloud environment:checkout <environment-ID>
   ```

1. ローカルのステージング環境をリモート環境と同期します。

   ```bash
   magento-cloud environment:synchronize <environment-ID>
   ```

1. 環境のスナップショットをバックアップとして作成します。

   ```bash
   magento-cloud snapshot: create -e <environment-ID>
   ```

1. デプロイする統合環境をステージング環境に結合します。

   ```bash
   magento-cloud environment:merge <integration-ID>
   ```

1. 完全なサイトテスト。

#### 手順 3：実稼動環境へのデプロイ

1. ローカルの実稼動環境のスナップショットをチェックアウト、同期、作成します。

1. ステージング環境を実稼動環境に結合してデプロイします。

   ```bash
   magento-cloud environment:merge <staging-ID>
   ```

1. 完全なサイトテスト。

## 静的ファイルの移行

[静的ファイル](https://experienceleague.adobe.com/docs/commerce-operations/operational-playbook/glossary.html) に保存されます。 `mounts`. ローカル環境などのソース・マウントの場所からターゲット・マウントの場所にファイルを移行する方法は 2 つあります。 どちらの方法でも、 `rsync` ユーティリティですが、Adobeでは `magento-cloud` ローカル環境とリモート環境の間でファイルを移動するための CLI。 また、Adobeでは、 `rsync` リモートソースから別のリモートの場所にファイルを移動する場合のメソッド。

### CLI を使用したファイルの移行

を使用できます `mount:upload` および `mount:download` ローカル環境とリモート環境の間でファイルを移行する CLI コマンド。 どちらのコマンドも、 `rsync` ユーティリティですが、CLI コマンドでは、クラウドインフラストラクチャー環境のAdobe Commerceに合わせてカスタマイズされたオプションとプロンプトが提供されます。 たとえば、オプションを指定せずに単純なコマンドを使用した場合、アップロードまたはダウンロードするマウントを選択するよう CLI によって求められます。

```bash
magento-cloud mount:download
```

応答の例：

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

**ローカルからファイルをアップロードするには `pub/media/` リモートへのフォルダー `pub/media/` 現在の環境のフォルダー**:

```bash
magento-cloud mount:upload --source /path/to/project/pub/media/ --mount pub/media/
```

応答の例：

```terminal
Uploading files from pub/media to the remote mount pub/media

Are you sure you want to continue? [Y/n] Y

  building file list ...   done
  ./
  sample-file.jpeg

  sent 8.43K bytes  received 48 bytes  3.39K bytes/sec
  total size is 154.57K  speedup is 18.23
```

の使用 `--help` オプション： `mount:upload` および `mount:download` その他のオプションを表示するコマンド。 例えば、 `--delete` 移行中に不要なファイルを削除するオプション。

### rsync を使用したファイルの移行

または、を使用することもできます。 `rsync` ファイルを移行するためのユーティリティ。

```bash
rsync -azvP <source> <destination>
```

このコマンドは、次のオプションを使用します。

- `a`-archive
- `z` – マイグレーション中にファイルを圧縮する
- `v`-verbose
- `P`部分的な進捗状況

参照： [rsync](https://linux.die.net/man/1/rsync) 助けて。

>[!NOTE]
>
>リモート環境からリモート環境にメディアを直接転送するには、SSH エージェント転送を有効にする必要があります。詳しくは、を参照してください。 [GitHub ガイダンス](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/using-ssh-agent-forwarding).

**静的ファイルをリモート環境からリモート環境に直接移行する（高速アプローチ）**:

1. SSH を使用してソース環境にログインします。 を使用しないでください。 `magento-cloud` CLI。 使用， `-A` 認証エージェント接続の転送を有効にするので、オプションは重要です。

   >[!TIP]
   >
   >を検索するには **SSH アクセス** 内のリンク [!DNL Cloud Console]を選択し、環境を選択して、 **サイトへのアクセス**.

   ```bash
   ssh -A <environment_ssh_link@ssh.region.magento.cloud>
   ```

1. の使用 `rsync` コピーするコマンド `pub/media` ソース環境から別のリモート環境へのディレクトリ。

   ```bash
   rsync -azvP pub/media/ <destination_environment_ssh_link@ssh.region.magento.cloud>:pub/media/
   ```

1. 他のリモート環境にログインして、ファイルが正常に移行されたことを確認します。

## データベースの移行

>[!WARNING]
>
>データベースのインポートおよびエクスポート操作には時間がかかることがあり、サイトのパフォーマンスと可用性に影響を与える可能性があります。 本番サイトでのパフォーマンスの低下や停止を防ぐため、ピーク以外の時間帯にインポートおよびエクスポート操作をスケジュールします。

>[!BEGINSHADEBOX]

**前提条件：** データベースダンプ（手順 3 を参照）には、データベーストリガーを含める必要があります。 これらをダンプする場合は、次の点を確認します [トリガー権限](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_trigger).

>[!IMPORTANT]
>
>統合環境データベースは開発テスト専用であり、ステージング環境や実稼動環境に移行しないデータを含めることができます。

継続的統合デプロイメントの場合、Adobe **推奨しない** 統合からステージング環境および実稼動環境へのデータの移行 テストデータに合格したり、重要なデータを上書きしたりできます。 重要な設定はすべて、 [設定ファイル](../store/store-settings.md) および `setup:upgrade` ビルドおよびデプロイ時のコマンド。

>[!ENDSHADEBOX]

Adobe **推奨** 実稼動環境からステージング環境にデータを移行して、サイトを完全にテストし、すべてのサービスと設定を備えたほぼ実稼動環境に保存します。

>[!NOTE]
>
>リモート環境からリモート環境に直接メディアを転送するには、ssh エージェント転送を有効にする必要があります。 [GitHub ガイダンス](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/using-ssh-agent-forwarding).

### データベースのバックアップ

データベースのバックアップを作成することをお勧めします。 次の手順では、のガイダンスを使用しています。 [データベースのバックアップ](../storage/database-dump.md).

**データベースをダンプするには**:

1. [SSH を使用してリモート環境にログインする](../development/secure-connections.md#use-an-ssh-command) コピーするデータベースを含みます。

1. 環境の関係を一覧表示し、データベースのログイン情報をメモします。

   ```bash
   php -r 'print_r(json_decode(base64_decode($_ENV["MAGENTO_CLOUD_RELATIONSHIPS"]))->database);'
   ```

   ステージング環境および実稼動環境の場合、データベースの名前はにあります。 `MAGENTO_CLOUD_RELATIONSHIPS` 変数（通常、アプリケーション名およびユーザー名と同じ）。

1. データベースのバックアップを作成します。 DB ダンプのターゲット・ディレクトリを選択するには、 `--dump-directory` オプション。

   スターター環境と Pro 統合環境の場合は、を使用します `main` データベースの名前として、次の操作を行います。

   ```bash
   php vendor/bin/ece-tools db-dump main
   ```

   ダンプオプション：
   - `--dump-directory=<dir>`— データベース ダンプのターゲット ディレクトリを選択します。
   - `--remove-definers`— データベース ダンプから DEFINER ステートメントを削除します。

1. ECE ツール方式をお勧めしますが、別の方法として、ネイティブの MySQL を使用して GZIP 形式でデータベースダンプファイルを作成する方法もあります。

   ```bash
   mysqldump -h <database-host> --user=<database-username> --password=<password> --single-transaction --triggers <database-name> | gzip - > /tmp/database.sql.gz
   ```

   ターゲット環境で二要素認証を設定した場合は、関連する 2FA テーブルを除外して、データベース移行後に再設定しないようにすることをお勧めします。

   ```bash
   mysqldump -h <database-host> --user=<database-username> --password=<password> --single-transaction --triggers --ignore-table=<database-name>.tfa_user_config --ignore-table=<database-name>.tfa_country_codes <database-name> | gzip - > /tmp/database.sql.gz
   ```

1. タイプ `logout` SSH 接続を終了します。

### データベースを削除して再作成します

データをインポートする場合、データベースをドロップして作成する必要があります。

**データベースを削除して再作成するには、次の手順に従います**:

1. を確立する [SSH トンネル](../development/secure-connections.md#ssh-tunneling) をリモート環境に送信します。

1. データベースサービスに接続します。

   ```bash
   mysql --host=127.0.0.1 --user='<database-username>' --pass='<user-password>' --database='<name>' --port='<port>'
   ```

1. 時刻 `MariaDB [main]>` プロンプトを表示して、データベースを削除します。

   スターターと Pro の統合の場合：

   ```shell
   drop database main;
   ```

   実稼動の場合：

   ```shell
   drop database <cluster-id>;
   ```

   ステージング用：

   ```shell
   drop database <cluster-ID_stg>;
   ```

1. データベースを再作成します。

   スターターと Pro の統合の場合：

   ```shell
   create database main;
   ```

   実稼動用に読み込み：

   ```shell
   zcat <cluster-ID>.sql.gz | sed -e 's/DEFINER[ ]*=[ ]*[^*]*\*/\*/' | mysql -h 127.0.0.1 -p -u <database-username> <database-name>;
   ```

   ステージング用にインポート :

   ```shell
   zcat <cluster-ID_stg>.sql.gz | sed -e 's/DEFINER[ ]*=[ ]*[^*]*\*/\*/' | mysql -h 127.0.0.1 -p -u <database-username> <database-name>;
   ```
