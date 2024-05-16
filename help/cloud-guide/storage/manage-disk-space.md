---
title: ディスク容量の管理
description: コマンドラインインターフェイスを使用してディスク容量を管理する方法を説明します。
feature: Cloud, Storage
exl-id: 480cb33b-ac83-441d-946e-5b4de09ad84e
source-git-commit: 1253d8357fd2554050d1775fefbc420a2097db5f
workflow-type: tm+mt
source-wordcount: '693'
ht-degree: 0%

---

# ディスク容量の管理

クラウドプロジェクトの合計ストレージ容量は、クラウドインフラストラクチャ上のAdobe Commerce契約および [アカウントページ](https://accounts.magento.cloud/user). アカウントの各プロジェクトカードには、次の数が表示されます。 _環境_, _ストレージ_ 容量（GB 単位）、および _ユーザー_. または、次のクラウドコマンドを使用することもできます。

```bash
magento-cloud subscription:info | grep storage
```

応答の例：

```terminal
| storage              | 51200
```

実稼動環境またはステージング環境がストレージ容量の 95% に達するか、それを超えると、クラウドインフラストラクチャモニタリングツールは、ストレージ容量が自動的に増加したことを通知するサポートアラートをトリガーにします。

通知の例：

>[!BEGINSHADEBOX]

_「監視により、クラスター（project-id-environment）のファイルストレージがいっぱいになることが検出されました。 ディスク使用量は現在、残り 1 GiB 未満の重要な使用レベルにあります。 共有ストレージボリュームは、現在、サービスの稼働を維持するために、60 GiB から 70 GiB にアップサイズされています。 実稼働ファイルとステージングファイルの使用状況を確認して、領域を解放できるかどうかを確認してください。」_

>[!ENDSHADEBOX]

>[!TIP]
>
>このような自動的な増加を避けるために、ストレージ容量を定期的に監視し、90% 未満で十分に維持することをお勧めします。 一度割り当てると、ステージングおよび実稼働用のストレージの増加を元に戻すことはできません。

## 統合環境を確認

統合環境のディスクスペースの使用状況は、次を使用して確認できます `magento-cloud` CLI。

**ディスク容量の概算使用量を確認するには**:

```bash
magento-cloud db:size
```

応答の例：

```terminal
Checking database service mysql...

+----------------+-----------------+--------+
| Allocated disk | Estimated usage | % used |
+----------------+-----------------+--------+
| 2.0 GiB        | 193.3 MiB       | ~ 9%   |
+----------------+-----------------+--------+
```

すべてのマウントはディスクを共有します。 を使用して、マウントに使用するディスク容量を確認できます。 `magento-cloud` CLI。

**マウントのディスク・スペースの概算使用量を確認するには**:

```bash
magento-cloud mount:size
```

応答の例：

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

## 専用クラスターの確認

ステージング環境および実稼動環境の場合は、を使用して、各環境でのディスク容量の使用状況を確認できます `disk free` コマンド：ファイル・システムが使用しているディスク容量を報告します。 リモート環境にログインするには、SSH を使用する必要があります。

```bash
df -h
```

この `-h` オプションは、人間が読み取れる形式（KB、MB または GB）でレポートを表示します。

次の応答例では、 `/mnt/shared` マウントは、メディアのディスク容量を表示し、 `/data/mysql/` mount は、データベースのディスク容量を示します。

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

応答の例：

```terminal
Filesystem                                    Size  Used Avail Use% Mounted on
192.168.5.5:/shared/project/var         9.8G  591M  9.3G   6% /app/project/var
```

## ディスク領域の割り当て

2 [設定ファイル](../environment/overview.md) クラウド環境でのディスク容量の割り当てを制御します。 `.magento.app.yaml` ファイルと `.magento/services.yaml` ファイル。 各ファイルには、 `disk` プロパティ。各設定のディスクサイズの値を MB 単位で定義します。 ディスク容量の割り当てを変更できるのは、Pro 統合およびスターター環境のみです。

>[!IMPORTANT]
>
>実稼動環境およびステージング環境の場合は、次の操作が必要です [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) ディスク領域の割り当てを変更します。 実稼動環境とステージング環境のサイズは特定の間隔でのみ増加するので、現在のディスク容量の使用状況に応じて、サポートはディスク容量の割り当てを最小 10 GB 増やすことをお勧めします。 一度割り当てると、ステージングおよび実稼働用のストレージの増加を元に戻すことはできません。

### アプリケーションのディスク容量

この `.magento.app.yaml` ファイルは、 [永続的なディスク容量](../application/properties.md#disk) アプリケーションで使用可能です。

**アプリケーションのディスク容量を増やすには**:

1. ローカル開発環境で、を開きます `.magento.app.yaml` 設定ファイル。

1. に新しい値を設定 `disk` プロパティ（MB 単位）。

   ```yaml
   disk: <value-mb>
   ```

1. 変更をファイルに保存します。

1. コードの変更を追加、コミット、プッシュします。

   ```bash
   git add .magento.app.yaml && git commit -m "Increase disk space for application" && git push origin <branch-name>
   ```

   変更は、更新された YAML ファイルをリモート環境にプッシュした後に有効になります。

### サービスディスク容量

この `.magento/services.yaml` ファイルは、MySQL や Redis などの各サービスで使用可能なディスク領域を制御します。

**サービスのディスク領域を増やすには**:

1. ローカル開発環境で、を開きます `.magento/services.yaml` 設定ファイル。

1. ファイルでサービスを追加または検索します。 参照： [サービス設定の詳細](../services/services-yaml.md).

1. ディスクプロパティの新しい値（MB 単位）を設定します。

   ```yaml
   <name>:
       type: <service-name>:<service-version>
       disk: <value-mb>
   ```

1. 変更をファイルに保存します。

1. コードの変更を追加、コミット、プッシュします。

   ```bash
   git add .magento/services.yaml && git commit -m "Increase disk space for service" && git push origin <branch-name>
   ```

   変更は、更新された YAML ファイルをリモート環境にプッシュした後に有効になります。

## ディスク容量の監視

Pro 実稼動環境では、New Relicの Managed alerts for Adobe Commerce アラートポリシーを使用して、ディスクスペースやその他のパフォーマンスインジケーターを監視できます。 詳しくは、を参照してください [管理されたアラートによるパフォーマンスの監視](../monitor/investigate-performance.md#monitor-performance-with-managed-alerts). 詳しくは、を参照してください [データベースのパフォーマンスの問題を解決するベストプラクティス](https://experienceleague.adobe.com/docs/commerce-operations/implementation-playbook/best-practices/maintenance/resolve-database-performance-issues.html).

## スペースが残っていません

ビルドキャッシュは、時間の経過と共に大きくなる可能性があります。 次のような警告が表示される場合： `No space left on device`で、ビルドキャッシュをクリアし、再デプロイしてみてください。

```bash
magento-cloud project:clear-build-cache
```
