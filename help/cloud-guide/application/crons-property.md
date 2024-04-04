---
title: Crons プロパティ
description: で「crons」プロパティを設定する方法の例を参照してください。 [!DNL Commerce] アプリケーション設定ファイル。
feature: Cloud, Configuration
exl-id: 67d592c1-2933-4cdf-b4f6-d73cd44b9f59
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '1063'
ht-degree: 0%

---

# Crons プロパティ

Adobe Commerceは `crons` プロパティを使用して、ライン型アクティビティをスケジュールします。 特定のタスクを特定の時間に実行するようにスケジュールするのに最適です。 読み取り専用環境の性質上、クラウドインフラストラクチャプロジェクト上のAdobe Commerceの Web インスタンスで一度に実行できる cron ジョブは 1 つだけです。 長時間実行されるタスクを、キュー内の小さなタスクに分類することをお勧めします。 または、 [ワーカーインスタンス](workers-property.md).

Adobeでは、 `crons` として [ファイルシステム所有者](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/prerequisites/file-system/configure-permissions.html). 実行 _not_ 実行 `crons` as `root` または Web サーバーユーザーとして。

この設定は、複数のデフォルトの cron ジョブを持つAdobe Commerceのオンプレミスデプロイメントとは異なります。 詳しくは、 [cron ジョブの設定](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cli/configure-cron-jobs.html) （内） _設定ガイド_.

## Cron ジョブの設定

The `crons` プロパティは、スケジュールに従ってトリガーされるプロセスを表します。 各ジョブには、名前と次のオプションが必要です。

- `spec` — スケジュールに使用する Cron 式。
- `cmd` — 実行するコマンド `start` および `stop`.
- `shutdown_timeout`—(_オプション_) cron ジョブがキャンセルされた場合、SIGKILL シグナルが送信されてジョブまたはプロセスが停止されるまでの秒数です。 デフォルト値は 10 秒です。
- `timeout`—(_オプション_) タイムアウトまでに cron ジョブを実行できる最大時間です。 デフォルト値は86400秒（24 時間）の最大値です。

デフォルトでは、すべての Commerce クラウドプロジェクトには次のデフォルトが設定されています `crons` 設定 `.magento.app.yaml` ファイル：

```yaml
crons:
    cronrun:
        spec: "* * * * *"
        cmd: "php bin/magento cron:run"
```

プロジェクトでカスタム cron ジョブが必要な場合は、それらをデフォルトのに追加できます `crons` 設定。 詳しくは、 [cron ジョブの作成](#build-a-cron-job).

### `crontab`

Adobe Commerceは、セルフサービスをサポートするために、Pro プロジェクトにのみ自動 Cron 設定オプションを追加しました。 `crons` ステージング環境と実稼動環境の設定 このオプションが有効な場合、 `crontab` をクリックして、cron 設定を確認します。 これは _not_ スタータープロジェクトで使用できます。

ただし、 `crontab` Pro プロジェクトの設定を確認する場合、Adobe Commerceは `crontab` クラウドインフラストラクチャにデプロイされたサイトに対して cron ジョブを実行する場合。

**Pro 環境で cron 設定を確認するには**:

1. 用途 [SSH](../development/secure-connections.md#use-an-ssh-command) リモート環境にログインします。

1. スケジュールされた Cron プロセスのリストを表示します。

   ```shell
   crontab -l
   ```

   >[!NOTE]
   >
   >次の場合、 `crontab -l` コマンドは、 `Command not found` エラーが発生し、 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) をクリックして、Pro プロジェクトで自動 crons セルフサービス設定オプションを有効にします。

次の例は、 `crontab` デフォルトの `crons` 設定：

```terminal
username@hostname:~$ crontab -l
# Crontab is managed by the system, attempts to edit it directly will fail.
SHELL=/etc/platform/6fck2obu3244c/cron-run
MAILTO=""

# m h  dom mon dow  job_name

* * * * *           cronrun
```

## cron ジョブの作成

cron ジョブには、スケジュールとタイミングの指定と、スケジュールされた時刻に実行するコマンドが含まれます。 スターター環境および Pro の場合 `integration` 環境の場合、最小間隔は 5 分に 1 回です。 Pro のステージング環境および実稼動環境の場合、最小間隔は 1 分に 1 回です。 クラウドインフラストラクチャ上のAdobe Commerceで、カスタム cron ジョブを `.magento.app.yaml` ファイルを `crons` 」セクションに入力します。 一般的な形式はです。 `spec` スケジュール設定および `cmd` をクリックして、実行するコマンドまたはカスタムスクリプトを指定します。

### 仕様

Adobe Commerceでは、 `crons` 仕様（仕様）: `* * * * *`

1. 分 (0 ～ 59) すべての Starter 環境および Pro 環境で、cron ジョブでサポートされる最小頻度は 5 分です。 管理者で設定を行う必要が生じる場合があります。
2. 時間 (0 ～ 23)
3. 日付 (1 ～ 31)
4. 月 (1 ～ 12)
5. 曜日 (0 ～ 6)（日曜日から土曜日、7 は一部のシステムでは日曜日も含まれます）

次に例を示します。

- `00 */3 * * *` は、最初の分（午前 12:00、午前 3:00、午前 6:00）に 3 時間ごとに実行されます。
- `20 */8 * * *` は 8 時間ごと（午前 12:20、午前 8:20、午後 4:20）に実行されます。
- `00 00 * * *` 1 日 1 日午前 0 時に実行
- `00 * * * 1` 週に 1 回、月曜日の午前 0 時に実行されます。

>[!NOTE]
>
>The `crons` 指定された時間 `.magento.app.yaml` ファイルは、データベースのストア設定値で指定されたタイムゾーンではなく、サーバーのタイムゾーンに基づいています。

スケジュールを決定する際は、タスクの完了に要する時間を考慮します。 例えば、ジョブを 3 時間ごとに実行し、タスクの完了に 40 分かかる場合は、スケジュールされたタイミングの変更を検討する必要があります。

### コマンド

The `cmd` 実行するコマンドまたはカスタムスクリプトを指定します。 コマンドスクリプト形式には、次のものを含めることができます。

```text
<path-to-php-binary> <project-dir>/<script-command>
```

例：

```yaml
crons:
    spec: "00 */8 * * *"
    cmd: "/usr/bin/php /app/abc123edf890/bin/magento export:start catalog_category_product"
```

この例では、 `<path-to-php-binary>` 次に該当 `/usr/bin/php`. インストールディレクトリ。プロジェクト ID はです。 `/app/abc123edf890/bin/magento`スクリプトアクションが `export:start catalog_category_product`.

### プロジェクトにカスタム cron ジョブを追加する

クラウドインフラストラクチャプラットフォーム上のAdobe Commerceで、 `crons` のセクション [`.magento.app.yaml`](../application/configure-app-yaml.md) ファイル。

>[!NOTE]
>
>スターター環境および Pro の場合 `integration` 環境の場合、最小間隔は 5 分に 1 回です。 Pro のステージング環境および実稼動環境の場合、最小間隔は 1 分に 1 回です。 デフォルトの最小値より頻繁に間隔を設定することはできません。

Adobe Commerce Pro プロジェクトでは、 [自動クロン機能](#set-up-cron-jobs) を使用してステージング環境と実稼動環境にカスタム cron ジョブを追加する前に、プロジェクトで有効にする必要があります。 `.magento.app.yaml` ファイル。 この機能が有効になっていない場合、 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) をクリックして、自動 cron を有効にします。

**カスタム cron ジョブを追加するには**:

1. ローカル開発環境で、 `.magento.app.yaml` Adobe Commerceのファイル `/app` ディレクトリ。

1. Adobe Analytics の `crons` セクションで、次の形式を使用してカスタマイズを追加します。

   ```yaml
   crons:
       <cron_name_1>:
           spec: "<schedule_time>"
           cmd: "<schedule_command>"
       <cron_name_2>:
           spec: "<schedule_time>"
           cmd: "<schedule_command>"
   ```

   次の例では、 `productcatalog` ジョブは、1 時間後の 8 時間ごと、20 分ごとに商品カタログを書き出します。

   ```yaml
   crons:
       magento:
           spec: '* * * * *'
           cmd: 'php bin/magento cron:run'
       productcatalog:
           spec: '20 */8 * * *'
           cmd: 'bin/magento export:start catalog_product_category'
   ```

1. コードの変更を追加、コミット、およびプッシュします。

   ```bash
   git add .magento.app.yaml && git commit -m "cron config updates" && git push origin <branch-name>
   ```

### cron ジョブの更新

カスタマイズしたジョブを追加、削除、または更新するには、 `crons` のセクション `.magento.app.yaml` ファイル。 次に、リモートで更新をテストします。 `integration` 環境を使用して、変更をステージング環境と実稼動環境にプッシュする必要があります。

## cron ジョブを無効にする

パフォーマンスの問題を防ぐために、インデックスの再作成やキャッシュのクリーニングなどのメンテナンスタスクを完了する前に、cron ジョブを手動で無効にすることができます。 以下を使用すると、 `ece-tools` CLI コマンド `cron:disable` ：すべての cron ジョブを無効にし、アクティブな cron プロセスを停止します。

**cron ジョブを無効にするには**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. SSH を使用してリモート環境にログインします。

   ```bash
   magento-cloud ssh
   ```

1. cron ジョブを無効にし、アクティブな cron プロセスを停止します。

   ```shell
   ./vendor/bin/ece-tools cron:disable
   ```

1. 必要なメンテナンスタスクを完了したら、必ず cron ジョブを再度有効にしてください。

   ```shell
   ./vendor/bin/ece-tools cron:enable
   ```

## cron ジョブのトラブルシューティング

Adobeは、クラウドインフラストラクチャプラットフォーム上のAdobe Commerceでの cron 処理を最適化し、cron に関連する問題を修正するために、クラウドインフラストラクチャパッケージ上のAdobe Commerceを更新しました。 cron 処理で問題が発生した場合は、プロジェクトで最新バージョンの `ece-tools` パッケージ。 詳しくは、 [ECE-Tools を更新](../dev-tools/update-package.md).

各環境のアプリケーションレベルのログファイルで、cron 処理情報を確認できます。 詳しくは、 [アプリケーションログ](../test/log-locations.md#application-logs).

cron 関連の問題のトラブルシューティングについては、次のAdobe Commerceサポート記事を参照してください。

- [Cron タスクは他のグループからのタスクをロックします](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/cron-tasks-lock-tasks-from-other-groups.html)

- [クラウド上で動かない cron ジョブを手動でリセット](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/how-to/reset-stuck-magento-cron-jobs-manually-on-cloud.html)
