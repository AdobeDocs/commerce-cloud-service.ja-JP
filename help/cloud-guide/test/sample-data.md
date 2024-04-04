---
title: サンプルデータ
description: クラウドインフラストラクチャ上にAdobe Commerceを使用してサンプルデータをインストールする方法を説明します。
exl-id: 517e549f-15ba-47b3-9236-a1c4d24c917d
source-git-commit: 8a0523f1714b6ea41887e99b5c31294cf5e5255e
workflow-type: tm+mt
source-wordcount: '205'
ht-degree: 0%

---

# サンプルデータ

ストアの開発時にサンプルデータが必要な場合は、サンプルデータをインストールできます。 このデータは、顧客、製品、その他のデータを含むアクティブなAdobe Commerceストアをシミュレートします。 このサンプルデータは、新しいAdobe Commerce on クラウドインフラストラクチャテンプレートのインストールで最も適しています。

ベストプラクティスとして、開発環境と統合環境にサンプルデータをインストールします。 ステージングまたは実稼動環境でサンプルデータを使用する場合は、 [削除](#reset-or-uninstall-sample-data) 運用を開始する前の情報と製品。

## サンプルデータをインストール

サンプルデータをインストールするには：

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. ルートに、次のコマンドを入力してサンプルデータを追加します。

   ```bash
   ./bin/magento sampledata:deploy
   ```

1. コンポーネントが更新されるまで待ちます。

1. 変更をコミットしてプッシュします。

   ```bash
   git add -A && git commit -m "Install sample data"
   ```

   ```bash
   git push origin <branch-name>
   ```

1. プロジェクトがデプロイされるまで待ちます。

1. 統合環境のストアフロントページに移動して、インストールが成功したことを確認します。 ストアフロントへの URL リンクは、 [!DNL Cloud Console].

1. 環境のスナップショットを作成します。

   ```bash
   magento-cloud snapshot:create -e <environment-ID>
   ```

ライブデータを使用して開発のテストを開始できます。

## サンプルデータのリセットまたはアンインストール

サンプルデータのインストールと同じ手順に従って、サンプルデータをリセットまたは削除できます。

- サンプルデータの削除： `./bin/magento sampledata:remove`
- サンプルデータをリセット： `./bin/magento sampledata:reset`
