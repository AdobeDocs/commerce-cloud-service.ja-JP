---
title: ECE-Tools パッケージを更新します。
description: ECE-Tools パッケージをアップグレードして、クラウドインフラストラクチャ上のAdobe Commerceに適用される最新の修正および機能を活用する方法を説明します。
feature: Cloud, Upgrade
exl-id: 7cce45eb-ae53-4468-b16d-4f4d3422ac52
source-git-commit: 513bc5b52f046ffd98005d80f34725b7f60b38bd
workflow-type: tm+mt
source-wordcount: '170'
ht-degree: 0%

---

# ECE-Tools パッケージを更新します。

の更新 `ece-tools` パッケージは他の [コマースパッケージ用クラウドツールスイート](../release-notes/cloud-tools-suite.md)は、次の依存関係を持ちます： `ece-tools`. したがって、をサポートするクラウドインフラストラクチャ上でAdobe Commerceのバージョンを使用する必要があります。 `ece-tools` パッケージ。

{{ece-tools-package}}

**前提条件**:

- 更新する前に `ece-tools`、レビュー [Cloud Tools Suite for Commerce リリースノート](../release-notes/cloud-tools-suite.md).
- 次の場所から更新する場合： `ece-tools` 2002.0.22 以前から 2002.1.0 に対するレビュー [後方互換性のない変更](../release-notes/backward-incompatible-changes.md) Adobe Commerce on cloud infrastructure プロジェクトに必要な変更を加えます。
- レビュー [アップグレードとパッチ](../development/commerce-version.md#upgrade-from-older-versions) を使用して、Adobe Commerce on cloud infrastructure プロジェクトと互換性のある ECE-Tools のバージョンを決定します。

{{upgrade-tip}}

**次の手順で `ece-tools` パッケージ**:

1. ローカルワークステーションで、Composer を使用して更新を実行します。

   ```bash
   composer update magento/ece-tools --with-dependencies
   ```

   >[!NOTE]
   >
   >を超えてを更新できない場合 `ece-tools` バージョン 2002.0.8( [ECE-Tools パッケージを使用するには、プロジェクトをアップグレードします](install-package.md).

1. コードの変更を追加、コミット、およびプッシュします。

   ```bash
   git add -A
   ```

   ```bash
   git commit -m "Update magento/ece-tools"
   ```

   ```bash
   git push origin <branch-name>
   ```

1. テストの検証後、このブランチを統合ブランチに結合します。
