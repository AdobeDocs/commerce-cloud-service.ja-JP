---
title: パッチの適用
description: クラウドインフラストラクチャプロジェクト上のAdobe Commerceでパッチを適用する方法を説明します。
feature: Cloud, Upgrade
exl-id: a7bf672f-7b89-45cd-8436-e885bca9029d
source-git-commit: e67d3259b1b5195147e4e441fe9efd82e48241ab
workflow-type: tm+mt
source-wordcount: '856'
ht-degree: 0%

---

# パッチの適用

[コマース用クラウドパッチ](https://github.com/magento/magento-cloud-patches) そして [クォリティパッチツール](https://github.com/magento/quality-patches) インストールしたAdobe Commerceアプリケーションにパッチを配信する。

- コマース用クラウドパッチパッケージは、重要な修正を含む必要なパッチを提供します。
- 品質パッチは、オプションの低影響品質の修正を次のように提供します。 [個々のパッチ](https://experienceleague.adobe.com/docs/commerce-operations/release/planning/versioning-policy.html#individual-patch) 下位互換性のない変更を含まない

詳しくは、 [利用可能なパッチ](https://experienceleague.adobe.com/tools/commerce-quality-patches/index.html) （内） _Commerce Operations Tools ガイド_ リリース済みのパッチの完全なリストを確認する。

どちらのパッケージも、すべてのAdobe Commerceバージョンとクラウド環境の統合を強化し、重要な修正、オプションの修正、カスタム修正をすばやく提供します。 これらのパッケージを使用して、Commerce で使用可能なすべての個々のパッチに関する一般情報を適用、元に戻し、表示できます。

>[!TIP]
>
>以下を使用すると、 [クォリティパッチツール](https://experienceleague.adobe.com/tools/commerce-quality-patches/index.html) Magento Open SourceおよびAdobe Commerceプロジェクト用のスタンドアロンパッケージとしてのコマース用クラウドパッチ。 非クラウドプロジェクトには、品質パッチツールを使用することをお勧めします。

リモート環境に変更をデプロイする場合、 `ece-tools` パッケージ使用 `magento/magento-cloud-patches` および `magento/quality-patches` 保留中のパッチをチェックし、次の順序で自動的に適用するには、次の手順に従います。

1. コマース用クラウドパッチパッケージに含まれているすべての必須コマースパッチを適用します。
1. 品質パッチツールに含まれる、選択したオプションのコマースパッチを適用します。
1. でカスタムパッチを適用する `/m2-hotfixes` ディレクトリをパッチ名順にアルファベット順に表示します。

>[!NOTE]
>
>を更新する際に、 `ece-tools` パッケージまたはコマース用クラウドパッチパッケージでは、次回プロジェクトをデプロイする際に最新の必要なパッチが適用されます。または、 `ece-patches apply` CLI コマンドを使用し、クラウド環境を再デプロイします。 スキップできません [必要なパッチ](https://github.com/magento/magento-cloud-patches/tree/develop/patches) を設定します。

## 前提条件

{{upgrade-tip}}

品質パッチツールは、コマース用のクラウドパッチと `ece-tools` パッケージ。 最新のパッチを適用するには、次の条件を満たす必要があります。 [ECE-Tools の最新バージョン](../dev-tools/update-package.md) インストール済み ECE-Tools の最低限必要なバージョンは 2002.1.2 です。

## 使用可能なパッチとステータスを表示

使用可能な個々のパッチのリストを表示するには、次の手順に従います。

```bash
php ./vendor/bin/ece-patches status
```

レスポンスのサンプル：

```terminal
More detailed information about patches you can find on https://support.magento.com/
╔════════════════╤═════════════════════════════════════════════════╤══════════╤═════════════╤═════════════════════════════════╗
║ Id             │ Title                                           │ Type     │ Status      │ Details                         ║
╠════════════════╪═════════════════════════════════════════════════╪══════════╪═════════════╪═════════════════════════════════╣
║ MAGECLOUD-5069 │ FPC is getting disabled during deployments      │ Required │ Applied     │ Affected components:            ║
║                │                                                 │          │             │  - magento/module-page-cache    ║
╟────────────────┼─────────────────────────────────────────────────┼──────────┼─────────────┼─────────────────────────────────╢
║ MCLOUD-5650    │ Hold deployment config after reading from file  │ Required │ Applied     │ Affected components:            ║
║                │                                                 │          │             │  - magento/framework            ║
╟────────────────┼─────────────────────────────────────────────────┼──────────┼─────────────┼─────────────────────────────────╢
║ MCLOUD-5684    │ Pagination Not working - product_list_limit=all │ Required │ Applied     │ Affected components:            ║
║                │                                                 │          │             │  - magento/module-elasticsearch ║
╟────────────────┼─────────────────────────────────────────────────┼──────────┼─────────────┼─────────────────────────────────╢
║ MC-65837       │ Fix load balancer issue                         │Deprecated│ Applied     │ Recommended replacement: MC-1   ║
║                │                                                 │          │             │ Affected components:            ║
║                │                                                 │          │             │  - magento/framework            ║
╟────────────────┼─────────────────────────────────────────────────┼──────────┼─────────────┼─────────────────────────────────╢
║ BUNDLE-2554    │ Set Payment info bug                            │ Required │ Not applied │ Affected components:            ║
║                │                                                 │          │             │  - amzn/amazon-pay-module       ║
╟────────────────┼─────────────────────────────────────────────────┼──────────┼─────────────┼─────────────────────────────────╢
║ MC-1           │ Fixes issue 1                                   │ Optional │ Applied     │ Affected components:            ║
║                │                                                 │          │             │  - magento/module-cms           ║
╟────────────────┼─────────────────────────────────────────────────┼──────────┼─────────────┼─────────────────────────────────╢
║ MC-2           │ Fixes issue 2                                   │ Optional │ Not applied │ Affected components:            ║
║                │                                                 │          │             │  - magento/module-cms           ║
╟────────────────┼─────────────────────────────────────────────────┼──────────┼─────────────┼─────────────────────────────────╢
║ MC-3           │ Fixes issue 3                                   │ Optional │ Not applied │ Required patches:               ║
║                │                                                 │          │             │  - MC-2                         ║
║                │                                                 │          │             │ Affected components:            ║
║                │                                                 │          │             │  - magento/module-cms           ║
╟────────────────┼─────────────────────────────────────────────────┼──────────┼─────────────┼─────────────────────────────────╢
║ N/A            │ ../m2-hotfixes/MDVA_custom__2.3.5_ce.patch      │ Custom   │ N/A         │ Affected components:            ║
║                │                                                 │          │             │  - magento/module-framework     ║
╚════════════════╧═════════════════════════════════════════════════╧══════════╧═════════════╧═════════════════════════════════╝
Magento 2 Enterprise Edition, version 2.3.5.0
```

ステータステーブルには、次のタイプの情報が含まれます。

- **タイプ**:
   - `Optional`- Quality Patches Tool と Cloud Patches パッケージのすべてのパッチは、Adobe CommerceおよびMagento Open Sourceインストールではオプションです。 クラウドインフラストラクチャ上のAdobe Commerceの場合、すべてのパッチはオプションです。
   - `Required`- Cloud のお客様は、コマース用のクラウドパッチパッケージのすべてのパッチが必要です。
   - `Deprecated` — 個々のパッチは非推奨とマークされ、適用済みの場合は元に戻すことをお勧めします。 非推奨のパッチを元に戻すと、そのパッチはステータステーブルに表示されなくなります。
   - `Custom`—&#39;m2-hotfixes&#39;ディレクトリのすべてのパッチ。

- **ステータス**:
   - `Applied` — パッチが適用されています。
   - `Not applied` — パッチは適用されていません。
   - `N/A` — 競合のため、パッチのステータスを定義できません。

- **詳細**:
   - `Affected components` — 影響を受けるモジュールのリスト。
   - `Required patches` — 必要なパッチ（依存関係）のリスト。
   - `Recommended replacement` — 非推奨のパッチの推奨置き換えであるパッチ。

## ローカル環境でのパッチの適用

ローカル環境で手動でパッチを適用し、デプロイする前にテストすることができます。

**個々のパッチをローカル開発環境に適用するには**:

1. 「QUALITY_PATCH」変数を `.magento.env.yaml` ファイルを開き、その下に必要なパッチをリストします。

   ```yaml
   stage:
     build:
       QUALITY_PATCHES:
         - MCTEST-1002
         - MCTEST-1003
   ```

1. プロジェクトルートから、パッチを適用します。

   ```bash
   php ./vendor/bin/ece-patches apply
   ```

   The `ece-patches apply` コマンドは、次の順序でパッチを適用します。
   - 必要なパッチ
   - 個々のパッチ（オプション）
   - カスタムパッチを `/m2-hotfixes` directory

1. キャッシュをクリアします。

   ```bash
   php ./bin/magento cache:clean
   ```

1. パッチをテストし、必要に応じてカスタムパッチを変更します。

## リモート環境でのパッチの適用

>[!WARNING]
>
>実稼動環境にデプロイする前に、統合環境またはステージング環境ですべてのパッチをテストすることを強くお勧めします。

**リモート環境でパッチを適用するには**:

1. 次を追加： `QUALITY_PATCHES` 変数を `.magento.env.yaml` ファイルを開き、その下に必要なパッチをリストします。

   ```yaml
   stage:
     build:
       QUALITY_PATCHES:
         - MCTEST-1002
         - MCTEST-1003
   ```

   >[!NOTE]
   >
   >新しいバージョンのAdobe Commerceにアップグレードした後、パッチが新しいバージョンに含まれていない場合は、パッチを再適用する必要があります。

1. 更新された `.magento.env.yaml` ファイル。

   ```bash
   git add .magento.env.yaml
   ```

   ```bash
   git commit -m "Apply patch"
   ```

   ```bash
   git push origin <branch-name>
   ```

## カスタムパッチの適用

展開時に、ECE-Tools は、すべてのAdobeパッチと、に追加するカスタムパッチを適用します `/m2-hotfixes` プロジェクトルートのディレクトリ。

>[!NOTE]
>
>すべてのパッチファイル名は、 `.patch` 拡張子。

**クラウド環境にカスタムパッチを適用してテストするには**:

1. プロジェクトのルートに、という名前のディレクトリを作成します。 `m2-hotfixes` 存在しない場合

   ```bash
   mkdir m2-hotfixes
   ```

1. パッチファイルを `/m2-hotfixes` ディレクトリ。

1. コードの変更を追加、コミット、およびプッシュします。

   ```bash
   git add m2-hotfixes/
   ```

   ```bash
   git commit -m "Apply patch"
   ```

   ```bash
   git push origin <branch-name>
   ```

   >[!NOTE]
   >
   >実稼動前の環境で、必ずすべてのパッチをテストしてください。 クラウドインフラストラクチャ上のAdobe Commerceの場合、 `magento-cloud environment:branch <branch-name>` CLI コマンド。

## カスタムパッチを元に戻す

以前に適用したカスタムパッチを元に戻すかアンインストールするには：

1. からパッチファイルを削除します。 `/m2-hotfixes` ディレクトリ。

1. コードの変更を追加、コミット、およびプッシュします。

   ```bash
   git add m2-hotfixes/
   ```

   ```bash
   git commit -m "Revert patch"
   ```

   ```bash
   git push origin <branch-name>
   ```

   >[!NOTE]
   >
   >必ず実稼動前の環境でテストしてください。 クラウドインフラストラクチャ上のAdobe Commerceの場合、 `magento-cloud environment:branch <branch-name>` CLI コマンド。

## 非クラウドプロジェクトへのパッチの適用

以下を使用します。 [クォリティパッチツール](https://github.com/magento/quality-patches) Magento Open SourceとAdobe Commerceプロジェクト用。

## ローカル環境のパッチを元に戻す

を使用して、ローカル開発環境で以前に適用したすべてのパッチを元に戻すことができます。 `ece-patches` CLI。

適用されたすべてのパッチを元に戻すには：

```bash
php ./vendor/bin/ece-patches revert
```

このコマンドを実行すると、次の順序ですべてのパッチが元に戻されます。

- /m2-hotfixs ディレクトリから、適用されたすべてのカスタムパッチを元に戻します。
- 適用されたすべてのオプションの個々のパッチを元に戻します。
- 適用されたすべての必要なパッチを元に戻します。

## ログ

クォリティパッチツール (Quality Patches Tool) では、に対するすべての操作がログに記録されます。 `<Project_root>/var/log/patch.log` ファイル。
