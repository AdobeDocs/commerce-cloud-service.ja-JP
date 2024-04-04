---
title: コンポーネントの障害からの回復
description: クラウドインフラストラクチャ上のAdobe Commerceでコンポーネントを適切にデプロイできなかった場合に回復する方法を説明します。
feature: Cloud, Deploy
exl-id: 4855be0c-6883-4ab1-a364-316d10e97250
source-git-commit: b44d97f82ef09288807c648010202422c9ac04eb
workflow-type: tm+mt
source-wordcount: '220'
ht-degree: 0%

---

# コンポーネントの障害からの回復

このトピックでは、コンポーネントが正しくデプロイされなかった場合の回復方法について説明します。 一般的な例としては、互換性のない PHP バージョンなど、リモート環境で満たされない依存関係を持つコンポーネントが含まれます。

失敗したデプロイメントからは、次のいずれかの方法で復元できます。

- [バックアップの復元](../storage/snapshots.md#restore-a-snapshot)
- 以前の変更からプロジェクトとコードを消去し、再デプロイします。

## クリーンアップ、削除、再デプロイ

以前のデプロイメントからクリーンアップするには、追加または更新されたコンポーネントを特定し、削除します。 まず、リモート環境にログインし、次の内容を手動でクリアします。 `var` ディレクトリ。 次に、 `composer.json` ファイルを作成し、環境を再デプロイします。

**をクリーニングするには `var` ディレクトリ**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. SSH を使用してリモート環境にログインします。

   ```bash
   magento-cloud ssh
   ```

1. 次をクリア： `var` ディレクトリ。

   ```shell
   rm -rf var/*
   ```

1. ログアウトします。

**コンポーネントを削除するには**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. キャッシュをクリアします。

   ```bash
   composer clear-cache
   ```

1. コンポーネントを `composer.json` ファイル。

   ```bash
   composer remove <component-name>:<version>
   ```

   次のメッセージが表示された場合は、これ以上の操作は必要ありません。

   ```terminal
   Package "<name>:<version>" listed for update is not installed. Ignoring.
   ```

1. 依存関係が更新されるまで待ちます。

1. コードの変更を追加、コミット、およびプッシュします。

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

のバックアップなしでの環境の復元の詳細を確認する [環境の復元](../development/restore-environment.md).

{{stuck-deployment-tip}}
