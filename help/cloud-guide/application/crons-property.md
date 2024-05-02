---
title: Crons プロパティ
description: で「cron」プロパティを設定する方法の例を参照 [!DNL Commerce] アプリケーション設定ファイル。
feature: Cloud, Configuration
exl-id: 67d592c1-2933-4cdf-b4f6-d73cd44b9f59
source-git-commit: 1c0e05c3d8461bea473bcf6ec35162d65ef2774f
workflow-type: tm+mt
source-wordcount: '1069'
ht-degree: 0%

---

# Crons プロパティ

Adobe Commerceはを使用します `crons` ライン型活動を計画するプロパティ。 1 日の特定の時間に実行する特定のタスクをスケジュールするのに最適です。 読み取り専用環境の性質上、クラウドインフラストラクチャプロジェクト上のAdobe Commerceの web インスタンスでは一度に 1 つの cron ジョブのみを実行できます。 長時間実行されるタスクをキューに入れられた小さなタスクに分類することをお勧めします。 または、 [ワーカーインスタンス](workers-property.md).

Adobeでは、を実行することをお勧めします `crons` as the [ファイルシステム所有者](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/prerequisites/file-system/configure-permissions.html). 実行 _ではない_ 実行 `crons` as `root` または web サーバーユーザーとして設定します。

この設定は、複数のデフォルト cron ジョブを持つAdobe Commerceのオンプレミスデプロイメントとは異なります。 参照： [Cron ジョブの設定](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cli/configure-cron-jobs.html) が含まれる _設定ガイド_.

## cron ジョブの設定

この `crons` プロパティは、スケジュールに従ってトリガーされるプロセスを記述します。 各ジョブには、名前と次のオプションが必要です。

- `spec`- スケジュールに使用する cron 式。
- `cmd` – 実行するコマンド `start` および `stop`.
- `shutdown_timeout` – （_オプション_） Cron ジョブがキャンセルされた場合、これはジョブまたはプロセスを停止するために SIGKILL シグナルが送信されるまでの秒数です。 デフォルト値は 10 秒です。
- `timeout` – （_オプション_） Cron ジョブがタイムアウトまでに実行できる最大時間。 デフォルトでは、許容される最大値の 86400 秒（24 時間）に設定されます。

デフォルトでは、すべてのCommerce クラウドプロジェクトに次のデフォルトがあります `crons` での設定 `.magento.app.yaml` ファイル：

```yaml
crons:
    cronrun:
        spec: "* * * * *"
        cmd: "php bin/magento cron:run"
```

プロジェクトでカスタム cron ジョブが必要な場合は、デフォルトに追加できます `crons` 設定。 参照： [Cron ジョブの作成](#build-a-cron-job).

### `crontab`

Adobe Commerceは、セルフサービスをサポートするために、Pro プロジェクトにのみ自動クローン設定オプションを追加しました `crons` ステージング環境と実稼動環境での設定。 このオプションが有効になっている場合は、 `crontab` :cron 設定を確認します。 これはです _ではない_ スタータープロジェクトで使用できます。

を使用できますが、 `crontab` pro プロジェクトの設定をレビューするために、Adobe Commerceはを使用しません `crontab` クラウドインフラストラクチャにデプロイされたサイトに対して cron ジョブを実行する。

**Pro 環境での cron 設定を確認するには：**:

1. 使用方法 [SSH](../development/secure-connections.md#use-an-ssh-command) リモート環境にログインします。

1. スケジュールされた cron プロセスを一覧表示します。

   ```shell
   crontab -l
   ```

   >[!NOTE]
   >
   >次の場合 `crontab -l` command は以下を返します。 `Command not found` エラー（ステージング環境および実稼動環境のみ）が発生した場合、次の操作を行う必要があります [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) でプロジェクトの自動クローン セルフサービス設定オプションを有効にします。

次の例は、 `crontab` デフォルトのみを持つ環境の出力 `crons` 設定：

```terminal
username@hostname:~$ crontab -l
# Crontab is managed by the system, attempts to edit it directly will fail.
SHELL=/etc/platform/6fck2obu3244c/cron-run
MAILTO=""

# m h  dom mon dow  job_name

* * * * *           cronrun
```

## Cron ジョブの作成

cron ジョブには、スケジュールとタイミングの仕様、およびスケジュールされた時刻に実行するコマンドが含まれます。 スターター環境および Pro の場合 `integration` 環境では、最小間隔は 5 分に 1 回です。 ステージング環境および実稼動環境の場合、最小間隔は 1 分あたり 1 回です。 クラウドインフラストラクチャー上のAdobe Commerceでは、カスタム cron ジョブをに追加します `.magento.app.yaml` 内のファイル `crons` セクション。 一般的な形式は次のとおりです。 `spec` スケジュール用および `cmd` をクリックして、実行するコマンドまたはカスタム スクリプトを指定します。

### 仕様

Adobe Commerceでは、5 つの値から成る式を `crons` 仕様（仕様）: `* * * * *`

1. 分（0～59）すべての Starter 環境および Pro 環境で、cron ジョブでサポートされる最小頻度は 5 分です。 管理者による設定が必要になる場合があります。
2. 時間（0 ～ 23）
3. 日付（1 ～ 31）
4. 月（1 ～ 12）
5. 曜日（0～6）（日曜日～土曜日、一部のシステムでは 7 も日曜日）

次に例を示します。

- `00 */3 * * *` 3 時間ごとに最初の分（午前 12 時、午前 3 時、午前 6 時）に実行
- `20 */8 * * *` 8 時間ごとに分 20 に実行（午前 12 時 20 分、午前 8 時 20 分、午後 4 時 20 分）
- `00 00 * * *` 1 日 1 回深夜に実行
- `00 * * * 1` 週に 1 回、月曜日の深夜に実行します。

>[!NOTE]
>
>この `crons` で指定された時間 `.magento.app.yaml` ファイルは、データベースのストア設定値で指定されたタイムゾーンではなく、サーバーのタイムゾーンに基づいています。

スケジュールを決定する際は、タスクの完了に要する時間を考慮します。 例えば、3 時間ごとにジョブを実行し、タスクが完了するまでに 40 分かかる場合は、スケジュールされたタイミングの変更を検討できます。

### コマンド

この `cmd` 実行するコマンドまたはカスタム スクリプトを指定します。 コマンドスクリプトの形式には、次のものが含まれます。

```text
<path-to-php-binary> <project-dir>/<script-command>
```

例：

```yaml
crons:
    spec: "00 */8 * * *"
    cmd: "/usr/bin/php /app/abc123edf890/bin/magento export:start catalog_category_product"
```

この例では、 `<path-to-php-binary>` 等しい `/usr/bin/php`. インストールディレクトリ（プロジェクト ID が含まれています） `/app/abc123edf890/bin/magento`。スクリプトのアクションはです。 `export:start catalog_category_product`.

### プロジェクトへのカスタム cron ジョブの追加

クラウドインフラストラクチャプラットフォーム上のAdobe Commerceでは、のカスタマイズを以下に追加できます `crons` の節 [`.magento.app.yaml`](../application/configure-app-yaml.md) ファイル。

>[!NOTE]
>
>スターター環境および Pro の場合 `integration` 環境では、最小間隔は 5 分に 1 回です。 ステージング環境および実稼動環境の場合、最小間隔は 1 分あたり 1 回です。 デフォルトの最小間隔よりも頻繁な間隔を設定することはできません。

Adobe Commerce Pro プロジェクトでは、 [オートクロン機能](#set-up-cron-jobs) を使用してステージング環境と実稼動環境にカスタム cron ジョブを追加するには、まずプロジェクトで有効にする必要があります `.magento.app.yaml` ファイル。 この機能が有効になっていない場合、 [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) をクリックして自動クローンを有効にします。

**カスタム cron ジョブの追加手順**:

1. ローカル開発環境で、を編集します `.magento.app.yaml` Adobe Commerceのファイル `/app` ディレクトリ。

1. が含まれる `crons` セクションでは、次の形式を使用してカスタマイズを追加します。

   ```yaml
   crons:
       <cron_name_1>:
           spec: "<schedule_time>"
           cmd: "<schedule_command>"
       <cron_name_2>:
           spec: "<schedule_time>"
           cmd: "<schedule_command>"
   ```

   次の例では、 `productcatalog` ジョブは、製品カタログを 8 時間ごとに、1 時間後 20 分後に書き出します。

   ```yaml
   crons:
       magento:
           spec: '* * * * *'
           cmd: 'php bin/magento cron:run'
       productcatalog:
           spec: '20 */8 * * *'
           cmd: 'bin/magento export:start catalog_product_category'
   ```

1. コードの変更を追加、コミットおよびプッシュします。

   ```bash
   git add .magento.app.yaml && git commit -m "cron config updates" && git push origin <branch-name>
   ```

### cron ジョブの更新

カスタマイズされたジョブを追加、削除、または更新するには、 `crons` の節 `.magento.app.yaml` ファイル。 次に、リモートでアップデートをテストします `integration` ステージング環境と実稼動環境に変更をプッシュする前の環境。

## Cron ジョブの無効化

パフォーマンスの問題を防ぐために、インデックスの再作成やキャッシュのクリーニングなどのメンテナンスタスクを完了する前に、cron ジョブを手動で無効にすることができます。 を使用できます `ece-tools` CLI コマンド `cron:disable` ：すべての cron ジョブを無効化し、アクティブな cron プロセスを停止します。

**Cron ジョブを無効にするには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. SSH を使用してリモート環境にログインします。

   ```bash
   magento-cloud ssh
   ```

1. cron ジョブを無効にし、アクティブな cron プロセスを停止します。

   ```shell
   ./vendor/bin/ece-tools cron:disable
   ```

1. 必要なメンテナンスタスクを完了したら、cron ジョブを再度有効にします。

   ```shell
   ./vendor/bin/ece-tools cron:enable
   ```

## cron ジョブのトラブルシューティング

Adobeは、クラウドインフラストラクチャプラットフォーム上のAdobe Commerce上で cron 処理を最適化し、cron 関連の問題を修正するために、Adobe Commerce on cloud infrastructure パッケージを更新しました。 Cron 処理で問題が発生した場合は、プロジェクトで最新バージョンの `ece-tools` パッケージ。 参照： [ECE ツールの更新](../dev-tools/update-package.md).

各環境のアプリケーションレベルのログファイルで、cron 処理情報を確認できます。 参照： [アプリケーションログ](../test/log-locations.md#application-logs).

cron 関連の問題のトラブルシューティングについては、次のAdobe Commerce サポート記事を参照してください。

- [Cron タスクは他のグループからタスクをロック](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/cron-tasks-lock-tasks-from-other-groups.html)

- [クラウドでスタックした cron ジョブを手動でリセット](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/how-to/reset-stuck-magento-cron-jobs-manually-on-cloud.html)
