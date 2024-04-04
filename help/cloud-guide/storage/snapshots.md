---
title: バックアップ管理
description: クラウドインフラストラクチャプロジェクト上でAdobe Commerceのバックアップを手動で作成し、復元する方法を説明します。
feature: Cloud, Paas, Snapshots, Storage
exl-id: 1cb00db7-2375-4761-9c07-1e20a74859e0
source-git-commit: 1d8ffabb9f903e89495d11c973a9f0a5a8dd1d43
workflow-type: tm+mt
source-wordcount: '682'
ht-degree: 0%

---

# バックアップ管理

アクティブなスターター環境の手動バックアップは、 **[!UICONTROL Backup]** ボタン [!DNL Cloud Console] または `magento-cloud snapshot:create` コマンドを使用します。

バックアップまたは _スナップショット_ は、実行中のサービス（MySQL データベース）と、マウントされたボリューム (var、pub/media、app/etc) に保存されているすべての永続的なデータを含む、環境データの完全なバックアップです。 スナップショットは実行します。 _not_ コードは既に Git ベースのリポジトリに保存されているので、コードをインクルードします。 スナップショットのコピーをダウンロードすることはできません。

バックアップ機能では、 **not** を Pro 環境に適用します。 Pro ステージング環境と実稼動環境は、デフォルトで、災害復旧用の定期的なバックアップを受け取ります。詳しくは、 [プロバックアップと災害復旧](../architecture/pro-architecture.md#backup-and-disaster-recovery). Pro ステージング環境および実稼動環境の自動ライブバックアップとは異なり、バックアップは **not** 自動。 それは _あなたの_ バックアップを手動で作成するか、cron ジョブを設定して、Starter または Pro 統合環境のバックアップを定期的に作成する責任があります。

## 手動バックアップの作成

任意のアクティブな Starter 環境と統合 Pro 環境の手動バックアップは、 [!DNL Cloud Console] または、Cloud CLI からスナップショットを作成します。 次が必要です： [管理者の役割](../project/user-access.md) 環境に対して。

**を使用して任意の Starter 環境のバックアップを作成するには[!DNL Cloud Console]**:

1. にログインします。 [[!DNL Cloud Console]](https://console.adobecommerce.com).
1. プロジェクトナビゲーションバーから環境を選択します。 環境がアクティブである必要があります。
1. Adobe Analytics の _バックアップ_ 表示、クリック **[!UICONTROL Backup]**. このオプションは、Pro 環境では使用できません。

   ![バックアップ](../../assets/button-backup.png){width="150"}

**を使用して統合環境のバックアップを作成するには、以下を実行します。[!DNL Cloud Console]**:

1. にログインします。 [[!DNL Cloud Console]](https://console.adobecommerce.com).
1. プロジェクトナビゲーションバーから統合/開発環境を選択します。 環境がアクティブである必要があります。
1. を選択します。 **[!UICONTROL Backup]** 」オプションを使用します。 このオプションは、Starter 環境と Pro 環境の両方で使用できます。
1. 次をクリック： **[!UICONTROL Yes]** 」ボタンをクリックします。

**を使用してスナップショットを作成するには `magento-cloud` CLI**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。
1. スナップショットに対する環境の分岐を確認します。
1. スナップショットを作成します。

   ```bash
   magento-cloud snapshot:create --live
   ```

   または、 `magento-cloud backup` ショートコマンド。 The `--live` オプションを使用すると、ダウンタイムを回避するために環境が稼働したままになります。 オプションの完全なリストを表示するには、次を入力します。 `magento-cloud snapshot:create --help`.

   レスポンスのサンプル：

   ```terminal
   Creating a snapshot of develop-branch
   Waiting for the activity ID (User created a backup of develop-branch):
   
   Creating backup of develop-branch
   Created backup my-snapshot
   [============================] 45 secs (complete)
   Activity ID succeeded
   Snapshot name: my-snapshot
   ```

1. 最新のスナップショットを確認します。

   ```bash
   magento-cloud snapshot:list
   ```

   リストには、スナップショットのステータスに関する情報が返されます。

   ```terminal
   Snapshots on the project (project-id), environment develop-branch (type: development):
   +---------------------------+----------------------+------------+
   | Created                   | Snapshot ID          | Restorable |
   +---------------------------+----------------------+------------+
   | 2023-03-08T17:07:01+00:00 | my-snapshot          | true       |
   +---------------------------+----------------------+------------+
   ```

## 手動バックアップの復元

必要な機能は次のとおりです。 [管理者アクセス](../project/user-access.md) を環境に追加します。 次の条件を満たしています： **七日** から _復元_ 手動バックアップ。 バックアップを復元しても、現在の Git ブランチのコードは変更されません。 この方法でのバックアップの復元は、Pro のステージング環境および実稼動環境には適用されません。詳しくは、 [プロバックアップと災害復旧](../architecture/pro-architecture.md#backup-and-disaster-recovery).

復元時間は、データベースのサイズに応じて異なります。

- 大規模なデータベース（200 GB 以上）には 5 時間かかる場合があります
- 中程度のデータベース (150 GB) には、2 時間半に 2 時間かかる場合があります
- 小規模なデータベース (60 GB) は 1 時間かかる場合があります

>[!TIP]
>
>バックアップなしでの復元：
>
>- 環境内の以前のコードに戻す、または追加された拡張機能を削除するには、 [コードのロールバック](#roll-back-code).
>- 不安定な環境を復元するには、次の手順を実行します。 _not_ バックアップを取っている場合は、 [環境の復元](../development/restore-environment.md).

**を使用してバックアップを復元するには[!DNL Cloud Console]**:

1. にログインします。 [[!DNL Cloud Console]](https://console.adobecommerce.com).
1. プロジェクトナビゲーションバーから環境を選択します。
1. Adobe Analytics の _バックアップ_ ビュー、次の中からバックアップを選択します： _保存済み_ リスト。 バックアップ機能では、 **not** を Pro 環境に適用します。
1. Adobe Analytics の ![その他](../../assets/icon-more.png){width="32"} (_その他_) メニューをクリックします。 **復元**.
1. 「バックアップからの復元」の情報を確認し、 **はい、復元します**.

**Cloud CLI を使用してスナップショットを復元するには**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。
1. 復元する環境ブランチを確認します。
1. 使用可能なすべてのスナップショットをリストします。

   ```bash
   magento-cloud snapshot:list
   ```

   リストには、使用可能なスナップショットに関する情報が返されます。

   ```terminal
   Snapshots on the project (project-id), environment develop-branch (type: development):
   +---------------------------+----------------------+------------+
   | Created                   | Snapshot ID          | Restorable |
   +---------------------------+----------------------+------------+
   | 2023-03-08T17:07:01+00:00 | my-snapshot          | true       |
   +---------------------------+----------------------+------------+
   ```

1. リストからスナップショット ID を使用してスナップショットをリストアします。

   ```bash
   magento-cloud snapshot:restore <snapshot-id>
   ```

## コードのロールバック

バックアップとスナップショットの実行 _not_ コードのコピーを含めます。 コードは既に Git ベースのリポジトリに保存されているので、Git ベースのコマンドを使用してコードをロールバック（または元に戻す）できます。 例えば、 `git log --oneline` 前のコミットまでスクロールし、次に [`git revert`](https://git-scm.com/docs/git-revert) 特定のコミットからコードを復元する場合。

また、 _非アクティブ_ 分岐。 使用する代わりに git コマンドを使用してブランチを作成 `magento-cloud` コマンド。 詳しくは、 [Git コマンド](../dev-tools/cloud-cli-overview.md#git-commands) （ Cloud CLI のトピック）を参照してください。
