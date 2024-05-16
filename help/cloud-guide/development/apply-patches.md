---
title: パッチの適用
description: Adobe Commerce on cloud infrastructure プロジェクトにパッチを適用する方法を説明します。
feature: Cloud, Upgrade
exl-id: a7bf672f-7b89-45cd-8436-e885bca9029d
source-git-commit: e67d3259b1b5195147e4e441fe9efd82e48241ab
workflow-type: tm+mt
source-wordcount: '856'
ht-degree: 0%

---

# パッチの適用

[Commerceのクラウドパッチ](https://github.com/magento/magento-cloud-patches) および [品質向上パッチツール](https://github.com/magento/quality-patches) インストールしたAdobe Commerce アプリケーションにパッチを配信します。

- Commerce用クラウドパッチ パッケージは、重要な修正を含む必要なパッチを提供します
- 品質パッチは、影響の少ないオプションの品質修正を次の形式で提供します [個々のパッチ](https://experienceleague.adobe.com/docs/commerce-operations/release/planning/versioning-policy.html#individual-patch) 後方互換性のない変更を含まない

参照： [使用可能なパッチ](https://experienceleague.adobe.com/tools/commerce-quality-patches/index.html) が含まれる _Commerce操作ツールガイド_ リリースされたパッチの完全なリストを確認します。

どちらのパッケージも、すべてのAdobe Commerce バージョンとクラウド環境の統合を強化し、重要な修正、オプションの修正、カスタムの修正の迅速な配信をサポートします。 これらのパッケージを使用して、Commerceで使用可能な個々のパッチに関する一般情報を適用、元に戻し、表示できます。

>[!TIP]
>
>を使用できます [品質向上パッチツール](https://experienceleague.adobe.com/tools/commerce-quality-patches/index.html) およびMagento Open SourceプロジェクトとAdobe Commerce プロジェクトのスタンドアロンパッケージとしてのCommerceのクラウドパッチ クラウド以外のプロジェクトには、品質向上パッチツールを使用することをお勧めします。

変更をリモート環境にデプロイする場合、 `ece-tools` パッケージで使用 `magento/magento-cloud-patches` および `magento/quality-patches` 保留中のパッチをチェックし、次の順序で自動的に適用するには、次の手順に従います。

1. 「Commerce用クラウドパッチ」パッケージに含まれる、必要なすべてのCommerce パッチを適用します。
1. Quality Patches Tool に含まれているオプションのCommerce パッチを適用します。
1. でのカスタムパッチの適用 `/m2-hotfixes` パッチ名のアルファベット順のディレクトリ。

>[!NOTE]
>
>を更新したとき `ece-tools` Commerce用のパッケージまたはクラウド修正プログラム パッケージ、必要な最新の修正プログラムは、次回プロジェクトをデプロイする際に適用されます。または、 `ece-patches apply` CLI コマンドとクラウド環境の再デプロイ。 スキップすることはできません [必要なパッチ](https://github.com/magento/magento-cloud-patches/tree/develop/patches) デプロイメントプロセス中に実行されます。

## 前提条件

{{upgrade-tip}}

Quality Patches Tool は、Commerce用 Cloud Patches および `ece-tools` パッケージ。 最新のパッチを適用するには、次の条件を満たす必要があります [ece-Tools の最新版](../dev-tools/update-package.md) インストールされています。 ECE-Tools の最低限必要なバージョンは 2002.1.2 です。

## 使用可能なパッチとステータスの表示

使用可能な個別パッチのリストを表示するには：

```bash
php ./vendor/bin/ece-patches status
```

応答の例：

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

ステータステーブルには、以下のタイプの情報が表示されます。

- **タイプ**:
   - `Optional` – 品質向上パッチツールおよびクラウドパッチパッケージのすべてのパッチは、Adobe CommerceおよびMagento Open Sourceのインストールではオプションです。 クラウドインフラストラクチャー上のAdobe Commerceの場合、すべてのパッチはオプションです。
   - `Required`—Cloud Patches for Commerce パッケージのすべてのパッチが、Cloud のお客様に必要です。
   - `Deprecated` – 個々のパッチは非推奨としてマークされます。適用した場合は元に戻すことをお勧めします。 非推奨パッチを元に戻すと、そのパッチはステータステーブルに表示されなくなります。
   - `Custom` – 「m2-hotfixes」ディレクトリのすべてのパッチ。

- **ステータス**:
   - `Applied`— パッチが適用されました。
   - `Not applied`— パッチが適用されていません。
   - `N/A` – 競合が原因でパッチのステータスを定義できません。

- **詳細**:
   - `Affected components` – 影響を受けるモジュールのリスト。
   - `Required patches` – 必要なパッチ （依存関係）のリスト。
   - `Recommended replacement` – 非推奨のパッチの代わりとして推奨されるパッチ。

## ローカル環境でのパッチの適用

ローカル環境でパッチを手動で適用し、デプロイ前にテストできます。

**ローカル開発環境で個別のパッチを適用するには**:

1. 「QUALITY_variables」PATCHをに追加します。 `.magento.env.yaml` ファイルを開き、その下に必要なパッチをリストします。

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

   この `ece-patches apply` コマンドは、次の順序でパッチを適用します。
   - 必要なパッチ
   - 個別のパッチ（オプション）
   - からのカスタムパッチ `/m2-hotfixes` directory

1. キャッシュをクリアします。

   ```bash
   php ./bin/magento cache:clean
   ```

1. パッチをテストし、カスタムパッチに必要な変更を加えます。

## リモート環境へのパッチの適用

>[!WARNING]
>
>実稼動環境にデプロイする前に、統合環境またはステージング環境ですべてのパッチをテストすることを強くお勧めします。

**リモート環境でパッチを適用するには**:

1. を追加 `QUALITY_PATCHES` 変数を `.magento.env.yaml` ファイルを開き、その下に必要なパッチをリストします。

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

1. 更新されたを追加、コミット、プッシュします。 `.magento.env.yaml` ファイル。

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

をデプロイすると、ECE-Tools はすべてのAdobeパッチと、ユーザーが追加したすべてのカスタムパッチをに適用します `/m2-hotfixes` プロジェクトルートのディレクトリ。

>[!NOTE]
>
>すべてのパッチファイル名はで終わる必要があります。 `.patch` 拡張機能。

**クラウド環境にカスタムパッチを適用してテストするには：**:

1. プロジェクトルートにというディレクトリを作成します。 `m2-hotfixes` 存在しない場合

   ```bash
   mkdir m2-hotfixes
   ```

1. パッチファイルをにコピーします `/m2-hotfixes` ディレクトリ。

1. コードの変更を追加、コミットおよびプッシュします。

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
   >必ず、実稼動前の環境ですべてのパッチをテストしてください。 クラウドインフラストラクチャー上のAdobe Commerceの場合、を使用して分岐を作成できます `magento-cloud environment:branch <branch-name>` CLI コマンド。

## カスタムパッチを元に戻す

以前に適用したカスタムパッチを元に戻すかアンインストールするには：

1. からパッチファイルを削除 `/m2-hotfixes` ディレクトリ。

1. コードの変更を追加、コミットおよびプッシュします。

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
   >必ず実稼動前の環境でテストしてください。 クラウドインフラストラクチャー上のAdobe Commerceの場合、を使用して分岐を作成できます `magento-cloud environment:branch <branch-name>` CLI コマンド。

## クラウド以外のプロジェクトへのパッチの適用

の使用 [品質向上パッチツール](https://github.com/magento/quality-patches) Magento Open SourceおよびAdobe Commerce プロジェクトの場合。

## ローカル環境でのパッチの復帰

ローカル開発環境で以前に適用したすべてのパッチを元に戻すには、 `ece-patches` CLI。

適用したすべてのパッチを元に戻すには、次の手順に従います。

```bash
php ./vendor/bin/ece-patches revert
```

このコマンドは、すべてのパッチを次の順序に戻します。

- 適用されたすべてのカスタム パッチを/m2-hotfixes ディレクトリから元に戻します。
- 適用したすべてのオプションの個別パッチを元に戻します。
- 適用されたすべての必要なパッチを元に戻します。

## ログ

品質向上パッチツールにより、すべての操作がに記録されます。 `<Project_root>/var/log/patch.log` ファイル。
