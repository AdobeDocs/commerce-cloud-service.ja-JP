---
title: ECE-Tools パッケージの更新
description: ECE-Tools パッケージをアップグレードして、クラウドインフラストラクチャ上のAdobe Commerceに適用された最新の修正と機能を活用する方法について説明します。
feature: Cloud, Upgrade
exl-id: 7cce45eb-ae53-4468-b16d-4f4d3422ac52
source-git-commit: 513bc5b52f046ffd98005d80f34725b7f60b38bd
workflow-type: tm+mt
source-wordcount: '170'
ht-degree: 0%

---

# ECE-Tools パッケージの更新

の更新 `ece-tools` パッケージは他も更新します [Commerce パッケージ用 Cloud Tools スイート](../release-notes/cloud-tools-suite.md)。これは、の依存関係です。 `ece-tools`. そのため、をサポートするバージョンのAdobe Commerce on cloud infrastructure を使用する必要があります `ece-tools` パッケージ。

{{ece-tools-package}}

**前提条件**:

- 更新する前に `ece-tools`を確認します [Commerce用 Cloud Tools スイートのリリースノート](../release-notes/cloud-tools-suite.md).
- から更新する場合 `ece-tools` 2002.0.22 以前から 2002.1.0 の場合は、を確認してください。 [後方互換性のない変更](../release-notes/backward-incompatible-changes.md) さらに、Adobe Commerce on cloud infrastructure プロジェクトに必要な変更を加えます。
- レビュー [アップグレードとパッチ](../development/commerce-version.md#upgrade-from-older-versions) クラウドインフラストラクチャプロジェクト上のAdobe Commerceと互換性のある ECE ツールのバージョンを判断する場合。

{{upgrade-tip}}

**を更新するには `ece-tools` package**:

1. ローカル ワークステーションで、Composer を使用して更新を実行します。

   ```bash
   composer update magento/ece-tools --with-dependencies
   ```

   >[!NOTE]
   >
   >以降は更新できない場合 `ece-tools` バージョン 2002.0.8 については、を参照してください。 [ECE-Tools パッケージを使用するようにプロジェクトをアップグレード](install-package.md).

1. コードの変更を追加、コミットおよびプッシュします。

   ```bash
   git add -A
   ```

   ```bash
   git commit -m "Update magento/ece-tools"
   ```

   ```bash
   git push origin <branch-name>
   ```

1. テスト検証後、このブランチを統合ブランチに結合します。
