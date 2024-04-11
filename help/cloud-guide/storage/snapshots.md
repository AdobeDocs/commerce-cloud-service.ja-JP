---
title: バックアップ管理
description: クラウドインフラストラクチャプロジェクト上のAdobe Commerceのバックアップを手動で作成および復元する方法について説明します。
feature: Cloud, Paas, Snapshots, Storage
exl-id: 1cb00db7-2375-4761-9c07-1e20a74859e0
source-git-commit: 069cbc233492d22932e8dce5bf0426dce8459727
workflow-type: tm+mt
source-wordcount: '685'
ht-degree: 0%

---

# バックアップ管理

アクティブなスターター環境の手動バックアップは、 **[!UICONTROL Backup]** のボタン [!DNL Cloud Console] または使用 `magento-cloud snapshot:create` コマンド。

バックアップまたは _スナップショット_ は、実行中のサービス（MySQL データベース）のすべての永続データと、マウントされたボリューム（var、pub/media、app/etc）に保存されたファイルを含む、環境データの完全なバックアップです。 スナップショットは次を実行します _ではない_ コードは Git ベースのリポジトリに既に保存されているので、コードを含めます。 スナップショットのコピーはダウンロードできません。

バックアップ/スナップショット機能では、次のことが行われます **ではない** ステージング環境および実稼動環境に適用します。これらの環境は、デフォルトで災害復旧用の通常のバックアップを受け取ります。 こちらを参照してください [Pro バックアップとディザスタリカバリ](../architecture/pro-architecture.md#backup-and-disaster-recovery) を参照してください。 ステージング環境および実稼動環境での自動ライブバックアップとは異なり、バックアップは次のとおりです **ではない** 自動。 このプロパティは _あなたの_ バックアップを手動で作成する責任、または Starter または Pro 統合環境のバックアップを定期的に作成する cron ジョブをセットアップする責任。

## 手動バックアップの作成

からアクティブなスターター環境と Integration Pro 環境の手動バックアップを作成できます [!DNL Cloud Console] または、Cloud CLI からスナップショットを作成します。 必須で、 [管理者の役割](../project/user-access.md) 環境の場合。

**を使用してスターター環境のバックアップを作成するには[!DNL Cloud Console]**:

1. にログインします [[!DNL Cloud Console]](https://console.adobecommerce.com).
1. プロジェクトナビゲーションバーから環境を選択します。 環境がアクティブである必要があります。
1. が含まれる _バックアップ_ 表示、クリック **[!UICONTROL Backup]**. このオプションは、Pro 環境では使用できません。

   ![バックアップ](../../assets/button-backup.png){width="150"}

**を使用して統合環境のバックアップを作成するには[!DNL Cloud Console]**:

1. にログインします [[!DNL Cloud Console]](https://console.adobecommerce.com).
1. プロジェクトナビゲーションバーから統合/開発環境を選択します。 環境がアクティブである必要があります。
1. 「」を選択します **[!UICONTROL Backup]** 右上のメニューの「」オプション。 このオプションは、Starter 環境と Pro 環境の両方で使用できます。
1. 「」をクリックします **[!UICONTROL Yes]** ボタン。

**を使用してスナップショットを作成するには `magento-cloud` CLI**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。
1. スナップショットを作成する環境ブランチをチェックアウトします。
1. スナップショットを作成します。

   ```bash
   magento-cloud snapshot:create --live
   ```

   または、を使用することもできます。 `magento-cloud backup` 短いコマンド。 この `--live` ダウンタイムを避けるために、オプションでは環境を実行したままにします。 オプションの完全なリストを表示するには、次のように入力します `magento-cloud snapshot:create --help`.

   応答の例：

   ```terminal
   Creating a snapshot of develop-branch
   Waiting for the activity ID (User created a backup of develop-branch):
   
   Creating backup of develop-branch
   Created backup my-snapshot
   [============================] 45 secs (complete)
   Activity ID succeeded
   Snapshot name: my-snapshot
   ```

1. 最新のスナップショットを検証します。

   ```bash
   magento-cloud snapshot:list
   ```

   リストは、スナップショットのステータスに関する情報を返します。

   ```terminal
   Snapshots on the project (project-id), environment develop-branch (type: development):
   +---------------------------+----------------------+------------+
   | Created                   | Snapshot ID          | Restorable |
   +---------------------------+----------------------+------------+
   | 2023-03-08T17:07:01+00:00 | my-snapshot          | true       |
   +---------------------------+----------------------+------------+
   ```

## 手動バックアップの復元

以下が必要です [管理アクセス](../project/user-access.md) を環境に追加します。 最大個のを持っています **7 日間** 対象： _復元_ 手動バックアップ。 バックアップを復元しても、現在の Git ブランチのコードは変更されません。 この方法でのバックアップの復元は、Pro ステージング環境と実稼動環境には適用されません。を参照してください [Pro バックアップとディザスタリカバリ](../architecture/pro-architecture.md#backup-and-disaster-recovery).

復元時間は、データベースのサイズによって異なります。

- 大規模なデータベース（200 GB 以上）では 5 時間かかる場合がある
- 中程度のデータベース（150 GB）で 2 1/2 時間かかる場合がある
- 小規模なデータベース（60 GB）では 1 時間かかる場合がある

>[!TIP]
>
>バックアップなしでリストアする：
>
>- 以前のコードにロールバックしたり、環境で追加した拡張機能を削除したりするには、を参照してください。 [コードをロールバック](#roll-back-code).
>- 次の手順で不安定な環境を復元します _ではない_ バックアップがある場合は、を参照してください [環境の復元](../development/restore-environment.md).

**を使用してバックアップを復元するには[!DNL Cloud Console]**:

1. にログインします [[!DNL Cloud Console]](https://console.adobecommerce.com).
1. プロジェクトナビゲーションバーから環境を選択します。
1. が含まれる _バックアップ_ 表示する、からバックアップを選択する _保存済み_ リスト。 バックアップ機能では、次のことが行われます **ではない** pro 環境に適用します。
1. が含まれる ![詳細](../../assets/icon-more.png){width="32"} （_詳細_） メニュー、クリック **復元**.
1. バックアップからの復元情報を確認し、 **はい、復元します**.

**Cloud CLI を使用してスナップショットを復元するには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。
1. 復元する環境ブランチをチェックアウトします。
1. 使用可能なすべてのスナップショットを一覧表示します。

   ```bash
   magento-cloud snapshot:list
   ```

   リストは、使用可能なスナップショットに関する情報を返します。

   ```terminal
   Snapshots on the project (project-id), environment develop-branch (type: development):
   +---------------------------+----------------------+------------+
   | Created                   | Snapshot ID          | Restorable |
   +---------------------------+----------------------+------------+
   | 2023-03-08T17:07:01+00:00 | my-snapshot          | true       |
   +---------------------------+----------------------+------------+
   ```

1. リストのスナップショット ID を使用してスナップショットを復元します。

   ```bash
   magento-cloud snapshot:restore <snapshot-id>
   ```

## コードをロールバック

バックアップとスナップショット _ではない_ コードのコピーを含めます。 コードは既に Git ベースのリポジトリに保存されているので、Git ベースのコマンドを使用してコードをロールバック（または元に戻す）できます。 例えば、 `git log --oneline` 前のコミットをスクロールするには、次を使用します [`git revert`](https://git-scm.com/docs/git-revert) 特定のコミットからコードを復元する

また、にコードを保存することもできます _inactive_ 分岐。 を使用する代わりに、Git コマンドを使用してブランチを作成します。 `magento-cloud` コマンド。 詳細を確認 [Git コマンド](../dev-tools/cloud-cli-overview.md#git-commands) Cloud CLI トピックの節を参照してください。
