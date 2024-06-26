---
title: コンポーネント障害からのリカバリ
description: コンポーネントがクラウドインフラストラクチャ上のAdobe Commerceに適切にデプロイされない場合の復元方法を説明します。
feature: Cloud, Deploy
exl-id: 4855be0c-6883-4ab1-a364-316d10e97250
source-git-commit: b44d97f82ef09288807c648010202422c9ac04eb
workflow-type: tm+mt
source-wordcount: '220'
ht-degree: 0%

---

# コンポーネント障害からのリカバリ

このトピックでは、コンポーネントの適切なデプロイに失敗した場合の回復方法について説明します。 一般的な例としては、互換性のない PHP バージョンなど、リモート環境で満たされない依存関係を持つコンポーネントが挙げられます。

デプロイメントの失敗から回復するには、次のいずれかの方法があります。

- [バックアップの復元](../storage/snapshots.md#restore-a-snapshot)
- 以前の変更からプロジェクトとコードを削除し、再デプロイします

## クリーンアップ、削除、および再デプロイ

以前のデプロイメントからクリーンアップするには、追加または更新されたコンポーネントを特定して削除します。 まず、リモート環境にログインし、の内容を手動で消去します `var` ディレクトリ。 次に、 `composer.json` 環境をファイル化して再デプロイします。

**をクリーニングするには `var` ディレクトリ**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. SSH を使用してリモート環境にログインします。

   ```bash
   magento-cloud ssh
   ```

1. をクリア `var` ディレクトリ。

   ```shell
   rm -rf var/*
   ```

1. ログアウトします。

**コンポーネントを削除するには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. キャッシュをクリアします。

   ```bash
   composer clear-cache
   ```

1. からコンポーネントを削除 `composer.json` ファイル。

   ```bash
   composer remove <component-name>:<version>
   ```

   次のメッセージが表示された場合は、それ以上何もする必要はありません。

   ```terminal
   Package "<name>:<version>" listed for update is not installed. Ignoring.
   ```

1. 依存関係を更新しています。しばらくお待ちください。

1. コードの変更を追加、コミットおよびプッシュします。

   ```bash
   git add -A
   ```

   ```bash
   git commit -m "<message>"
   ```

   ```bash
   git push origin <environment-ID>
   ```

{{redeploy-warning}}

バックアップを使用しない環境の復元の詳細については、を参照してください。 [環境の復元](../development/restore-environment.md).

{{stuck-deployment-tip}}
