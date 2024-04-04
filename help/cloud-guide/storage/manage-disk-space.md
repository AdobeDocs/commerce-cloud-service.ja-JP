---
title: ディスク容量の管理
description: コマンドラインインターフェイスを使用してディスク領域を管理する方法を説明します。
feature: Cloud, Storage
exl-id: 480cb33b-ac83-441d-946e-5b4de09ad84e
source-git-commit: 1253d8357fd2554050d1775fefbc420a2097db5f
workflow-type: tm+mt
source-wordcount: '693'
ht-degree: 0%

---

# ディスク容量の管理

クラウドインフラストラクチャ契約のAdobe Commerceおよび [アカウントページ](https://accounts.magento.cloud/user). アカウント内の各プロジェクトカードに、 _環境_、 _ストレージ_ 容量（GB 単位）、および _ユーザー_. または、次の Cloud コマンドを使用できます。

```bash
magento-cloud subscription:info | grep storage
```

レスポンスのサンプル：

```terminal
| storage              | 51200
```

Pro の実稼動環境またはステージング環境がストレージ容量の 95 %以上に達した場合、クラウドインフラストラクチャ監視ツールは、ストレージ容量の自動増加を通知するサポートアラートをトリガーします。

通知の例：

>[!BEGINSHADEBOX]

_「監視で、クラスター (project-id-environment) のファイルストレージがいっぱいに近づいていることが検出されました。 ディスク使用量は、現在、1 GiB 未満の重要な使用量レベルにあります。 現在、サービスを稼働状態に保つために、共有ストレージボリュームは 60 GiB から 70 GiB にアップサイズされています。 実稼動ファイルとステージングファイルの使用状況を見て、領域をクリアできるかどうかを確認してください。」_

>[!ENDSHADEBOX]

>[!TIP]
>
>ストレージ容量を定期的に監視し、90 %未満で適切に維持することをお勧めします。これにより、自動的な増加が回避されます。 一度割り当てると、Pro のステージングおよび実稼動環境でのストレージの増加を元に戻すことはできません。

## 統合環境を確認する

統合環境のディスク容量使用量は、 `magento-cloud` CLI。

**ディスク容量の使用量を概算するには**:

```bash
magento-cloud db:size
```

レスポンスのサンプル：

```terminal
Checking database service mysql...

+----------------+-----------------+--------+
| Allocated disk | Estimated usage | % used |
+----------------+-----------------+--------+
| 2.0 GiB        | 193.3 MiB       | ~ 9%   |
+----------------+-----------------+--------+
```

すべてのマウントは 1 台のディスクを共有します。 マウントのディスク容量使用量は、 `magento-cloud` CLI。

**マウントのディスク容量使用量の概算を確認するには**:

```bash
magento-cloud mount:size
```

レスポンスのサンプル：

```terminal
Checking disk usage for all mounts on <project>-<environment>-mymagento@ssh.us.magento.cloud...

+------------+-----------+---------+-----------+-----------+--------+
| Mount(s)   | Size(s)   | Disk    | Used      | Available | % Used |
+------------+-----------+---------+-----------+-----------+--------+
| app/etc    | 184 KiB   | 1.9 GiB | 481.3 MiB | 1.4 GiB   | 24.7%  |
| pub/media  | 128 KiB   |         |           |           |        |
| pub/static | 158.2 MiB |         |           |           |        |
| var        | 316.7 MiB |         |           |           |        |
+------------+-----------+---------+-----------+-----------+--------+
```

## 専用クラスターを確認

Pro ステージング環境および実稼動環境の場合は、 `disk free` コマンド：ファイルシステムが使用するディスク領域の量を表示します。 リモート環境にログインするには、SSH を使用する必要があります。

```bash
df -h
```

The `-h` 「 」オプションを選択すると、レポートが人間が読み取り可能な形式 (KB、MB、GB) で表示されます。

次のサンプル応答では、 `/mnt/shared` マウントは、メディアのディスク領域を示し、 `/data/mysql/` mount は、データベースのディスク容量を示します。

```terminal
Filesystem                                    Size  Used Avail Use% Mounted on
udev                                           16G     0   16G   0% /dev
tmpfs                                         3.2G  9.1M  3.2G   1% /run
/dev/xvda1                                     59G  8.9G   48G  16% /
tmpfs                                          16G   36K   16G   1% /dev/shm
tmpfs                                         5.0M     0  5.0M   0% /run/lock
tmpfs                                          16G     0   16G   0% /sys/fs/cgroup
/dev/xvdj                                     9.8G  2.3G  7.6G  23% /data/mysql
/dev/xvdi                                     9.8G  491M  9.3G   5% /data/exports
192.168.5.5:/shared                           9.8G  591M  9.3G   6% /mnt/shared
/dev/loop0                                     91M   91M     0 100% /app/project
192.168.5.5:/shared/project/var         9.8G  591M  9.3G   6% /app/project/var
192.168.5.5:/shared/project/app/etc     9.8G  591M  9.3G   6% /app/project/app/etc
192.168.5.5:/shared/project/pub/media   9.8G  591M  9.3G   6% /app/project/pub/media
192.168.5.5:/shared/project/pub/static  9.8G  591M  9.3G   6% /app/project/pub/static
```

ディレクトリを指定して、応答を制限できます。 例：

```bash
df -h var/
```

レスポンスのサンプル：

```terminal
Filesystem                                    Size  Used Avail Use% Mounted on
192.168.5.5:/shared/project/var         9.8G  591M  9.3G   6% /app/project/var
```

## ディスク容量の割り当て

2 [設定ファイル](../environment/overview.md) クラウド環境でのディスク領域の割り当てを制御します。 `.magento.app.yaml` ファイルと `.magento/services.yaml` ファイル。 各ファイルには、 `disk` プロパティ。各構成のディスクサイズ値を MB 単位で定義します。 Pro 統合環境と Starter 環境でのみ、ディスク容量の割り当てを変更できます。

>[!IMPORTANT]
>
>実稼動環境およびステージング環境の場合は、次の操作を行う必要があります。 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) ディスク容量の割り当てを変更する。 Pro Production 環境と Staging 環境のサイズの増加は、特定の間隔でのみ発生する可能性があるので、現在のディスク領域の使用量に応じて、ディスク領域の割り当てを最低 10 GB 増やすことをサポートで推奨します。 一度割り当てると、Pro のステージングおよび実稼動環境でのストレージの増加を元に戻すことはできません。

### アプリケーションのディスク容量

The `.magento.app.yaml` ファイルはを制御します [永続的なディスク容量](../application/properties.md#disk) アプリケーションで使用できます。

**アプリケーションのディスク容量を増やすには**:

1. ローカル開発環境で、 `.magento.app.yaml` 設定ファイル。

1. の新しい値を設定 `disk` プロパティ（MB 単位）。

   ```yaml
   disk: <value-mb>
   ```

1. ファイルに変更を保存します。

1. コードの変更を追加、コミット、およびプッシュします。

   ```bash
   git add .magento.app.yaml && git commit -m "Increase disk space for application" && git push origin <branch-name>
   ```

   変更は、更新した YAML ファイルをリモート環境にプッシュした後に有効になります。

### サービスディスク容量

The `.magento/services.yaml` ファイルは、MySQL や Redis など、各サービスで使用できるディスク容量を制御します。

**サービスのディスク容量を増やすには**:

1. ローカル開発環境で、 `.magento/services.yaml` 設定ファイル。

1. ファイル内のサービスを追加するか、検索します。 詳しくは、 [サービスの設定の詳細](../services/services-yaml.md).

1. ディスクプロパティの新しい値を（MB 単位で）設定します。

   ```yaml
   <name>:
       type: <service-name>:<service-version>
       disk: <value-mb>
   ```

1. ファイルに変更を保存します。

1. コードの変更を追加、コミット、およびプッシュします。

   ```bash
   git add .magento/services.yaml && git commit -m "Increase disk space for service" && git push origin <branch-name>
   ```

   変更は、更新した YAML ファイルをリモート環境にプッシュした後に有効になります。

## ディスク容量の監視

Pro 実稼動環境では、New RelicのAdobe Commerce用管理アラートポリシーを使用して、ディスク容量やその他のパフォーマンスインジケーターを監視できます。 詳しくは、 [管理されたアラートでパフォーマンスを監視する](../monitor/investigate-performance.md#monitor-performance-with-managed-alerts). 詳しいガイダンスについては、 [データベースのパフォーマンスの問題を解決するためのベストプラクティス](https://experienceleague.adobe.com/docs/commerce-operations/implementation-playbook/best-practices/maintenance/resolve-database-performance-issues.html).

## スペースが残っていません

ビルドキャッシュは、時間の経過と共に増やすことができます。 次のような警告が表示された場合： `No space left on device`ビルドキャッシュをクリアして、再デプロイしてみてください。

```bash
magento-cloud project:clear-build-cache
```
